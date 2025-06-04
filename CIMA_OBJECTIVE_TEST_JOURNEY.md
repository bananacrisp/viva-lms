# CIMA Objective Test - Complete Student Journey

## Student Experience Flow Analysis

This document maps out every step a student should experience for CIMA Objective Tests, from initial exam request through to comprehensive results analysis.

---

## 📚 STEP 1: Pre-Exam Setup

### Student Action: Browse Available Subjects & Request New Exam

#### Current Backend Status: ✅ FULLY SUPPORTED
**API Endpoints Available:**
- `GET /get_user_subjects` - Returns subjects based on Memberstack subscription
- `GET /subject/subject_id` - Get complete subject details

**What Happens:**
1. Student logs in via Memberstack authentication
2. System checks their subscription plan via `ms_plan_subject` table
3. Student sees available subjects based on subscription
4. Student selects subject and requests new exam generation

**Backend Implementation:**
- ✅ Subscription-based access control working
- ✅ Subject hierarchy fully implemented
- ✅ User authentication integrated

---

## 🎯 STEP 2: Exam Generation

### Student Action: Generate New Personalized Exam

#### Current Backend Status: ✅ FULLY IMPLEMENTED
**API Endpoint:**
- `GET /objective_test_generate_exam_optimised?user_id={id}&subject_id={id}`

**What Happens:**
1. System retrieves exam blueprint for the subject
2. Advanced algorithm distributes questions based on section weightings
3. Questions selected from question bank with randomization
4. New exam instance created in `generated_exam` table
5. Questions stored in `generated_exam_questions` table with display order

**Backend Implementation:**
- ✅ Intelligent question distribution algorithm
- ✅ Section weighting compliance
- ✅ Question randomization
- ✅ User-specific exam versioning

---

## ⏱️ STEP 3: Exam Attempt Initiation

### Student Action: Start Exam Attempt (with Validation)

#### Current Backend Status: ✅ FULLY IMPLEMENTED
**API Endpoint:**
- `GET /objective_test_start_exam_attempt?user_id={id}&generated_exam_id={id}`

**What Happens:**
1. System validates user hasn't exceeded 3-attempt limit
2. If valid, creates new attempt record in `user_exam_attempt`
3. Returns all questions in correct display order
4. Records start time for timing purposes

**Backend Implementation:**
- ✅ 3-attempt limit enforcement
- ✅ Attempt number tracking
- ✅ Question delivery with ordering
- ✅ Start time recording

---

## 📝 STEP 4: Exam Taking Experience

### Student Action: Answer Questions & Submit Exam

#### Current Backend Status: ⚠️ PARTIALLY MISSING

**Current API Endpoint:**
- `POST /object_test_complete_attempt` - Submit complete exam

**What's Missing - Individual Question Management:**

### 🔴 MISSING ENDPOINT 1: Save Individual Answers (Progressive Save)
**Needed:** `POST /objective_test_save_answer`
**Purpose:** Allow students to save answers as they go (not just final submission)
**Parameters:**
```json
{
  "attempt_id": 123,
  "question_id": 456,
  "answer_data": {...},
  "time_spent": 45,
  "flagged": false
}
```

### 🔴 MISSING ENDPOINT 2: Get Current Attempt State
**Needed:** `GET /objective_test_get_attempt_state/{attempt_id}`
**Purpose:** Resume exam if browser closed/refreshed
**Returns:**
```json
{
  "attempt_id": 123,
  "current_question": 15,
  "time_remaining": 3600,
  "answers_saved": [...],
  "flagged_questions": [...]
}
```

### 🔴 MISSING ENDPOINT 3: Flag Questions for Review
**Needed:** `POST /objective_test_flag_question`
**Purpose:** Mark questions for later review
**Parameters:**
```json
{
  "attempt_id": 123,
  "question_id": 456,
  "flagged": true
}
```

### 🔴 MISSING TABLE: question_attempt_progress
**Purpose:** Track individual question progress during exam
**Suggested Schema:**
```sql
question_attempt_progress:
- id (primary key)
- user_exam_attempt_id (foreign key)
- question_id (foreign key) 
- answer_data (JSON)
- time_spent (int - seconds)
- flagged (boolean)
- visited (boolean)
- answered (boolean)
- created_at
- updated_at
```

---

## ✅ STEP 5: Exam Submission & Scoring

### Student Action: Submit Final Exam

#### Current Backend Status: ✅ IMPLEMENTED
**API Endpoint:**
- `POST /object_test_complete_attempt`

**What Happens:**
1. Receives all final answers
2. Calculates score automatically
3. Updates attempt status to "completed"
4. Records finish time and total duration
5. Determines pass/fail status

**Backend Implementation:**
- ✅ Automatic scoring
- ✅ Pass/fail determination
- ✅ Time tracking completion
- ✅ Final answer storage

