---
{"dg-publish":true,"permalink":"/diagram/mkhtt-erd-dbml/","title":"Database Schema","dg-note-properties":{"title":"Database Schema"}}
---

```dbml
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

Table channel {
  channel_id int [pk, increment]
  name varchar(50)
  display_name varchar(100)
  description text
  is_active boolean
}

Table transaction_type {
  transaction_type_id int [pk, increment]
  name varchar(50)
  display_name varchar(100)
  description text
  is_active boolean
  directorate_id int [note: 'Black Box (FK to external directorate table)']
}

Table permit_type {
  permit_type_id int [pk, increment]
  name varchar(50)
  display_name varchar(100)
  description text
  is_active boolean
}

Table fee_type {
  fee_type_id int [pk, increment]
  name varchar(50)
  display_name varchar(100)
  description text
  is_active boolean
}

Table payment_method {
  payment_method_id int [pk, increment]
  name varchar(50)
  display_name varchar(100)
  description text
  is_active boolean
}

// ============================================
// USERS, STRUCTURE & SECURITY
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
  directorate_id int [note: 'Black Box']
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
// TRANSACTIONS
// ============================================
Table citizen_transaction {
  citizen_transaction_id int [pk, increment]
  transaction_type_id int [ref: > transaction_type.transaction_type_id]
  citizen_id int [note: 'Black Box (FK to citizen)']
  citizen_name varchar(255)
  citizen_phone varchar(20)
  citizen_email varchar(100)
  citizen_id_number varchar(50)
  description text
  location varchar(255)
  latitude decimal(10,8)
  longitude decimal(11,8)
  source_directorate_id int [note: 'Black Box (FK to directorate)']
  channel_id int [ref: > channel.channel_id]
  assigned_to_department_id int [ref: > department.department_id]
  assigned_to_user_id int [ref: > user.user_id]
}

Table directorate_transaction {
  directorate_transaction_id int [pk, increment]
  transaction_type_id int [ref: > transaction_type.transaction_type_id]
  source_directorate_id int [note: 'Black Box (FK to directorate)']
  reference_number varchar(100)
  description text
  location varchar(255)
  latitude decimal(10,8)
  longitude decimal(11,8)
  parent_transaction_id int [ref: > directorate_transaction.directorate_transaction_id]
  channel_id int [ref: > channel.channel_id]
  assigned_to_department_id int [ref: > department.department_id]
  assigned_to_user_id int [ref: > user.user_id]
}

// ============================================
// APPROVAL SYSTEM
// ============================================
Table approval_workflow {
  workflow_step_id int [pk, increment]
  entity_type varchar(50)
  entity_type_id int
  step_order int
  step_name varchar(100)
  approving_directorate_id int [note: 'Black Box (FK to directorate)']
  approving_department_id int [ref: > department.department_id]
}

Table approval_history {
  approval_history_id int [pk, increment]
  entity_type varchar(50)
  entity_id int
  workflow_step_id int [ref: > approval_workflow.workflow_step_id]
  approver_user_id int [ref: > user.user_id]
  comment text
}

// ============================================
// WAREHOUSE
// ============================================
Table purchase_request {
  purchase_request_id int [pk, increment]
  material_id int [note: 'Black Box (FK to material)']
  requested_quantity decimal(10,2)
  current_stock decimal(10,2)
  suggested_supplier varchar(255)
  estimated_cost decimal(10,2)
  requested_by_user_id int [ref: > user.user_id]
  director_approved_at timestamp
  completed_at timestamp
  budget_allocation_id int [note: 'Black Box (FK to budget_allocation)']
  quantity_purchased decimal(10,2)
}

Table inventory_audit_cycle {
  audit_cycle_id int [pk, increment]
  cycle_name varchar(100)
  start_date date
  end_date date
  approved_by_user_id int [ref: > user.user_id]
  notes text
}

Table inventory_audit_item {
  audit_item_id int [pk, increment]
  audit_cycle_id int [ref: > inventory_audit_cycle.audit_cycle_id]
  material_id int [note: 'Black Box (FK to material)']
  system_quantity decimal(10,2)
  physical_quantity decimal(10,2)
  notes text
}

// ============================================
// PERMITS
// ============================================
Table permit {
  permit_id int [pk, increment]
  source_type varchar(30)
  source_id int
  permit_type_id int [ref: > permit_type.permit_type_id]
  permit_number varchar(50)
  issue_date date
  expiry_date date
  issued_by_user_id int [ref: > user.user_id]
  location varchar(255)
  latitude decimal(10,8)
  longitude decimal(11,8)
  detailed_description text
  qr_code varchar(500)
}

// ============================================
// SHARED / CROSS-MODULE
// ============================================
Table attachment {
  attachment_id int [pk, increment]
  entity_type varchar(50)
  entity_id int
  file_name varchar(255)
  file_path varchar(500)
  file_size int
  mime_type varchar(100)
  description text
  uploaded_by_user_id int [ref: > user.user_id]
}

Table notification {
  notification_id int [pk, increment]
  recipient_user_id int [ref: > user.user_id]
  recipient_citizen_id int [note: 'Black Box (FK to citizen)']
  recipient_phone varchar(20)
  recipient_email varchar(100)
  entity_type varchar(50)
  entity_id int
  subject varchar(255)
  message text
  channel_id int [ref: > channel.channel_id]
  delivery_status varchar(20)
  is_read boolean
  read_at timestamp
  sent_at timestamp
}

Table transaction_fee {
  transaction_fee_id int [pk, increment]
  entity_type varchar(30)
  entity_id int
  fee_type_id int [ref: > fee_type.fee_type_id]
  amount decimal(10,2)
  payment_method_id int [ref: > payment_method.payment_method_id]
  payment_date timestamp
  receipt_number varchar(100)
  due_date date
  notes text
}

// ============================================
// UNIVERSAL STATUS SYSTEM
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

Ref: citizen_transaction.transaction_type_id > transaction_type.transaction_type_id
Ref: citizen_transaction.channel_id > channel.channel_id
Ref: citizen_transaction.assigned_to_department_id > department.department_id
Ref: citizen_transaction.assigned_to_user_id > user.user_id

Ref: directorate_transaction.transaction_type_id > transaction_type.transaction_type_id
Ref: directorate_transaction.parent_transaction_id > directorate_transaction.directorate_transaction_id
Ref: directorate_transaction.channel_id > channel.channel_id
Ref: directorate_transaction.assigned_to_department_id > department.department_id
Ref: directorate_transaction.assigned_to_user_id > user.user_id

Ref: approval_history.workflow_step_id > approval_workflow.workflow_step_id
Ref: approval_history.approver_user_id > user.user_id

Ref: purchase_request.requested_by_user_id > user.user_id

Ref: inventory_audit_item.audit_cycle_id > inventory_audit_cycle.audit_cycle_id
Ref: inventory_audit_cycle.approved_by_user_id > user.user_id

Ref: permit.permit_type_id > permit_type.permit_type_id
Ref: permit.issued_by_user_id > user.user_id

Ref: attachment.uploaded_by_user_id > user.user_id

Ref: notification.recipient_user_id > user.user_id
Ref: notification.channel_id > channel.channel_id

Ref: transaction_fee.fee_type_id > fee_type.fee_type_id
Ref: transaction_fee.payment_method_id > payment_method.payment_method_id

Ref: entity_status.status_id > status.status_id
Ref: entity_status.previous_status_id > status.status_id
// @view 84 67 0.748
// @size 1569 739
```