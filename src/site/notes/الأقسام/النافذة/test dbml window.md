---
{"dg-publish":true,"permalink":"/alaqsam/alnafdht/test-dbml-window/","dg-note-properties":{}}
---

```dbml
// =============================================
// 1. MASTER DATA / BLACK BOXES (External Systems)
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
// 2. LOOKUP TABLE (Visible)
// =============================================

Table channel {
  channel_id int [pk, increment]
  name varchar(50)
  display_name varchar(100)
  description text
  is_active boolean
  note: 'Visible: Communication channels (window, phone, whatsapp, email, etc.)'
}

// =============================================
// 3. SHARED TABLES (Visible)
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
  note: 'Visible: Centralized file management for all modules'
}

Table notification {
  notification_id int [pk, increment]
  
  // Relational (Fast Filtering & FKs)
  recipient_user_id int [ref: > user_black_box.user_id]
  entity_type varchar(50) // 'transaction', 'permit', 'purchase_request'
  entity_id int
  channel_id int [ref: > channel.channel_id] // 'email', 'whatsapp', 'in_app', 'sms'
  
  delivery_status varchar(20) 
  is_read boolean
  read_at timestamp
  sent_at timestamp

  content jsonb [not null, default: '{}']
  
  note: 'content JSONB stores the actual message payload. Structure varies by channel (email, whatsapp, in_app).'
}

// =============================================
// 4. TRANSACTION MODULE (Black Box)
// =============================================

Table transaction_module_bb {
  transaction_id int [pk, increment]
  source_type varchar(30) [not null] // 'citizen' or 'directorate'
  source_id int [not null] // references citizen_bb or directorate_bb
  
  // Visible relationships to shared tables
  channel_id int [ref: > channel.channel_id]
  assigned_to_user_id int [ref: > user_black_box.user_id]
  assigned_to_department_id int [ref: > department_black_box.department_id]
  
  note: 'Black Box: All transaction details, workflow, status history, approvals, and fees are managed internally.'
}

// =============================================
// 5. RELATIONSHIPS
// =============================================

// Transaction → Channel (Visible)
Ref: transaction_module_bb.channel_id > channel.channel_id

// Transaction → User (Assigned)
Ref: transaction_module_bb.assigned_to_user_id > user_black_box.user_id

// Transaction → Department (Assigned)
Ref: transaction_module_bb.assigned_to_department_id > department_black_box.department_id

// Attachment → User (Uploader)
Ref: attachment.uploaded_by_user_id > user_black_box.user_id

// Notification → User (Recipient)
Ref: notification.recipient_user_id > user_black_box.user_id

// Polymorphic Relationships (Application Level)
// transaction_module_bb.source_id → citizen_black_box.citizen_id (when source_type = 'citizen')
// transaction_module_bb.source_id → directorate_black_box.directorate_id (when source_type = 'directorate')
// attachment.entity_id → transaction_module_bb.transaction_id (when entity_type = 'transaction')
// notification.entity_id → transaction_module_bb.transaction_id (when entity_type = 'transaction')
// @pos user_black_box 354 614
// @pos attachment 621 98
// @pos notification -1 226
// @pos transaction_module_bb -2 0
// @view 327 29 0.990
// @size 1489 776
```