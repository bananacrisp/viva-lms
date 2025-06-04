# Generate Exam Optimised Endpoint

## Overview

The `/objective_test_generate_exam_optimised` endpoint is an optimized version of the exam generation system that creates a complete objective test exam instance with a properly structured question distribution across sections and modules.

**Endpoint**: `GET /api:Y2coVQCE/objective_test_generate_exam_optimised`  
**Authentication**: Not required  
**API Group**: `03_cima_objective_test_exams`

## Purpose

This endpoint generates a new exam instance for a specific subject, automatically:
- Retrieves the exam blueprint for the subject
- Calculates question distribution based on section weightings
- Selects questions from available question pools
- Creates a nested structure showing section/module/question type breakdown
- Saves the generated exam to the database

## Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `user_id` | integer | No | The ID of the user for whom the exam is being generated |
| `subject_id` | integer | No | The ID of the subject for which to generate the exam |

## Response Structure

### Success Response (200 OK)

```json
{
  "success": true,
  "message": "Optimized exam generated with nested structure",
  "new_exam": {
    "id": 123,
    "created_at": "2025-06-04 10:30:00",
    "user_id": 456,
    "exam_name": "Generated Exam",
    "exam_type": "objective_test",
    "subject_name": "Subject",
    "exam_blueprint_name": "CIMA P1 Blueprint",
    "user_version_number": 1,
    "date_generated": "2025-06-04 10:30:00",
    "exam_blueprint_id": 789,
    "subject_id": 12,
    "updated_at": "2025-06-04 10:30:00"
  },
  "nested_structure": [
    {
      "section_id": 1,
      "section_name": "Cost Accounting",
      "section_type": "core",
      "weighting_percentage": 30,
      "target_questions": 18,
      "modules": [
        {
          "module_id": 101,
          "module_name": "Cost Classification",
          "display_order": 1,
          "question_types": [
            {
              "question_type": "multiple_choice",
              "available": 25,
              "selected": 6
            },
            {
              "question_type": "true_false",
              "available": 15,
              "selected": 3
            }
          ],
          "total_selected": 9
        }
      ]
    }
  ],
  "total_questions": 60,
  "blueprint": {
    "id": 789,
    "blueprint_name": "CIMA P1 Blueprint",
    "exam_type": "objective_test",
    "total_questions": 60,
    "subject_id": 12
  }
}
```

### Error Response

If no exam blueprint is found for the subject:

```json
{
  "error": "No exam blueprint found for this subject"
}
```

## Implementation Details

### Question Distribution Algorithm

1. **Section Allocation**: 
   - Questions are distributed based on each section's `weighting_percentage`
   - The algorithm calculates exact counts and handles rounding to ensure the total matches the blueprint

2. **Module Distribution**:
   - Within each section, questions are evenly distributed among modules
   - Extra questions from rounding are distributed to modules sequentially

3. **Question Type Selection**:
   - Questions are selected from different types (multiple_choice, true_false, etc.)
   - The selection aims for even distribution across available question types

4. **Randomization**:
   - Selected questions are randomized before being assigned display order numbers
   - Each question receives an `exam_display_order` from 1 to total questions

### Database Operations

The endpoint performs the following database operations:

1. **Queries**:
   - `exam_blueprint`: Retrieves the blueprint for the subject
   - `subject_section`: Gets all sections for the subject (ordered by display_order)
   - `subject_module`: Gets all modules for the subject (ordered by display_order)
   - `exam_question`: Retrieves all questions associated with the blueprint

2. **Inserts**:
   - `generated_exam`: Creates a new exam record with metadata

### Performance Considerations

- The endpoint uses an optimized Lambda function for question selection logic
- Database queries are executed in parallel where possible
- The Lambda function has a 15-second timeout for complex calculations
- No caching is currently enabled, but could be implemented for frequently accessed blueprints

## Usage Example

### Request
```http
GET /api:Y2coVQCE/objective_test_generate_exam_optimised?user_id=456&subject_id=12
```

### cURL Example
```bash
curl -X GET "https://xj81-ukxc-jagn.e2.xano.io/api:Y2coVQCE/objective_test_generate_exam_optimised?user_id=456&subject_id=12"
```

### JavaScript/Fetch Example
```javascript
const generateExam = async (userId, subjectId) => {
  const response = await fetch(
    `https://xj81-ukxc-jagn.e2.xano.io/api:Y2coVQCE/objective_test_generate_exam_optimised?user_id=${userId}&subject_id=${subjectId}`,
    {
      method: 'GET',
      headers: {
        'Content-Type': 'application/json'
      }
    }
  );
  
  const data = await response.json();
  return data;
};
```

## Related Endpoints

- `/objective_test_generate_exam` - Original exam generation endpoint
- `/objective_test_start_exam_attempt` - Start an attempt for a generated exam
- `/get_exam_questions` - Retrieve questions for a specific generated exam

## Notes

- The endpoint does not require authentication, but typically `user_id` should be verified in production
- The generated exam is saved to the database and can be retrieved later
- Each user can have multiple generated exams for the same subject
- The `user_version_number` field can be used to track multiple attempts or versions