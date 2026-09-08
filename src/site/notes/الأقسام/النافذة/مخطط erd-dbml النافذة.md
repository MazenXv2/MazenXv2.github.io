---
{"dg-publish":true,"permalink":"/alaqsam/alnafdht/mkhtt-erd-dbml-alnafdht/","dg-note-properties":{}}
---

```dbml
// =============================================
// CORE LOOKUP TABLES
// =============================================

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
  entity_type varchar(50)
  entity_id int
  name varchar(50)
  display_name varchar(100)
  description text
  color_code varchar(7)
}

// =============================================
// ENTITY TABLES (Black Box)
// =============================================

Table citizen_black_box {
  citizen_id int [pk, increment]
  note: 'Black Box (managed by external system)'
}

Table directorate_black_box {
  directorate_id int [pk, increment]
  note: 'Black Box (managed by external system)'
}

// =============================================
// TRANSACTION TABLE (Core)
// =============================================

Table transaction {
  transaction_id int [pk, increment]
  // Fields will be added here in future iterations
  note: 'Core transaction table - fields to be expanded'
}

// =============================================
// SHARED / CROSS-MODULE TABLES
// =============================================

Table attachment {
  attachment_id int [pk, increment]
  entity_type varchar(50)
  entity_id int
  file_name varchar(255)
  file_path varchar(500)
  file_size int
  content_type varchar(100)
  description text
  uploaded_by_user_id int [ref: > user_black_box.user_id]
}

Table notification {
  notification_id int [pk, increment]
  recipient_user_id int [ref: > user_black_box.user_id]
  entity_type varchar(50)
  entity_id int
  subject varchar(255)
  message text
  delivery_status varchar(20)
  is_read boolean
  read_at timestamp
  sent_at timestamp
}

// =============================================
// STATUS TRACKING (Global)
// =============================================

Table entity_status {
  entity_status_id int [pk, increment]
  entity_id int [ref: > transaction.transaction_id]
  status_id int [ref: > status.status_id]
  previous_status_id int [ref: > status.status_id]
  comment text
}

// =============================================
// REFERENCED TABLES (Black Box - For Context)
// =============================================

Table user_black_box {
  user_id int [pk, increment]
  note: 'Black Box (managed by authentication system)'
}

Table department_black_box {
  department_id int [pk, increment]
  note: 'Black Box (managed by organizational system)'
}

// =============================================
// RELATIONSHIPS (Foreign Keys)
// =============================================

// Citizen → Transaction
// Polymorphic relationship (source_type = 'citizen')
// Not enforced as a direct FK because source_id can point to citizen_bb or user_bb
Ref: transaction.transaction_id > citizen_black_box.citizen_id
  note: 'Polymorphic reference - source_type = "citizen"'

// Directorate → Transaction
// Polymorphic relationship (source_type = 'directorate')
Ref: transaction.transaction_id > directorate_black_box.directorate_id
  note: 'Polymorphic reference - source_type = "directorate"'

// Transaction → Transaction Type
Ref: transaction.transaction_type_id > transaction_type.transaction_type_id

// Transaction → Channel
Ref: transaction.channel_id > channel.channel_id

// Transaction → Department (Assigned)
Ref: transaction.assigned_to_department_id > department_black_box.department_id

// Transaction → User (Assigned)
Ref: transaction.assigned_to_user_id > user_black_box.user_id

// Attachment → Transaction (Polymorphic)
// attachment.entity_id refers to transaction.transaction_id when entity_type = 'transaction'
Ref: attachment.entity_id > transaction.transaction_id
  note: 'Polymorphic reference - entity_type = "transaction"'

// Notification → Transaction (Polymorphic)
// notification.entity_id refers to transaction.transaction_id when entity_type = 'transaction'
Ref: notification.entity_id > transaction.transaction_id
  note: 'Polymorphic reference - entity_type = "transaction"'

// Entity Status → Status
Ref: entity_status.status_id > status.status_id

// Entity Status → Previous Status
Ref: entity_status.previous_status_id > status.status_id

// Entity Status → Transaction
Ref: entity_status.entity_id > transaction.transaction_id
  note: 'Polymorphic reference - tracks status of transactions'
// @pos citizen_black_box 762 443
// @pos directorate_black_box 756 577
// @pos transaction 413 487
// @pos user_black_box 511 297
// @view 447 46 0.743
// @size 1510 789
```