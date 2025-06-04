# Start Exam Attempt Endpoint

## Overview

The `/objective_test_start_exam_attempt` endpoint initiates a new exam attempt for a user on a previously generated exam. It enforces a 3-attempt limit per generated exam and returns the exam questions if the attempt is allowed.

**Endpoint**: `GET /api:Y2coVQCE/objective_test_start_exam_attempt`  
**Authentication**: Required (user_student)  
**API Group**: `03_cima_objective_test_exams`

## Purpose

This endpoint:
- Validates that the user hasn't exceeded the 3-attempt limit for the specific generated exam
- Creates a new exam attempt record with tracking information
- Returns all questions for the exam in the correct display order
- Initializes timing and scoring fields for later completion

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `user_id` | integer | No | The ID of the user starting the attempt (references user_student table) |
| `generated_exam_id` | integer | No | The ID of the previously generated exam (references generated_exam table) |

## Response Structure

### Success Response (200 OK)

When the user can start a new attempt:

```json
{
  "attempt_id": 456,
  "questions": [
    {
      "id": 789,
      "question_text": "What is the primary purpose of cost accounting?",
      "question_type": "multiple_choice",
      "options": ["Option A", "Option B", "Option C", "Option D"],
      "correct_answer": "Option B",
      "section_id": 12,
      "module_id": 34,
      "exam_display_order": 1,
      "marks": 1
    },
    // ... more questions
  ],
  "total_questions": 60,
  "start_time": "2025-06-04 14:30:00"
}
```

### Error Response - Attempt Limit Reached

When the user has already completed 3 attempts:

```json
{
  "success": false,
  "message": "Sorry, you have reached the maximum amount of attempts permitted. Please generate a new exam to carry on your testing.",
  "attempts_completed": 3,
  "max_attempts_allowed": 3,
  "can_generate_new": true
}
```

## Business Logic

### Attempt Limit Validation

1. **Query Completed Attempts**: The endpoint counts all "completed" attempts for the user and generated exam combination
2. **Calculate Next Attempt Number**: Increments the count of completed attempts by 1
3. **Enforce Limit**: If completed attempts >= 3, the request is rejected with an appropriate error message

### Attempt Creation

If validation passes, the endpoint:

1. **Retrieves Exam Questions**: Queries all questions for the generated exam, sorted by `exam_display_order`
2. **Counts Questions**: Calculates the total number of questions
3. **Creates Attempt Record** with:
   - `status`: "started"
   - `start_time`: Current timestamp
   - `attempt_number`: Calculated sequential number
   - `total_questions`: Count of questions
   - `passing_threshold`: 70 (default)
   - Empty fields for scores and completion data

## Database Operations

### Queries
- `user_exam_attempt`: Count completed attempts (with aggregation)
- `generated_exam_questions`: Retrieve all questions for the exam (ordered)

### Inserts
- `user_exam_attempt`: Create new attempt record

## Implementation Details

### Lambda Functions

1. **Next Attempt Number Calculator**:
   ```javascript
   const currentCount = completed_attempts_count.attempt_count || 0;
   const nextNumber = currentCount + 1;
   return nextNumber;
   ```

2. **Attempt Limit Checker**:
   ```javascript
   const completedAttempts = completed_attempts_count.attempt_count || 0;
   if (completedAttempts >= 3) {
     return {
       canAttempt: false,
       error: "Sorry, you have reached the maximum amount of attempts permitted..."
     };
   }
   return { canAttempt: true };
   ```

### Conditional Logic

The endpoint uses conditional branching:
- **If `canAttempt` is true**: Proceeds with attempt creation
- **If `canAttempt` is false**: Returns error response immediately

## Usage Examples

### Request
```http
GET /api:Y2coVQCE/objective_test_start_exam_attempt?user_id=123&generated_exam_id=456
```

### cURL Example
```bash
curl -X GET "https://xj81-ukxc-jagn.e2.xano.io/api:Y2coVQCE/objective_test_start_exam_attempt?user_id=123&generated_exam_id=456" \
  -H "Authorization: Bearer YOUR_AUTH_TOKEN"
```

### JavaScript/Fetch Example
```javascript
const startExamAttempt = async (userId, generatedExamId, authToken) => {
  const response = await fetch(
    `https://xj81-ukxc-jagn.e2.xano.io/api:Y2coVQCE/objective_test_start_exam_attempt?user_id=${userId}&generated_exam_id=${generatedExamId}`,
    {
      method: 'GET',
      headers: {
        'Authorization': `Bearer ${authToken}`,
        'Content-Type': 'application/json'
      }
    }
  );
  
  const data = await response.json();
  
  if (data.success === false) {
    // Handle attempt limit reached
    console.error(data.message);
    return null;
  }
  
  return data;
};
```

## State Management

### Attempt States
- **started**: Initial state when attempt is created
- **in_progress**: Can be set when user actively answers questions
- **completed**: Set when user submits the exam

### Fields Populated Later
These fields are initialized as empty and populated when the exam is completed:
- `finish_time`
- `total_score`
- `percentage_score`
- `pass_status`
- `total_time_seconds`
- `correct_answers`

## Related Endpoints

- `/objective_test_generate_exam_optimised` - Generate a new exam
- `/object_test_complete_attempt` - Submit answers and complete the attempt
- `/get_attempt_review` - Review completed attempt results
- `/get_exam_questions` - Get questions without starting an attempt

## Error Handling

### Common Errors
1. **Attempt Limit Reached**: User has completed 3 attempts
2. **Invalid IDs**: User or exam doesn't exist
3. **Authentication Failed**: Invalid or missing auth token

### Client Recommendations
- Check `success` field in response
- If limit reached, prompt user to generate a new exam
- Cache attempt_id for subsequent API calls
- Start a client-side timer using the `start_time`

## Security Considerations

- Endpoint requires authentication (`auth = "user_student"`)
- User can only start attempts for their own user_id
- Consider adding validation to ensure generated_exam belongs to the authenticated user
- Attempt records are immutable once created

## Performance Notes

- Queries use indexed fields for efficient lookups
- Lambda functions have 10-second timeout
- Question retrieval is optimized with single query and sorting
- No caching implemented; consider for high-traffic scenarios