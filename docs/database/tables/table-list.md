# Complete Table List

## User Management Tables

| Table | ID | Description | Key Features |
|-------|----|-----------|-----------| 
| `user_student` | 52 | Main student user accounts | Authentication enabled, Memberstack integration |
| `users_admin` | 84 | Administrative users | Admin/tutor roles |
| `person` | 53 | Extended person information | Links to user accounts |

## Memberstack Integration

| Table | ID | Description | Key Features |
|-------|----|-----------|-----------| 
| `ms_plan` | 74 | Memberstack subscription plans | Plan IDs, pricing |
| `ms_plan_subject` | 75 | Subject access by plan | Plan-subject relationships |

## Academic Structure

| Table | ID | Description | Key Features |
|-------|----|-----------|-----------| 
| `subject` | 57 | Core academic subjects | CIMA/ACCA, levels, Webflow integration |
| `subject_section` | 68 | Subject sections | Weighting percentages, display order |
| `subject_module` | 69 | Learning modules | Within sections, ordered content |
| `subject_lessons` | 70 | Individual lessons | Video content, Bold integration |
| `subject_documents` | 71 | Subject resources | Document management |
| `subject_exams` | 72 | Subject-exam associations | Links subjects to exams |
| `qualification` | 58 | Top-level qualifications | CIMA, ACCA |
| `level` | 59 | Qualification levels | Operational, Management, Strategic |

## Traditional Exam System

| Table | ID | Description | Key Features |
|-------|----|-----------|-----------| 
| `exam` | 54 | Exam definitions | Basic exam properties |
| `exam_blueprint` | 76 | Exam templates | Question counts, time limits, passing scores |
| `exam_blueprint_module_allocation` | 77 | Module allocations | Blueprint-module mappings |
| `exam_section` | 60 | Exam sections | Section organization |
| `exam_section_category` | 61 | Section categories | Categorized content |
| `exam_question` | 62 | Question bank | 11 question types, JSON data |
| `exam_questions_bridge` | 63 | Question-section links | Display ordering |
| `generated_exam` | 78 | User exam instances | Personalized exam generation |
| `generated_exam_questions` | 79 | Generated exam questions | Question selection and ordering |

## User Exam Attempts

| Table | ID | Description | Key Features |
|-------|----|-----------|-----------| 
| `user_exams` | 55 | Basic user-exam relationships | Simple tracking |
| `user_exam_attempt` | 80 | Detailed attempt tracking | 3-attempt limit, timing, scoring |
| `user_attempt_answers` | 82 | Multiple choice answers | JSON responses, correctness |
| `user_question_answers` | 81 | Question responses | Individual answer tracking |
| `user_question_response` | 65 | Text responses | Open-ended answers |
| `user_category_score` | 67 | Category performance | Score breakdowns |
| `user_section_score` | 66 | Section performance | Section-level results |

## Case Study System

| Table | ID | Description | Key Features |
|-------|----|-----------|-----------| 
| `case_study_exam_blue_print` | 85 | Case study exam templates | Complex exam structure |
| `case_study_section` | 86 | Case study sections | Time limits, JSON questions |
| `case_study_task` | 87 | Individual tasks | Sub-tasks, marking allocation |
| `case_study_exhibits` | 89 | Supporting materials | Pre-seen materials, images |
| `case_study_core_activities` | 92 | Activity definitions | Core business activities |
| `case_study_exam_window` | 90 | Exam scheduling | Release dates, exam periods |
| `case_study_exam_instance` | 95 | Active exam instances | Window-blueprint links |
| `case_study_user_attempt` | 93 | Student attempts | Status tracking, section progress |
| `case_study_user_attempt_answers` | 94 | Student responses | JSON answers, marking status |
| `case_study_task_user_scores` | 91 | Detailed scoring | Major/minor points, comments |

## Key Statistics

- **Total Tables**: 39
- **User Management**: 5 tables
- **Academic Structure**: 8 tables  
- **Traditional Exams**: 12 tables
- **Case Studies**: 10 tables
- **User Tracking**: 7 tables

## Table Naming Conventions

### Prefixes
- `user_` - User-related data and attempts
- `subject_` - Academic content structure
- `exam_` - Traditional exam system
- `case_study_` - Case study examination system
- `ms_` - Memberstack integration
- `generated_` - Runtime-generated content

### Common Suffixes
- `_attempt` - User attempt tracking
- `_answers` - User response storage
- `_score` - Performance measurement
- `_bridge` - Relationship tables
- `_blueprint` - Template/specification tables

## Field Patterns

### Standard Fields (All Tables)
- `id` - Auto-incrementing primary key
- `created_at` - Timestamp (auto-now, private)

### Common Foreign Keys
- `user_id` - References user_student(52)
- `subject_id` - References subject(57)
- `exam_blueprint_id` - References exam_blueprint(76)
- `generated_exam_id` - References generated_exam(78)

### Business Logic Fields
- `status` - Enum tracking (started/in_progress/completed)
- `display_order` - Ordering for UI presentation
- `weighting_percentage` - Score distribution
- `attempt_number` - Sequential attempt tracking