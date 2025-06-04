# Viva LMS Database Documentation

This directory contains comprehensive documentation for the Viva Learning Management System database schema and structure.

## Overview

The Viva LMS database consists of **39 tables** organized into logical functional areas that support a comprehensive learning management system for CIMA and ACCA qualifications.

## Database Structure

### Core Functional Areas

1. **[User Management](#user-management)** - User accounts, authentication, and roles
2. **[Academic Structure](#academic-structure)** - Subjects, sections, modules, and content
3. **[Exam System](#exam-system)** - Traditional multiple-choice and objective test exams
4. **[Case Study System](#case-study-system)** - Complex case study examinations
5. **[User Progress](#user-progress)** - Attempt tracking, scoring, and results
6. **[Memberstack Integration](#memberstack-integration)** - Subscription and access control

### Database Statistics

- **Total Tables**: 39
- **Primary Exam Types**: CIMA Objective Tests, CIMA Case Studies, ACCA (planned)
- **Question Types**: 11 supported types (single/multiple choice, drag-drop, numerical, etc.)
- **User Attempt Limit**: 3 attempts per generated exam
- **Authentication**: Integrated with Memberstack for user management

## Quick Reference

### Key Tables by Category

#### User Management (4 tables)
- `user_student` - Main student accounts (auth enabled)
- `users_admin` - Administrative users
- `person` - Extended person information
- `ms_plan` & `ms_plan_subject` - Memberstack plan integration

#### Academic Structure (6 tables)
- `subject` - Core academic subjects
- `subject_section` - Subject sections with weighting
- `subject_module` - Learning modules within sections
- `subject_lessons` - Individual lessons and videos
- `qualification` & `level` - Academic hierarchy

#### Exam System (9 tables)
- `exam_blueprint` - Exam templates and specifications
- `generated_exam` - User-specific exam instances
- `exam_question` - Question bank
- `user_exam_attempt` - Attempt tracking with 3-attempt limit

#### Case Study System (10 tables)
- `case_study_exam_blue_print` - Case study exam templates
- `case_study_user_attempt` - Student case study attempts
- `case_study_task` - Individual case study tasks
- `case_study_exhibits` - Supporting materials and resources

## Table Relationships

### Primary Relationships

```
subject (1) → (many) subject_section
subject_section (1) → (many) subject_module
subject_module (1) → (many) subject_lessons

exam_blueprint (1) → (many) generated_exam
generated_exam (1) → (many) user_exam_attempt
user_exam_attempt (1) → (many) user_attempt_answers

user_student (1) → (many) user_exam_attempt
user_student (1) → (many) case_study_user_attempt
```

### Authentication & Authorization

```
user_student ← user authentication table
ms_plan (1) → (many) ms_plan_subject → (many) subject
```

## Data Types and Patterns

### Standard Fields
- **Primary Key**: `id` (auto-incrementing integer)
- **Timestamps**: `created_at` (auto-now, private), `updated_at` (when modified)
- **Foreign Keys**: `{table}_id` with `tableref_id` specification

### Common Data Types
- **Text**: Names, descriptions, content (with trim validation)
- **JSON**: Complex data (question data, answers, scoring details)
- **Enum**: Status tracking, categories, types
- **Email**: Email addresses (auto-lowercase, trim validation)
- **Password**: Secure password storage (internal access)

### Business Rules

#### Attempt Limits
- **Objective Tests**: Maximum 3 attempts per generated exam
- **Case Studies**: No predefined limit (depends on exam window)

#### Scoring
- **Pass Threshold**: Typically 70% for objective tests
- **Case Studies**: Complex marking with major/minor points, deductions

#### Question Distribution
- Questions distributed based on section weighting percentages
- Even distribution across modules within sections
- Randomized question selection and ordering

## Documentation Files

### Detailed Documentation
- **[Tables](./tables/)** - Individual table schemas and descriptions
- **[Relationships](./relationships/)** - Foreign key relationships and dependencies
- **[Schemas](./schemas/)** - Full schema exports and data models

### Quick References
- **[Table List](./tables/table-list.md)** - Complete list of all tables with descriptions
- **[Entity Relationships](./relationships/erd.md)** - Entity relationship diagrams
- **[Data Dictionary](./schemas/data-dictionary.md)** - Field definitions and constraints

## Development Notes

### Security Considerations
- User authentication table (`user_student`) has auth enabled
- Password fields marked as internal access
- Email addresses auto-normalized (lowercase, trimmed)
- Created timestamps private to prevent manipulation

### Performance Considerations
- Foreign key relationships enable efficient joins
- Display order fields for consistent sorting
- JSON fields for flexible data structures
- Enum fields for controlled vocabularies

### Integration Points
- **Memberstack**: User authentication and subscription management
- **Webflow**: Content management integration via slugs
- **Bold**: Video content management
- **File Storage**: Asset management for case study exhibits

## Next Steps

1. Review individual table documentation in the `tables/` directory
2. Understand relationships using the `relationships/` documentation
3. Check schema exports in the `schemas/` directory for technical details
4. See API documentation in `../api/` for endpoint implementations

---

*Last Updated: June 2025*  
*Database Version: Production*