---

## 📊 STEP 6: Results Review & Analytics

### Student Action: View Detailed Results & Performance Analysis

#### Current Backend Status: ⚠️ BASIC IMPLEMENTATION, MISSING ADVANCED FEATURES

**Current API Endpoint:**
- `GET /get_attempt_review?attempt_id={id}`

**Current Features:**
- ✅ Basic attempt results
- ✅ Score and pass/fail status
- ✅ Some performance breakdown

### 🔴 MISSING ADVANCED ANALYTICS ENDPOINTS:

### Missing Endpoint 1: Detailed Section Performance
**Needed:** `GET /objective_test_section_analysis/{attempt_id}`
**Purpose:** Section-by-section performance breakdown
**Returns:**
```json
{
  "sections": [
    {
      "section_name": "Cost Accounting",
      "weighting_percentage": 30,
      "questions_attempted": 18,
      "questions_correct": 14,
      "section_score": 77.8,
      "time_spent": 1800,
      "strength_level": "strong"
    }
  ]
}
```

### Missing Endpoint 2: Question Type Analysis
**Needed:** `GET /objective_test_question_type_analysis/{attempt_id}`
**Purpose:** Performance by question type
**Returns:**
```json
{
  "question_types": [
    {
      "type": "multiple_choice",
      "total_questions": 45,
      "correct_answers": 38,
      "accuracy_rate": 84.4,
      "avg_time_per_question": 65
    }
  ]
}
```

### Missing Endpoint 3: Historical Progress
**Needed:** `GET /objective_test_progress_history/{user_id}/{subject_id}`
**Purpose:** Track improvement over multiple attempts
**Returns:**
```json
{
  "attempts": [
    {
      "attempt_number": 1,
      "date": "2025-06-01",
      "score": 65,
      "improvement_areas": ["Budgeting", "Variance Analysis"]
    }
  ],
  "overall_trend": "improving",
  "predicted_pass_probability": 78
}
```

---

## 🏠 STEP 7: Dashboard Integration

### Student Action: View Progress in Main Dashboard

#### Current Backend Status: 🔴 MISSING DASHBOARD APIS

### Missing Dashboard Endpoints:

### Missing Endpoint 1: Student Dashboard Summary
**Needed:** `GET /student_dashboard/{user_id}`
**Purpose:** Complete overview for student dashboard
**Returns:**
```json
{
  "recent_attempts": [...],
  "subjects_in_progress": [...],
  "upcoming_exam_recommendations": [...],
  "performance_trends": {...},
  "achievement_badges": [...]
}
```

### Missing Endpoint 2: Subject Progress Overview
**Needed:** `GET /subject_progress/{user_id}/{subject_id}`
**Purpose:** Detailed subject-level progress
**Returns:**
```json
{
  "subject_name": "Management Accounting",
  "completion_percentage": 75,
  "sections_mastered": 6,
  "sections_needs_work": 2,
  "best_score": 82,
  "latest_score": 78,
  "exam_readiness": "nearly_ready"
}
```

---

## 📋 SUMMARY: Missing Backend Components

### 🔴 Critical Missing APIs (7 endpoints)
1. `POST /objective_test_save_answer` - Progressive answer saving
2. `GET /objective_test_get_attempt_state/{attempt_id}` - Resume capability
3. `POST /objective_test_flag_question` - Question flagging
4. `GET /objective_test_section_analysis/{attempt_id}` - Section performance
5. `GET /objective_test_question_type_analysis/{attempt_id}` - Question type analysis
6. `GET /objective_test_progress_history/{user_id}/{subject_id}` - Historical progress
7. `GET /student_dashboard/{user_id}` - Dashboard summary

### 🔴 Missing Database Table
1. `question_attempt_progress` - Individual question state tracking

### 🔴 Enhanced Analytics Features
- Detailed section performance breakdown
- Question type analysis
- Historical progress tracking
- Predictive analytics (pass probability)
- Learning recommendations
- Achievement/badge system

---

## 🎯 Development Priority Recommendations

### Phase 1: Core Exam Experience (Essential)
1. **Progressive Answer Saving** - Students can't lose work
2. **Resume Capability** - Handle browser refresh/connectivity issues
3. **Question Flagging** - Essential for exam navigation

### Phase 2: Advanced Analytics (High Value)
4. **Section Performance Analysis** - Critical for learning improvement
5. **Question Type Analysis** - Helps identify skill gaps
6. **Dashboard Integration** - Student engagement and progress tracking

### Phase 3: Predictive Features (Nice to Have)
7. **Historical Progress Tracking** - Long-term learning insights
8. **Pass Probability Prediction** - Confidence building
9. **Achievement System** - Gamification for engagement

---

*This analysis is based on current Xano backend implementation and industry best practices for professional exam platforms.*