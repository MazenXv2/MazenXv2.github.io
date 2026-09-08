---
{"dg-publish":true,"permalink":"/alaqsam/almeamlat/mkhtt-erd-dbml-almeamlat/","dg-note-properties":{}}
---

```dbml
// ============================================
// TRANSACTIONS SECTION (MERGED - SINGLE TABLE)
// ============================================

// ============================================
// CORE LOOKUP TABLES
// ============================================

Table transaction_type {
  transaction_type_id int [pk, increment]
  name varchar(50)
  display_name varchar(100)
  description text
  is_active boolean
}

Table channel {
  channel_id int [pk, increment]
  name varchar(50)
  display_name varchar(100)
  description text
  is_active boolean
}

Table status {
  status_id int [pk, increment]
  name varchar(50)
  display_name varchar(100)
  description text
  color_code varchar(7)
}

// ============================================
// BLACK BOX TABLES (External References)
// ============================================

Table citizen_black_box {
  citizen_id int [pk, increment]
  note: 'Black Box (managed by external citizen system)'
}

Table directorate_black_box {
  directorate_id int [pk, increment]
  note: 'Black Box (managed by external directorate system)'
}

Table user_black_box {
  user_id int [pk, increment]
  note: 'Black Box (managed by authentication system)'
}

Table department_black_box {
  department_id int [pk, increment]
  note: 'Black Box (managed by organizational system)'
}

// ============================================
// TRANSACTION TABLE (MERGED)
// ============================================

Table transaction {
  transaction_id int [pk, increment]
  source_type varchar(30)
  source_id int
  transaction_type_id int [ref: > transaction_type.transaction_type_id]
  channel_id int [ref: > channel.channel_id]
  description text
  address varchar(255)
  latitude decimal(10,8)
  longitude decimal(11,8)
  reference_number varchar(100)
  assigned_to_department_id int [ref: > department_black_box.department_id]
  assigned_to_user_id int [ref: > user_black_box.user_id]
}

// ============================================
// APPROVAL SYSTEM
// ============================================

Table approval_workflow {
  workflow_step_id int [pk, increment]
  transaction_type_id int [ref: > transaction_type.transaction_type_id]
  step_order int
  step_name varchar(100)
  approving_directorate_id int [ref: > directorate_black_box.directorate_id]
  approving_department_id int [ref: > department_black_box.department_id]
}

Table approval_history {
  approval_history_id int [pk, increment]
  transaction_id int [ref: > transaction.transaction_id]
  workflow_step_id int [ref: > approval_workflow.workflow_step_id]
  approver_user_id int [ref: > user_black_box.user_id]
  comment text
}

// ============================================
// STATUS TRACKING (Global)
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
// RELATIONSHIPS (Foreign Keys)
// ============================================

Ref: transaction.transaction_type_id > transaction_type.transaction_type_id
Ref: transaction.channel_id > channel.channel_id
Ref: transaction.assigned_to_department_id > department_black_box.department_id
Ref: transaction.assigned_to_user_id > user_black_box.user_id'
Ref: transaction.transaction_id > entity_status.entity_id

Ref: approval_workflow.transaction_type_id > transaction_type.transaction_type_id
Ref: approval_workflow.approving_directorate_id > directorate_black_box.directorate_id
Ref: approval_workflow.approving_department_id > department_black_box.department_id

Ref: approval_history.transaction_id > transaction.transaction_id
Ref: approval_history.workflow_step_id > approval_workflow.workflow_step_id
Ref: approval_history.approver_user_id > user_black_box.user_id

Ref: entity_status.status_id > status.status_id
Ref: entity_status.previous_status_id > status.status_id

Ref: citizen_black_box.citizen_id > transaction.source_id
Ref: directorate_black_box.directorate_id > transaction.source_id
// @pos transaction_type -237 857
// @pos channel 1221 737
// @pos status 1210 46
// @pos citizen_black_box 12 14
// @pos directorate_black_box 10 98
// @pos user_black_box 627 790
// @pos department_black_box 1038 640
// @pos transaction 495 134
// @pos approval_workflow -295 344
// @pos approval_history -276 620
// @pos entity_status 1142 300
// @view 268 46 0.767
// @size 1570 793
```