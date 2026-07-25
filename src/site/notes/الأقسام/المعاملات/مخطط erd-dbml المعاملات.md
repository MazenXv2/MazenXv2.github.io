---
{"dg-publish":true,"permalink":"/alaqsam/almeamlat/mkhtt-erd-dbml-almeamlat/","dg-note-properties":{}}
---

```dbml
// ============================================
// قسم المعاملات (Transactions Section)
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
  directorate_id int [note: 'Black Box (FK to external directorate)']
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
// ENTITY TABLES (Black Box)
// ============================================

Table citizen {
  citizen_id int [pk, increment]
  full_name varchar(255)
  note: 'Black Box (managed by external system)'
}

Table directorate {
  directorate_id int [pk, increment]
  name varchar(100)
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
// STATUS TRACKING
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
// REFERENCED TABLES (Black Box)
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
// RELATIONSHIPS (Foreign Keys)
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

// Entity Status Relationships
Ref: entity_status.status_id > status.status_id
Ref: entity_status.previous_status_id > status.status_id
// @view 509 142 0.388
// @size 1560 703
```