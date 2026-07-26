---
{"dg-publish":true,"permalink":"/alaqsam/alrkhs/mkhtt-erd-dbml-alrkhs/","dg-note-properties":{}}
---

```dbml
// ============================================
// قسم الرخص (Permits Section)
// ============================================

// ============================================
// CORE LOOKUP
// ============================================

Table permit_type {
  permit_type_id int [pk, increment]
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
// TRANSACTION TABLES (Source - Black Box references)
// ============================================

Table citizen_transaction {
  citizen_transaction_id int [pk, increment]
  transaction_type_id int
  citizen_id int [note: 'Black Box (FK to citizen)']
  citizen_name varchar(255)
  description text
  location varchar(255)
  note: 'Referenced by permit.source_id when source_type = "citizen_transaction"'
}

Table directorate_transaction {
  directorate_transaction_id int [pk, increment]
  transaction_type_id int
  source_directorate_id int [note: 'Black Box (FK to directorate)']
  reference_number varchar(100)
  description text
  location varchar(255)
  note: 'Referenced by permit.source_id when source_type = "directorate_transaction"'
}

// ============================================
// PERMIT TABLES
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

// ============================================
// RELATIONSHIPS
// ============================================

Ref: permit.permit_type_id > permit_type.permit_type_id
Ref: permit.issued_by_user_id > user.user_id

// Polymorphic tracking (conceptual, not a direct FK)
// permit.source_id → citizen_transaction.citizen_transaction_id OR directorate_transaction.directorate_transaction_id
// permit.source_type = 'citizen_transaction' or 'directorate_transaction'

Ref: entity_status.status_id > status.status_id
Ref: entity_status.previous_status_id > status.status_id
// @view 399 58 0.903
// @size 1580 768
```