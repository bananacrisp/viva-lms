# Entity Relationship Diagram

## Core Relationships

### User Authentication Flow
```
user_student (auth enabled)
    ↓ (has many)
user_exam_attempt
    ↓ (has many)
user_attempt_answers

user_student
    ↓ (has many)
case_study_user_attempt
    ↓ (has many)
case_study_user_attempt_answers
```

### Academic Content Hierarchy
```
qualification (CIMA/ACCA)
    ↓ (has many)
level (Operational/Management/Strategic)
    ↓ (has many)
subject
    ↓ (has many)
subject_section (with weighting %)
    ↓ (has many)
subject_module
    ↓ (has many)
subject_lessons (video content)
```

### Exam Generation Process
```
subject
    ↓ (defines)
exam_blueprint (template)
    ↓ (generates)
generated_exam (user-specific)
    ↓ (contains)
generated_exam_questions
    ↓ (sourced from)
exam_question (question bank)
```

### Case Study Structure
```
case_study_exam_blue_print
    ↓ (has many)
case_study_section
    ↓ (has many)
case_study_task
    ↓ (references)
case_study_core_activities

case_study_exam_window
    ↓ (activates)
case_study_exam_instance
    ↓ (links to)
case_study_exam_blue_print
```

## Detailed Relationships

### User Management
- **user_student (52)** ← Primary authentication table
  - → user_exam_attempt (80) [1:many]
  - → case_study_user_attempt (93) [1:many]
  - → generated_exam (78) [1:many]

- **person (53)** ← Extended user information
  - → user_student (52) [many:1] via user_id

### Memberstack Integration
- **ms_plan (74)** ← Subscription plans
  - → ms_plan_subject (75) [1:many]
  - → subject (57) [many:many] via ms_plan_subject

### Academic Structure
- **subject (57)** ← Core academic subjects
  - → subject_section (68) [1:many]
  - → subject_module (69) [1:many]
  - → subject_lessons (70) [1:many]
  - → subject_documents (71) [1:many]
  - → subject_exams (72) [1:many]
  - → exam_blueprint (76) [1:many]

- **subject_section (68)** ← Subject sections
  - → subject (57) [many:1]
  - → subject_module (69) [1:many]
  - → exam_blueprint (76) [many:1]

- **subject_module (69)** ← Learning modules
  - → subject (57) [many:1]
  - → subject_section (68) [many:1]
  - → subject_lessons (70) [1:many]
  - → exam_question (62) [1:many]

### Traditional Exam System
- **exam_blueprint (76)** ← Exam templates
  - → subject (57) [many:1]
  - → generated_exam (78) [1:many]
  - → exam_question (62) [1:many]
  - → exam_blueprint_module_allocation (77) [1:many]

- **generated_exam (78)** ← User-specific exams
  - → user_student (52) [many:1]
  - → subject (57) [many:1]
  - → exam_blueprint (76) [many:1]
  - → generated_exam_questions (79) [1:many]
  - → user_exam_attempt (80) [1:many]

- **exam_question (62)** ← Question bank
  - → exam_blueprint (76) [many:1]
  - → subject_module (69) [many:1]
  - → generated_exam_questions (79) [1:many]

### User Attempts & Scoring
- **user_exam_attempt (80)** ← Attempt tracking
  - → user_student (52) [many:1]
  - → generated_exam (78) [many:1]
  - → user_attempt_answers (82) [1:many]
  - → user_category_score (67) [1:many]
  - → user_section_score (66) [1:many]

- **user_attempt_answers (82)** ← Individual responses
  - → user_exam_attempt (80) [many:1]
  - → exam_question (62) [many:1]

### Case Study System
- **case_study_exam_blue_print (85)** ← Case study templates
  - → subject (57) [many:1]
  - → case_study_section (86) [1:many]
  - → case_study_exam_instance (95) [1:many]

- **case_study_section (86)** ← Case study sections
  - → case_study_exam_blue_print (85) [many:1]
  - → case_study_task (87) [1:many]

- **case_study_task (87)** ← Individual tasks
  - → case_study_section (86) [many:1]
  - → case_study_core_activities (92) [many:1]
  - → case_study_task_user_scores (91) [1:many]

- **case_study_user_attempt (93)** ← Student attempts
  - → user_student (52) [many:1]
  - → case_study_exam_instance (95) [many:1]
  - → case_study_user_attempt_answers (94) [1:many]

## Business Rules Enforced by Relationships

### Attempt Limits
- user_exam_attempt → generated_exam: Max 3 "completed" attempts per user
- case_study_user_attempt → instance: Controlled by exam window timing

### Question Distribution
- exam_blueprint → subject_section: Uses weighting_percentage for distribution
- subject_section → subject_module: Even distribution within sections
- subject_module → exam_question: Random selection from available pool

### Access Control
- user_student → ms_plan → subject: Subscription-based subject access
- case_study_exam_window → case_study_exam_instance: Time-based availability

### Content Hierarchy
- qualification → level → subject: Academic progression structure
- subject → section → module → lesson: Learning content organization

## Data Integrity Constraints

### Foreign Key Constraints
- All `{table}_id` fields reference the corresponding table's primary key
- Cascading deletes not typically used (soft deletes preferred)
- NULL values allowed for optional relationships

### Business Logic Constraints
- attempt_number: Sequential, starting from 1
- display_order: Positive integers for ordering
- weighting_percentage: Should sum to 100% within exam_blueprint
- status: Must follow valid state transitions

### Unique Constraints
- user_student.email_address: Unique across all students
- subject.slug: Unique URL-friendly identifiers
- generated_exam: Unique per user_id + exam_blueprint_id + user_version_number