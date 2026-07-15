---
{"dg-publish":true,"permalink":"/diagram/mkhtt-erd-dbml/","title":"Database Schema","dg-note-properties":{"title":"Database Schema"}}
---


```dbml
// ============================================
// 1. مجموعة الديوان - الكيانات الأساسية
// ============================================

Table User {
  user_id int [pk, increment]
  username varchar
  password_hash varchar
  email varchar
  phone varchar
  role_id int
  department_id int
  is_active boolean
  created_at datetime
  last_login datetime
}

Table Role {
  role_id int [pk, increment]
  role_name varchar
  description varchar
  created_at datetime
}

Table Department {
  department_id int [pk, increment]
  department_name varchar
  parent_department_id int
  manager_id int
  description varchar
}

Table Book {
  book_id int [pk, increment]
  book_number varchar
  book_date date
  direction varchar
  type varchar
  sender_department_id int
  receiver_department_id int
  subject varchar
  content text
  file_url varchar
  registered_by int
  status varchar
  created_at datetime
  updated_at datetime
}

Table Complaint {
  complaint_id int [pk, increment]
  external_citizen_id varchar
  citizen_name varchar
  citizen_phone varchar
  citizen_email varchar
  citizen_id_number varchar
  complaint_type varchar
  subject varchar
  description text
  file_url varchar
  status varchar
  priority varchar
  source varchar
  submitted_at datetime
  resolved_at datetime
  assigned_to int
  created_by int
}

Table Inquiry {
  inquiry_id int [pk, increment]
  external_citizen_id varchar
  citizen_name varchar
  citizen_phone varchar
  citizen_email varchar
  inquiry_type varchar
  subject varchar
  description text
  status varchar
  source varchar
  submitted_at datetime
  responded_at datetime
  assigned_to int
  created_by int
}

Table Attachment {
  attachment_id int [pk, increment]
  entity_type varchar
  entity_id int
  file_name varchar
  file_url varchar
  file_type varchar
  uploaded_by int
  uploaded_at datetime
}

Table Notification {
  notification_id int [pk, increment]
  user_id int
  notification_type varchar
  message varchar
  is_read boolean
  sent_via varchar
  sent_at datetime
  read_at datetime
}

Table Transaction {
  transaction_id int [pk, increment]
  transaction_number varchar
  transaction_type varchar
  entity_type varchar
  entity_id int
  created_date date
  assigned_to int
  status varchar
  created_by int
}

// ============================================
// 2. الكيانات الخارجية (خارج نظام المديرية)
// ============================================

Table External_System {
  system_name varchar [pk]
  api_endpoint varchar
  auth_method varchar
}

// ============================================
// 3. العلاقات بين الكيانات (DBML Syntax)
// ============================================

// User relationships
Ref: User.role_id > Role.role_id
Ref: User.department_id > Department.department_id
Ref: Notification.user_id > User.user_id
Ref: Book.registered_by > User.user_id
Ref: Attachment.uploaded_by > User.user_id
Ref: Transaction.created_by > User.user_id
Ref: Transaction.assigned_to > User.user_id
Ref: Complaint.created_by > User.user_id
Ref: Complaint.assigned_to > User.user_id
Ref: Inquiry.created_by > User.user_id
Ref: Inquiry.assigned_to > User.user_id

// Department relationships
Ref: Department.parent_department_id > Department.department_id
Ref: Department.manager_id > User.user_id
Ref: Book.sender_department_id > Department.department_id
Ref: Book.receiver_department_id > Department.department_id

// Book relationships
// Note: Book is already linked to User and Department above

// Complaint relationships
// Note: Complaint is already linked to User above

// Inquiry relationships
// Note: Inquiry is already linked to User above

// Attachment relationships - polymorphic relationships (handled at application level)
// entity_type and entity_id link to Complaint, Inquiry, or Book

// Transaction relationships
// Note: Transaction is already linked to User above
// Transaction.entity_type and entity_id link to Complaint or Inquiry

// External System relationships - logical relationships (not physical FKs)
// External_System sends/receives Complaint and Inquiry via API

// ============================================
// 4. ملاحظات إضافية للعلاقات
// ============================================

// علاقات متعددة الأطراف:
// - Department لها علاقة one-to-many مع User
// - User لها علاقة one-to-many مع Notification
// - User لها علاقة one-to-many مع Book (registered_by)
// - User لها علاقة one-to-many مع Attachment (uploaded_by)
// - User لها علاقة one-to-many مع Transaction (created_by)
// - User لها علاقة one-to-many مع Complaint (created_by, assigned_to)
// - User لها علاقة one-to-many مع Inquiry (created_by, assigned_to)

// علاقات متعددة (many-to-many) تحتاج إلى جداول وسيطة:
// - يمكن إضافة جدول وسيط لربط Book مع Department بشكل أكثر مرونة
// - يمكن إضافة جدول وسيط لربط User مع Department كعلاقة تاريخية

// علاقات بوليمورفيك (Polymorphic) يتم معالجتها في طبقة التطبيق:
// - Attachment يمكن أن يرتبط بـ Complaint, Inquiry, أو Book
// - Transaction يمكن أن يرتبط بـ Complaint أو Inquiry
// @pos Department -58 509
// @pos Transaction 588 1273
// @view -563 -1657 1.312
```