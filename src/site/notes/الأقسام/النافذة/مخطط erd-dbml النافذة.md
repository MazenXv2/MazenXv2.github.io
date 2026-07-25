---
{"dg-publish":true,"permalink":"/alaqsam/alnafdht/mkhtt-erd-dbml-alnafdht/","dg-note-properties":{}}
---

```dbml
// ============================================
// قسم النافذة (Window Unit)
// استقبال شكاوى واستفسارات المواطن
// واستقبال كتب ومراسلات المديريات الأخرى
// ============================================

// ============================================
// الجداول الأساسية
// ============================================

// قنوات الاستقبال
Table channel {
  channel_id int [pk, increment]
  name varchar(50)
  display_name varchar(100)
  description text
  is_active boolean
}

// أنواع المعاملات
Table transaction_type {
  transaction_type_id int [pk, increment]
  name varchar(50)
  display_name varchar(100)
  description text
  is_active boolean
  directorate_id int [note: 'Black Box: معرف المديرية']
}

// الحالات العامة
Table status {
  status_id int [pk, increment]
  name varchar(50)
  display_name varchar(100)
  description text
  color_code varchar(7)
}

// ============================================
// معاملات المواطن
// ============================================

// معاملة المواطن (الشكوى / الاستفسار)
Table citizen_transaction {
  citizen_transaction_id int [pk, increment]
  transaction_type_id int [ref: > transaction_type.transaction_type_id]
  citizen_id int [note: 'Black Box: معرف المواطن من النظام الخارجي']
  citizen_name varchar(255)
  citizen_phone varchar(20)
  citizen_email varchar(100)
  citizen_id_number varchar(50)
  description text
  location varchar(255)
  latitude decimal(10,8)
  longitude decimal(11,8)
  source_directorate_id int [note: 'Black Box: معرف المديرية المحولة (إن وجد)']
  channel_id int [ref: > channel.channel_id]
  assigned_to_department_id int [ref: > department.department_id]
  assigned_to_user_id int [ref: > user.user_id]
}

// ============================================
// معاملات المديريات الأخرى
// ============================================

// معاملة مديرية أخرى (كتاب وارد، طلب تنسيق)
Table directorate_transaction {
  directorate_transaction_id int [pk, increment]
  transaction_type_id int [ref: > transaction_type.transaction_type_id]
  source_directorate_id int [note: 'Black Box: معرف المديرية المرسلة']
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
// المرفقات والإشعارات والرسوم
// ============================================

// المرفقات (صور، مستندات) — مرتبطة بأي كيان
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

// الإشعارات المرسلة للمواطن أو المستخدم
Table notification {
  notification_id int [pk, increment]
  recipient_user_id int [ref: > user.user_id]
  recipient_citizen_id int [note: 'Black Box: معرف المواطن']
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

// الرسوم المالية (إذا كانت المعاملة تتطلب دفع رسوم)
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
// تتبع الحالة (Universal Status)
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
// العلاقات (Foreign Keys)
// ============================================
Ref: citizen_transaction.transaction_type_id > transaction_type.transaction_type_id
Ref: citizen_transaction.channel_id > channel.channel_id

Ref: directorate_transaction.transaction_type_id > transaction_type.transaction_type_id
Ref: directorate_transaction.parent_transaction_id > directorate_transaction.directorate_transaction_id
Ref: directorate_transaction.channel_id > channel.channel_id

// العلاقات العامة
Ref: attachment.entity_id > citizen_transaction.citizen_transaction_id
Ref: attachment.entity_id > directorate_transaction.directorate_transaction_id

Ref: notification.entity_id > citizen_transaction.citizen_transaction_id
Ref: notification.entity_id > directorate_transaction.directorate_transaction_id

Ref: transaction_fee.entity_id > citizen_transaction.citizen_transaction_id
Ref: transaction_fee.entity_id > directorate_transaction.directorate_transaction_id

Ref: entity_status.entity_id > citizen_transaction.citizen_transaction_id
Ref: entity_status.entity_id > directorate_transaction.directorate_transaction_id
Ref: entity_status.status_id > status.status_id
Ref: entity_status.previous_status_id > status.status_id
// @view 558 15 0.512
// @size 1767 762
```