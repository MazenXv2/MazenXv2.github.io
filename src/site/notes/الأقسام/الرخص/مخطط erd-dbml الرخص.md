---
{"dg-publish":true,"permalink":"/alaqsam/alrkhs/mkhtt-erd-dbml-alrkhs/","dg-note-properties":{}}
---

```dbml
// ============================================
// قسم الرخص (Permits Section) - Hybrid Design
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
  fields_schema jsonb [not null, default: '[]']
  // Defines dynamic fields: [{"key":"depth","label":"Depth","type":"decimal"}]
  version int [default: 1]
  note: 'fields_schema defines the dynamic fields for this permit type'
}

// ============================================
// TRANSACTION TABLES (Source)
// ============================================

Table transaction {
  transaction_id int [pk, increment]
  note: 'Black Box (references transaction_module_bb)'
}

// ============================================
// PERMIT TABLES (Core)
// ============================================

Table permit {
  permit_id int [pk, increment]
  transaction_id int [not null, ref: > transaction.transaction_id]
  permit_type_id int [not null, ref: > permit_type.permit_type_id]
  
  // Core fields (still relational for fast filtering)
  permit_number varchar(50)
  issue_date date
  expiry_date date
  issued_by_user_id int [ref: > user_black_box.user_id]
  location varchar(255)
  latitude decimal(10,8)
  longitude decimal(11,8)
  
  // ----- JSONB Payloads (Dynamic/Flexible Data) -----
  custom_values jsonb [not null, default: '{}']
  // Actual data: {"depth": 5.5, "area": 200, "description": "Excavation near school", "qr_code": "https://..."}
  
  status_history jsonb [not null, default: '[]']
  // Full audit trail: [{"status_id": 2, "status_name": "active", "changed_at": "...", "changed_by_user_id": 5, "comment": "Permit issued"}]
  
  // Optional: denormalized for fast lookup (last status_id)
  current_status_id int [null]
  note: 'current_status_id is denormalized for performance. It should always match the last status_id in status_history.'
}

// ============================================
// REFERENCED TABLES (Black Box)
// ============================================

Table user_black_box {
  user_id int [pk, increment]
  username varchar(50)
  note: 'Black Box (managed by authentication system)'
}

// ============================================
// RELATIONSHIPS
// ============================================

Ref: permit.permit_type_id > permit_type.permit_type_id
Ref: permit.issued_by_user_id > user_black_box.user_id
Ref: permit.transaction_id > transaction.transaction_id

// ============================================
// INDEX RECOMMENDATIONS
// ============================================

/*
-- Index for current status ID (last element)
CREATE INDEX idx_permit_current_status ON permit (((status_history -> -1) ->> 'status_id'));

-- Index for current status name
CREATE INDEX idx_permit_current_status_name ON permit (((status_history -> -1) ->> 'status_name'));

-- Index for expiry date (for automated expiration checks)
CREATE INDEX idx_permit_expiry ON permit (expiry_date) WHERE expiry_date IS NOT NULL;

-- GIN index for custom_values (if searching within)
CREATE INDEX idx_permit_custom_gin ON permit USING GIN (custom_values);

-- GIN index for status_history (if searching past statuses)
CREATE INDEX idx_permit_status_gin ON permit USING GIN (status_history);

-- Composite index for active permits (for dashboard queries)
CREATE INDEX idx_permit_active ON permit (permit_type_id, current_status_id) WHERE current_status_id = 2;
*/
// @view 348 55 1.226
// @size 1480 714
```