---
{"dg-publish":true,"permalink":"/alaqsam/alnafdht/mkhtt-erd-dbml-alnafdht/","dg-note-properties":{}}
---

```dbml
// ============================================
// قسم النافذة (Window Unit)
// ============================================

// ============================================
// CORE LOOKUP TABLES
// ============================================

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
  directorate_id int [note: 'Black Box (FK to external directorate)']
}

Table status {
  status_id int [pk, increment]
  name varchar(50)
  display_name varchar(100)
  description text
  color_code varchar(7)
}

// ============================================
// ENTITY TABLES (Black Box)
// ============================================

Table citizen {
  citizen_id int [pk, increment]
  full_name varchar(255)
  phone varchar(20)
  email varchar(100)
  id_number varchar(50)
  note: 'Black Box (managed by external system)'
}

Table directorate {
  directorate_id int [pk, increment]
  name varchar(100)
  phone varchar(20)
  email varchar(100)
  note: 'Black Box (managed by external system)'
}

// ============================================
// TRANSACTION TABLES
// ============================================

Table citizen_transaction {
  citizen_transaction_id int [pk, increment]
  transaction_type_id int [ref: > transaction_type.transaction_type_id]
  citizen_id int [ref: > citizen.citizen_id]
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
  source_directorate_id int [ref: > directorate.directorate_id]
  reference_number varchar(100)
  description text
  location varchar(255)
  latitude decimal(10,8)
  longitude decimal(11,8)
  parent_transaction_id int [note: 'Reference to another directorate_transaction (Free ID, no FK)']
  channel_id int [ref: > channel.channel_id]
  assigned_to_department_id int [ref: > department.department_id]
  assigned_to_user_id int [ref: > user.user_id]
}

// ============================================
// SHARED / CROSS-MODULE TABLES
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
  fee_type_id int
  amount decimal(10,2)
  payment_method_id int
  payment_date timestamp
  receipt_number varchar(100)
  due_date date
  notes text
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
// REFERENCED TABLES (Black Box - For Context)
// ============================================

Table user {
  user_id int [pk, increment]
  username varchar(50)
}

Table department {
  department_id int [pk, increment]
  name varchar(100)
}

// ============================================
// RELATIONSHIPS
// ============================================

// Citizen → Citizen Transaction
Ref: citizen_transaction.citizen_id > citizen.citizen_id

// Directorate → Directorate Transaction
Ref: directorate_transaction.source_directorate_id > directorate.directorate_id

// Citizen Transaction Relationships
Ref: citizen_transaction.transaction_type_id > transaction_type.transaction_type_id
Ref: citizen_transaction.channel_id > channel.channel_id
Ref: citizen_transaction.assigned_to_department_id > department.department_id
Ref: citizen_transaction.assigned_to_user_id > user.user_id

// Directorate Transaction Relationships
Ref: directorate_transaction.transaction_type_id > transaction_type.transaction_type_id
Ref: directorate_transaction.channel_id > channel.channel_id
Ref: directorate_transaction.assigned_to_department_id > department.department_id
Ref: directorate_transaction.assigned_to_user_id > user.user_id

// Attachment
Ref: attachment.uploaded_by_user_id > user.user_id

// Notification
Ref: notification.recipient_user_id > user.user_id
Ref: notification.channel_id > channel.channel_id

// Entity Status
Ref: entity_status.status_id > status.status_id
Ref: entity_status.previous_status_id > status.status_id
// @view 150 148 0.927
// @size 1571 777
```