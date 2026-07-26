---
{"dg-publish":true,"permalink":"/alaqsam/alidary/mkhtt-erd-dbml-alidary/","dg-note-properties":{}}
---

```dbml
// ============================================
// القسم الإداري (Admin Section)
// ============================================

// ============================================
// CORE LOOKUP TABLES
// ============================================

Table status {
  status_id int [pk, increment]
  name varchar(50)
  display_name varchar(100)
  description text
  color_code varchar(7)
}

Table request_type {
  request_type_id int [pk, increment]
  name varchar(50)
  display_name varchar(100)
  description text
  is_active boolean
}

Table performance_report_type {
  report_type_id int [pk, increment]
  name varchar(50)
  display_name varchar(100)
  description text
  is_active boolean
}

// ============================================
// USERS & STRUCTURE
// ============================================

Table user {
  user_id int [pk, increment]
  username varchar(50)
  password_hash varchar(255)
  email varchar(100)
  phone varchar(20)
  role_id int [ref: > role.role_id]
  department_id int [ref: > department.department_id]
  directorate_id int [note: 'Black Box (FK to external directorate)']
  is_active boolean
  last_login timestamp
}

Table role {
  role_id int [pk, increment]
  role_name varchar(50)
  description text
}

Table department {
  department_id int [pk, increment]
  name varchar(100)
  parent_id int [ref: > department.department_id]
  manager_user_id int [ref: > user.user_id]
  description text
  directorate_id int [note: 'Black Box (FK to external directorate)']
}

Table resource {
  resource_id int [pk, increment]
  name varchar(50)
  description text
  module varchar(50)
}

Table permission {
  permission_id int [pk, increment]
  name varchar(50)
  description text
  resource_id int [ref: > resource.resource_id]
  action varchar(50)
}

Table role_permission {
  role_id int [pk, ref: > role.role_id]
  permission_id int [pk, ref: > permission.permission_id]
  can_grant boolean
}

Table user_permission {
  user_id int [pk, ref: > user.user_id]
  permission_id int [pk, ref: > permission.permission_id]
  granted_by_user_id int [ref: > user.user_id]
  granted_at timestamp
  start_date timestamp
  end_date timestamp
}

Table department_permission {
  department_id int [pk, ref: > department.department_id]
  permission_id int [pk, ref: > permission.permission_id]
}

// ============================================
// ATTENDANCE
// ============================================

Table attendance_event {
  event_id int [pk, increment]
  user_id int [ref: > user.user_id]
  event_time timestamp
  event_type varchar(10)
  task_id int [note: 'Black Box (FK to task, optional)']
  go_home_directly boolean
  notes text
}

// ============================================
// GENERAL REQUESTS
// ============================================

Table general_request {
  general_request_id int [pk, increment]
  request_type_id int [ref: > request_type.request_type_id]
  requester_user_id int [ref: > user.user_id]
  department_id int [ref: > department.department_id]
  directorate_id int [note: 'Black Box (FK to directorate)']
  subject varchar(255)
  description text
  fulfilled_at timestamp
}

// ============================================
// CIRCULARS & PROPOSALS
// ============================================

Table circular_proposal {
  proposal_id int [pk, increment]
  title varchar(255)
  content text
  justification text
  proposed_by_user_id int [ref: > user.user_id]
  department_id int [ref: > department.department_id]
  sent_to_municipality_at timestamp
  municipality_feedback text
  approved_version text
  approved_at timestamp
  final_circular_id int [ref: > circular.circular_id]
}

Table circular {
  circular_id int [pk, increment]
  circular_number varchar(50)
  title varchar(255)
  content text
  issue_date date
  effective_date date
  issued_by_user_id int [ref: > user.user_id]
  department_id int [ref: > department.department_id]
  proposal_id int [ref: > circular_proposal.proposal_id]
}

// ============================================
// PERFORMANCE REPORTS
// ============================================

Table performance_report {
  report_id int [pk, increment]
  report_type_id int [ref: > performance_report_type.report_type_id]
  reporter_user_id int [ref: > user.user_id]
  subject_user_id int [ref: > user.user_id]
  department_id int [ref: > department.department_id]
  report_date date
  description text
  recommendations text
  sent_to_hr boolean
  sent_to_investigation boolean
  investigation_result text
  resolution_action text
}

// ============================================
// UNIVERSAL STATUS TRACKING
// ============================================

Table entity_status {
  entity_status_id int [pk, increment]
  entity_type varchar(50)
  entity_id int
  status_id int [ref: > status.status_id]
  previous_status_id int [ref: > status.status_id]
  comment text
}

// ============================================
// RELATIONSHIPS
// ============================================

Ref: user.role_id > role.role_id
Ref: user.department_id > department.department_id
Ref: department.parent_id > department.department_id
Ref: department.manager_user_id > user.user_id
Ref: permission.resource_id > resource.resource_id

Ref: role_permission.role_id > role.role_id
Ref: role_permission.permission_id > permission.permission_id

Ref: user_permission.user_id > user.user_id
Ref: user_permission.permission_id > permission.permission_id
Ref: user_permission.granted_by_user_id > user.user_id

Ref: department_permission.department_id > department.department_id
Ref: department_permission.permission_id > permission.permission_id

Ref: attendance_event.user_id > user.user_id

Ref: general_request.request_type_id > request_type.request_type_id
Ref: general_request.requester_user_id > user.user_id
Ref: general_request.department_id > department.department_id

Ref: circular_proposal.proposed_by_user_id > user.user_id
Ref: circular_proposal.department_id > department.department_id
Ref: circular_proposal.final_circular_id > circular.circular_id

Ref: circular.issued_by_user_id > user.user_id
Ref: circular.department_id > department.department_id
Ref: circular.proposal_id > circular_proposal.proposal_id

Ref: performance_report.report_type_id > performance_report_type.report_type_id
Ref: performance_report.reporter_user_id > user.user_id
Ref: performance_report.subject_user_id > user.user_id
Ref: performance_report.department_id > department.department_id
// @view -510 -1017 0.999
// @size 1575 796
```