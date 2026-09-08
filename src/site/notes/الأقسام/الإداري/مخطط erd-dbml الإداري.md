---
{"dg-publish":true,"permalink":"/alaqsam/alidary/mkhtt-erd-dbml-alidary/","dg-note-properties":{}}
---

```dbml
// ============================================
// القسم الإداري (Admin Section) - Final Clean Version
// ============================================

// ============================================
// GLOBAL LOOKUP TABLE
// ============================================

Table status {
  status_id int [pk, increment]
}

// ============================================
// BLACK BOX TABLES (Only ID is stored)
// ============================================

Table user_black_box {
  user_id int [pk, increment]
}

Table directorate_black_box {
  directorate_id int [pk, increment]
}

Table audit_log_black_box {
  audit_log_id int [pk, increment]
  user_id int [ref: > user_black_box.user_id]
}

Table employee_black_box {
	employee_id int [pk, increment]
}

// ============================================
// ATTENDANCE (Your Design)
// ============================================

Table attendance_event {
  event_id int [pk, increment]
  user_id int [ref: > employee_black_box.employee_id]
  event_time timestamp
  event_type varchar(10)
  task_id int [note: 'Black Box (FK to task, optional)']
  go_home_directly boolean
  notes text
}

// ============================================
// CIRCULARS & PROPOSALS (Your Design)
// ============================================

Table circular_proposal {
  proposal_id int [pk, increment]
  title varchar(255)
  content text
  justification text
  proposed_by_user_id int [ref: > user_black_box.user_id]
  sent_to_municipality_at timestamp
  municipality_feedback text
  approved_version text
  approved_at timestamp
  approved_by_directorate_id int [ref: > directorate_black_box.directorate_id]
  final_circular_id int [ref: > circular.circular_id]
}

Table circular {
  circular_id int [pk, increment]
  circular_number varchar(50)
  title varchar(255)
  content text
  issue_date date
  effective_date date
  issued_by_user_id int [ref: > user_black_box.user_id]
}

// ============================================
// UNIVERSAL STATUS TRACKING (Your Design)
// ============================================

Table entity_status {
  entity_status_id int [pk, increment]
  entity_type varchar(50)
  entity_id int
  status_id int [ref: > status.status_id]
}

// ============================================
// RELATIONSHIPS (Foreign Keys)
// ============================================

// Attendance
Ref: attendance_event.user_id > employee_black_box.employee_id

// Circular Proposals
Ref: circular_proposal.proposed_by_user_id > user_black_box.user_id
Ref: circular_proposal.department_id > department_black_box.department_id
Ref: circular_proposal.final_circular_id > circular.circular_id
Ref: circular_proposal.proposal_id > entity_status.entity_id

// Circular
Ref: circular.issued_by_user_id > user_black_box.user_id
Ref: circular.department_id > department_black_box.department_id


// Entity Status
Ref: entity_status.status_id > status.status_id

// @pos status 1069 676
// @pos user_black_box 747 151
// @pos directorate_black_box 801 276
// @pos audit_log_black_box 744 35
// @pos employee_black_box 1078 348
// @pos attendance_event 1070 34
// @pos circular_proposal 251 401
// @pos circular 3 12
// @pos entity_status 1073 482
// @view 43 19 0.926
// @size 1577 793
```