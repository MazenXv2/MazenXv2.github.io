---
{"dg-publish":true,"permalink":"/diagram/mkhtt-erd-dbml/","title":"Database Schema","dg-note-properties":{"title":"Database Schema"}}
---

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

  directorate_id int

  note: 'Black Box (FK to external directorate table)'

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

  role_id int

  department_id int

  directorate_id int

  is_active boolean

  last_login timestamp

  note: 'directorate_id is a Black Box (FK to external directorate)'

}

  

Table role {

  role_id int [pk, increment]

  role_name varchar(50)

  description text

}

  

Table department {

  department_id int [pk, increment]

  name varchar(100)

  parent_id int

  manager_user_id int

  description text

  directorate_id int

  note: 'directorate_id is a Black Box'

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

  resource_id int

  action varchar(50)

}

  

Table role_permission {

  role_id int [pk]

  permission_id int [pk]

  can_grant boolean

}

  

Table user_permission {

  user_id int [pk]

  permission_id int [pk]

  granted_by_user_id int

  granted_at timestamp

  start_date timestamp

  end_date timestamp

}

  

Table department_permission {

  department_id int [pk]

  permission_id int [pk]

}

  

// ============================================

// TRANSACTIONS

// ============================================

Table citizen_transaction {

  citizen_transaction_id int [pk, increment]

  transaction_type_id int

  citizen_id int

  citizen_name varchar(255)

  citizen_phone varchar(20)

  citizen_email varchar(100)

  citizen_id_number varchar(50)

  description text

  location varchar(255)

  latitude decimal(10,8)

  longitude decimal(11,8)

  source_directorate_id int

  channel_id int

  assigned_to_department_id int

  assigned_to_user_id int

  note: 'citizen_id and source_directorate_id are Black Boxes'

}

  

Table directorate_transaction {

  directorate_transaction_id int [pk, increment]

  transaction_type_id int

  source_directorate_id int

  reference_number varchar(100)

  description text

  location varchar(255)

  latitude decimal(10,8)

  longitude decimal(11,8)

  parent_transaction_id int

  channel_id int

  assigned_to_department_id int

  assigned_to_user_id int

  note: 'source_directorate_id is a Black Box'

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

  approving_directorate_id int

  approving_department_id int

  note: 'approving_directorate_id is a Black Box'

}

  

Table approval_history {

  approval_history_id int [pk, increment]

  entity_type varchar(50)

  entity_id int

  workflow_step_id int

  approver_user_id int

  comment text

}

  

// ============================================

// WAREHOUSE

// ============================================

Table purchase_request {

  purchase_request_id int [pk, increment]

  material_id int

  requested_quantity decimal(10,2)

  current_stock decimal(10,2)

  suggested_supplier varchar(255)

  estimated_cost decimal(10,2)

  requested_by_user_id int

  director_approved_at timestamp

  completed_at timestamp

  budget_allocation_id int

  quantity_purchased decimal(10,2)

  note: 'material_id and budget_allocation_id are Black Boxes'

}

  

Table inventory_audit_cycle {

  audit_cycle_id int [pk, increment]

  cycle_name varchar(100)

  start_date date

  end_date date

  approved_by_user_id int

  notes text

}

  

Table inventory_audit_item {

  audit_item_id int [pk, increment]

  audit_cycle_id int

  material_id int

  system_quantity decimal(10,2)

  physical_quantity decimal(10,2)

  notes text

  note: 'material_id is a Black Box'

}

  

// ============================================

// PERMITS

// ============================================

Table permit {

  permit_id int [pk, increment]

  source_type varchar(30)

  source_id int

  permit_type_id int

  permit_number varchar(50)

  issue_date date

  expiry_date date

  issued_by_user_id int

  location varchar(255)

  latitude decimal(10,8)

  longitude decimal(11,8)

  detailed_description text

  qr_code varchar(500)

  note: 'source_type = "citizen_transaction" or "directorate_transaction"'

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

  uploaded_by_user_id int

}

  

Table notification {

  notification_id int [pk, increment]

  recipient_user_id int

  recipient_citizen_id int

  recipient_phone varchar(20)

  recipient_email varchar(100)

  entity_type varchar(50)

  entity_id int

  subject varchar(255)

  message text

  channel_id int

  delivery_status varchar(20)

  is_read boolean

  read_at timestamp

  sent_at timestamp

  note: 'recipient_citizen_id is a Black Box. delivery_status: pending, sent, delivered, failed'

}

  

Table transaction_fee {

  transaction_fee_id int [pk, increment]

  entity_type varchar(30)

  entity_id int

  fee_type_id int

  amount decimal(10,2)

  payment_method_id int

  payment_date timestamp

  receipt_number varchar(100)

  due_date date

  notes text

  note: 'entity_type = "citizen_transaction" or "directorate_transaction"'

}

  

// ============================================

// UNIVERSAL STATUS SYSTEM

// ============================================

Table entity_status {

  entity_status_id int [pk, increment]

  entity_type varchar(50)

  entity_id int

  status_id int

  previous_status_id int

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