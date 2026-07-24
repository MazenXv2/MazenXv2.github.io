---
{"dg-publish":true,"permalink":"/diagram/mkhtt-erd-dbml/","title":"Database Schema","dg-note-properties":{"title":"Database Schema"}}
---

```dbml
// ============================================
// CORE LOOKUP TABLES
// ============================================
Table status {
  status_id int [pk, increment]
  name varchar
  display_name varchar
  description text
  color_code varchar
}

Table channel {
  channel_id int [pk, increment]
  name varchar
  display_name varchar
  description text
  is_active boolean
}

Table transaction_type {
  transaction_type_id int [pk, increment]
  name varchar
  display_name varchar
  description text
  is_active boolean
  directorate_id int [note: 'Black Box (FK to external directorate table)']
}

Table permit_type {
  permit_type_id int [pk, increment]
  name varchar
  display_name varchar
  description text
  is_active boolean
}

Table fee_type {
  fee_type_id int [pk, increment]
  name varchar
  display_name varchar
  description text
  is_active boolean
}

Table payment_method {
  payment_method_id int [pk, increment]
  name varchar
  display_name varchar
  description text
  is_active boolean
}

// ============================================
// USERS, STRUCTURE & SECURITY
// ============================================
Table user {
  user_id int [pk, increment]
  username varchar
  password_hash varchar
  email varchar
  phone varchar
  role_id int [ref: > role.role_id]
  department_id int [ref: > department.department_id]
  directorate_id int [note: 'Black Box']
  is_active boolean
  last_login datetime
}

Table role {
  role_id int [pk, increment]
  role_name varchar
  description text
}

Table department {
  department_id int [pk, increment]
  name varchar
  parent_id int [ref: > department.department_id]
  manager_user_id int [ref: > user.user_id]
  description text
  directorate_id int [note: 'Black Box']
}

Table resource {
  resource_id int [pk, increment]
  name varchar
  description text
  module varchar
}

Table permission {
  permission_id int [pk, increment]
  name varchar
  description text
  resource_id int [ref: > resource.resource_id]
  action varchar
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
  granted_at datetime
  start_date datetime
  end_date datetime
}

Table department_permission {
  department_id int [pk, ref: > department.department_id]
  permission_id int [pk, ref: > permission.permission_id]
}

// ============================================
// TRANSACTIONS
// ============================================
Table citizen_transaction {
  citizen_transaction_id int [pk, increment]
  transaction_type_id int [ref: > transaction_type.transaction_type_id]
  citizen_id int [note: 'Black Box (FK to citizen)']
  citizen_name varchar
  citizen_phone varchar
  citizen_email varchar
  citizen_id_number varchar
  description text
  location varchar
  latitude decimal
  longitude decimal
  source_directorate_id int [note: 'Black Box (FK to directorate)']
  channel_id int [ref: > channel.channel_id]
  assigned_to_department_id int [ref: > department.department_id]
  assigned_to_user_id int [ref: > user.user_id]
}

Table directorate_transaction {
  directorate_transaction_id int [pk, increment]
  transaction_type_id int [ref: > transaction_type.transaction_type_id]
  source_directorate_id int [note: 'Black Box (FK to directorate)']
  reference_number varchar
  description text
  location varchar
  latitude decimal
  longitude decimal
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
  entity_type varchar
  entity_type_id int
  step_order int
  step_name varchar
  approving_directorate_id int [note: 'Black Box (FK to directorate)']
  approving_department_id int [ref: > department.department_id]
}

Table approval_history {
  approval_history_id int [pk, increment]
  entity_type varchar
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
  requested_quantity decimal
  current_stock decimal
  suggested_supplier varchar
  estimated_cost decimal
  requested_by_user_id int [ref: > user.user_id]
  director_approved_at datetime
  completed_at datetime
  budget_allocation_id int [note: 'Black Box (FK to budget_allocation)']
  quantity_purchased decimal
}

Table inventory_audit_cycle {
  audit_cycle_id int [pk, increment]
  cycle_name varchar
  start_date date
  end_date date
  approved_by_user_id int [ref: > user.user_id]
  notes text
}

Table inventory_audit_item {
  audit_item_id int [pk, increment]
  audit_cycle_id int [ref: > inventory_audit_cycle.audit_cycle_id]
  material_id int [note: 'Black Box (FK to material)']
  system_quantity decimal
  physical_quantity decimal
  notes text
}

// ============================================
// PERMITS
// ============================================
Table permit {
  permit_id int [pk, increment]
  source_type enum('citizen_transaction', 'directorate_transaction')
  source_id int
  permit_type_id int [ref: > permit_type.permit_type_id]
  permit_number varchar
  issue_date date
  expiry_date date
  issued_by_user_id int [ref: > user.user_id]
  location varchar
  latitude decimal
  longitude decimal
  detailed_description text
  qr_code varchar
}

// ============================================
// SHARED / CROSS-MODULE
// ============================================
Table attachment {
  attachment_id int [pk, increment]
  entity_type varchar
  entity_id int
  file_name varchar
  file_path varchar
  file_size int
  mime_type varchar
  description text
  uploaded_by_user_id int [ref: > user.user_id]
}

Table notification {
  notification_id int [pk, increment]
  recipient_user_id int [ref: > user.user_id]
  recipient_citizen_id int [note: 'Black Box (FK to citizen)']
  recipient_phone varchar
  recipient_email varchar
  entity_type varchar
  entity_id int
  subject varchar
  message text
  channel_id int [ref: > channel.channel_id]
  delivery_status enum('pending', 'sent', 'delivered', 'failed')
  is_read boolean
  read_at datetime
  sent_at datetime
}

Table transaction_fee {
  transaction_fee_id int [pk, increment]
  entity_type enum('citizen_transaction', 'directorate_transaction')
  entity_id int
  fee_type_id int [ref: > fee_type.fee_type_id]
  amount decimal
  payment_method_id int [ref: > payment_method.payment_method_id]
  payment_date datetime
  receipt_number varchar
  due_date date
  notes text
}

// ============================================
// UNIVERSAL STATUS SYSTEM
// ============================================
Table entity_status {
  entity_status_id int [pk, increment]
  entity_type varchar
  entity_id int
  status_id int [ref: > status.status_id]
  previous_status_id int [ref: > status.status_id]
  comment text
}

// ============================================
// RELATIONSHIPS (Explicit References)
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
// @view -940 -1490 1.165
// @size 2130 1008
```