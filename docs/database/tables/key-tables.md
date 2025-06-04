# Key Tables Reference

## Most Important Tables for Development

### 1. User Authentication & Management

#### user_student (ID: 52) 🔐
**Purpose**: Primary student authentication table  
**Key Fields**:
- `id` - Primary key
- `email_address` - Unique login identifier
- `password` - Encrypted password storage
- `first_name` - Student name
- `member_id` - Memberstack integration ID

**Business Rules**:
- Authentication enabled table
- Email must be unique
- Integrates with Memberstack for subscriptions

#### users_admin (ID: 84)
**Purpose**: Administrative user management  
**Key Fields**:
- `name` - Admin name
- `role` - enum: `admin`, `tutor`

### 2. Academic Content Structure

#### subject (ID: 57) 📚
**Purpose**: Core academic subjects (CIMA/ACCA)  
**Key Fields**:
- `name` - Subject name
- `qualification` - enum: `CIMA`, `ACCA`
- `level` - enum: `Operational Level`, `Management Level`, `Strategic Level`
- `short_code` - Subject abbreviation
- `memberstack_id` - Subscription integration
- `webflow_cms_slug` - Content management integration

#### subject_section (ID: 68)
**Purpose**: Subject sections with exam weighting  
**Key Fields**:
- `subject_id` - Parent subject
- `name` - Section name
- `weighting_percentage` - Exam distribution weight
- `section_type` - Content classification
- `display_order` - UI ordering

#### subject_module (ID: 69)
**Purpose**: Learning modules within sections  
**Key Fields**:
- `subject_id` - Parent subject
- `subject_section_id` - Parent section
- `module_name` - Module title
- `display_order` - Ordering within section

### 3. Exam Generation System

#### exam_blueprint (ID: 76) 📋
**Purpose**: Exam templates and specifications  
**Key Fields**:
- `subject_id` - Associated subject
- `blueprint_name` - Template name
- `exam_type` - enum: `cima_objective_test`, `cima_case_study`, `acca`
- `total_questions` - Question count
- `time_limit_minutes` - Exam duration
- `passing_score_percentage` - Pass threshold

**Business Rules**:
- Defines exam structure and rules
- Used to generate user-specific exams

#### generated_exam (ID: 78) 🎯
**Purpose**: User-specific exam instances  
**Key Fields**:
- `user_id` - Student taking exam
- `exam_blueprint_id` - Template used
- `subject_id` - Subject being tested
- `user_version_number` - Version for this user
- `exam_name` - Display name

**Business Rules**:
- Created when user requests new exam
- Multiple versions allowed per user
- Links to attempt tracking

### 4. User Attempt Tracking

#### user_exam_attempt (ID: 80) ⏱️
**Purpose**: Individual exam attempts with 3-attempt limit  
**Key Fields**:
- `user_id` - Student attempting
- `generated_exam_id` - Specific exam instance
- `attempt_number` - Sequential attempt (1-3)
- `status` - enum: `started`, `in_progress`, `completed`
- `start_time` - When attempt began
- `finish_time` - When completed
- `total_score` - Final score
- `percentage_score` - Score as percentage
- `pass_status` - Boolean pass/fail

**Business Rules**:
- Maximum 3 completed attempts per generated exam
- Status progression: started → in_progress → completed
- Timing tracked for analytics

#### user_attempt_answers (ID: 82)
**Purpose**: Individual question responses  
**Key Fields**:
- `user_exam_attempt_id` - Parent attempt
- `exam_question_id` - Question answered
- `selected_answers` - JSON response data
- `is_correct` - Boolean correctness

### 5. Question Bank

#### exam_question (ID: 62) ❓
**Purpose**: Question repository with multiple types  
**Key Fields**:
- `question_name` - Question title
- `question_type` - 11 supported types (single_answer, multiple_answer, etc.)
- `question_data` - JSON question content
- `explanation` - Answer explanation
- `exam_blueprint_id` - Associated blueprint
- `subject_module_id` - Source module
- `points` - Question value

**Question Types**:
- `single_answer` - Multiple choice (one correct)
- `multiple_answer` - Multiple choice (multiple correct)
- `true_false` - Boolean questions
- `numerical` - Numeric input
- `essay` - Long-form text
- And 6 more types...

### 6. Case Study System

#### case_study_exam_blue_print (ID: 85) 📝
**Purpose**: Case study exam templates  
**Key Fields**:
- `name` - Case study name
- `subject_id` - Associated subject
- `section_count` - Number of sections
- `total_marks` - Maximum possible score
- `pass_marks` - Passing threshold
- `instructions` - JSON exam instructions

#### case_study_user_attempt (ID: 93)
**Purpose**: Case study attempt tracking  
**Key Fields**:
- `user_id` - Student attempting
- `case_study_exam_instance_id` - Specific instance
- `status` - enum: `started`, `in_progress`, `submitted`, `marked`
- `current_section` - Progress tracking
- `total_score` - Final score
- `pass_fail_status` - enum: Pass/Fail/Pending

### 7. Subscription Management

#### ms_plan (ID: 74) 💳
**Purpose**: Memberstack subscription plans  
**Key Fields**:
- `plan_id` - Memberstack plan identifier
- `plan_name` - Display name
- `plan_description` - Plan details
- `plan_price` - Cost information

#### ms_plan_subject (ID: 75)
**Purpose**: Subject access by subscription plan  
**Key Fields**:
- `ms_plan_id` - Subscription plan
- `subject` - Accessible subject

**Business Rules**:
- Controls which subjects users can access
- Based on their Memberstack subscription level

## Development Quick Reference

### Common Query Patterns

#### Get User's Available Subjects
```sql
SELECT s.* FROM subject s
JOIN ms_plan_subject mps ON s.id = mps.subject
JOIN ms_plan mp ON mps.ms_plan_id = mp.id
WHERE mp.plan_id = 'user_plan_id'
```

#### Check Attempt Limit
```sql
SELECT COUNT(*) FROM user_exam_attempt 
WHERE user_id = ? AND generated_exam_id = ? AND status = 'completed'
-- Result must be < 3 to allow new attempt
```

#### Get Question Distribution
```sql
SELECT ss.*, COUNT(eq.id) as available_questions
FROM subject_section ss
JOIN subject_module sm ON ss.id = sm.subject_section_id
JOIN exam_question eq ON sm.id = eq.subject_module_id
WHERE ss.subject_id = ?
GROUP BY ss.id
```

### Key Business Logic

1. **Exam Generation**: Questions distributed by section weighting percentages
2. **Attempt Limits**: 3 attempts max per generated exam for objective tests
3. **Authentication**: user_student table has auth enabled
4. **Subscriptions**: Subject access controlled via ms_plan_subject
5. **Progress Tracking**: Detailed attempt and answer tracking for analytics

### Integration Points

- **Memberstack**: Authentication and subscription management
- **Webflow**: Content delivery via CMS slugs  
- **Bold**: Video content management
- **File Storage**: Case study exhibits and resources