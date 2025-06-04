# Data Dictionary

## Field Types and Validation

### Standard Data Types

#### Text Fields
- **Purpose**: Names, descriptions, content
- **Validation**: `trim: true` (remove leading/trailing whitespace)
- **Access**: Typically `public`
- **Examples**: `name`, `description`, `question_name`

#### Email Fields
- **Purpose**: Email addresses
- **Validation**: `trim: true`, `lower: true` (auto-lowercase)
- **Access**: `public`
- **Examples**: `email_address`

#### Password Fields
- **Purpose**: Secure password storage
- **Validation**: Encrypted/hashed storage
- **Access**: `internal` (not exposed in API responses)
- **Examples**: `password`

#### Integer Fields
- **Purpose**: Numeric values, counts, scores
- **Validation**: Positive integers where applicable
- **Access**: `public`
- **Examples**: `id`, `total_questions`, `score`

#### Timestamp Fields
- **Purpose**: Date/time tracking
- **Validation**: Auto-generated where applicable
- **Access**: `created_at` typically `private`, others `public`
- **Examples**: `created_at`, `start_time`, `finish_time`

#### JSON Fields
- **Purpose**: Complex data structures
- **Validation**: Valid JSON format
- **Access**: `public`
- **Examples**: `question_data`, `answers`, `task_answers`

#### Boolean Fields
- **Purpose**: True/false flags
- **Validation**: Boolean values only
- **Access**: `public`
- **Examples**: `is_active`, `pass_status`

#### Enum Fields
- **Purpose**: Controlled vocabularies
- **Validation**: Must match predefined values
- **Access**: `public`
- **Examples**: `status`, `qualification`, `question_type`

### Common Field Patterns

#### Primary Keys
- **Name**: `id`
- **Type**: `int`
- **Properties**: Auto-incrementing, required, public
- **Usage**: Unique identifier for each record

#### Timestamps
- **Created**: `created_at` (timestamp, auto-now, private)
- **Updated**: `updated_at` (timestamp, manual update, public)
- **Started**: `start_time` (timestamp, nullable, public)
- **Finished**: `finish_time` (timestamp, nullable, public)

#### Foreign Keys
- **Pattern**: `{table_name}_id`
- **Type**: `int`
- **Properties**: References another table's primary key
- **Examples**: `user_id`, `subject_id`, `exam_blueprint_id`

#### Display Control
- **Ordering**: `display_order` (int, for UI sorting)
- **Status**: `status` (enum, for state tracking)
- **Active**: `is_active` (bool, for soft deletes)

## Enum Value Definitions

### User and Authentication
#### user_admin.role
- `admin` - Full system administration
- `tutor` - Content management and student oversight

### Academic Structure
#### subject.level
- `Operational Level` - Entry-level qualification content
- `Management Level` - Mid-level qualification content  
- `Strategic Level` - Advanced qualification content

#### subject.qualification
- `CIMA` - Chartered Institute of Management Accountants
- `ACCA` - Association of Chartered Certified Accountants

#### subject_section.section_type
- `Foundational` - Basic knowledge areas
- `Content Area` - Main subject content
- `Exam Window` - Time-specific exam content
- `Capability` - Skills-based content
- `Application Material` - Practical application content

#### subject_lessons.lesson_type
- `introduction` - Introductory lesson content
- `content_area` - Main learning content

### Exam System
#### exam_blueprint.exam_type
- `cima_objective_test` - CIMA multiple choice exams
- `cima_case_study` - CIMA case study exams
- `acca` - ACCA examinations

#### user_exam_attempt.status
- `started` - Attempt created, not yet in progress
- `in_progress` - User actively taking exam
- `completed` - Exam submitted and scored

### Question Types
#### exam_question.question_type
- `single_answer` - Single correct answer (radio buttons)
- `multiple_answer` - Multiple correct answers (checkboxes)
- `dropdown` - Single dropdown selection
- `multiple_dropdown` - Multiple dropdown selections
- `gapfill` - Fill in the blank questions
- `true_false` - True/false questions
- `drag_drop` - Drag and drop interactions
- `numerical` - Numeric answer input
- `essay` - Long-form text responses
- `case study analysis` - Complex case study questions
- `complex matching` - Advanced matching exercises

### Case Study System
#### case_study_user_attempt.status
- `started` - Case study attempt initiated
- `in_progress` - Student actively working
- `submitted` - Student has submitted answers
- `marked` - Marking completed

#### case_study_exhibits.asset_type
- `pre-seen` - Materials available before exam
- `image` - Image-based exhibits

## JSON Field Structures

### Question Data (exam_question.question_data)
```json
{
  "question_text": "What is the primary purpose...",
  "options": ["Option A", "Option B", "Option C", "Option D"],
  "correct_answer": "Option B",
  "explanation": "Option B is correct because...",
  "marks": 1
}
```

### User Answers (user_attempt_answers.selected_answers)
```json
{
  "selected": ["Option B"],
  "time_spent": 45,
  "flagged": false
}
```

### Case Study Task Answers (case_study_task.task_answers)
```json
{
  "sub_tasks": [
    {
      "id": 1,
      "answer_text": "Student response...",
      "marks_available": 10
    }
  ]
}
```

### Case Study User Scores (case_study_task_user_scores.major_points)
```json
{
  "points": [
    {
      "description": "Identified key issue",
      "marks": 3,
      "awarded": true
    }
  ]
}
```

## Validation Rules

### Text Field Constraints
- **Maximum Length**: Generally unlimited unless specified
- **Trim**: Leading/trailing whitespace removed
- **Required**: Specified per field (nullable: false + required: true)

### Numeric Constraints
- **Positive Integers**: IDs, counts, scores
- **Percentage Values**: 0-100 for weighting_percentage
- **Time Values**: Seconds for durations, minutes for limits

### Email Validation
- **Format**: Valid email format required
- **Case**: Auto-converted to lowercase
- **Uniqueness**: Enforced at application level

### Foreign Key Integrity
- **References**: Must point to existing records
- **Cascading**: Handled at application level
- **Null Values**: Allowed for optional relationships

## Access Control Patterns

### Field Access Levels
- **public**: Available in API responses
- **private**: Not included in API responses (e.g., created_at)
- **internal**: Completely hidden (e.g., passwords)

### Table-Level Authentication
- **user_student**: Authentication required (`auth: 52`)
- **Most others**: No authentication at table level
- **API Level**: Authentication handled per endpoint

### Business Logic Security
- **Attempt Limits**: Enforced in application logic
- **Time Windows**: Case study availability controlled
- **Subscription Access**: Subject access via Memberstack plans