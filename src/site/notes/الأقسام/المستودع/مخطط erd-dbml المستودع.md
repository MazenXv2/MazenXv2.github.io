---
{"dg-publish":true,"permalink":"/alaqsam/almstwde/mkhtt-erd-dbml-almstwde/","dg-note-properties":{}}
---

```dbml
// ============================================
// قسم المستودع (Warehouse Section)
// ============================================

// ============================================
// CORE LOOKUP
// ============================================

Table status {
  status_id int [pk, increment]
  name varchar(50)
  display_name varchar(100)
  description text
  color_code varchar(7)
}

// ============================================
// PURCHASE REQUESTS
// ============================================

Table purchase_request {
  purchase_request_id int [pk, increment]
  material_id int [note: 'Black Box (FK to material)']
  requested_quantity decimal(10,2)
  current_stock decimal(10,2)
  suggested_supplier varchar(255)
  estimated_cost decimal(10,2)
  requested_by_user_id int [ref: > user.user_id]
  director_approved_at timestamp
  completed_at timestamp
  budget_allocation_id int [note: 'Black Box (FK to budget_allocation)']
  quantity_purchased decimal(10,2)
}

// ============================================
// INVENTORY AUDIT
// ============================================

Table inventory_audit_cycle {
  audit_cycle_id int [pk, increment]
  cycle_name varchar(100)
  start_date date
  end_date date
  approved_by_user_id int [ref: > user.user_id]
  notes text
}

Table inventory_audit_item {
  audit_item_id int [pk, increment]
  audit_cycle_id int [ref: > inventory_audit_cycle.audit_cycle_id]
  material_id int [note: 'Black Box (FK to material)']
  system_quantity decimal(10,2)
  physical_quantity decimal(10,2)
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

// ============================================
// RELATIONSHIPS
// ============================================

Ref: purchase_request.requested_by_user_id > user.user_id

Ref: inventory_audit_item.audit_cycle_id > inventory_audit_cycle.audit_cycle_id
Ref: inventory_audit_cycle.approved_by_user_id > user.user_id

// Entity Status (Polymorphic)
Ref: entity_status.status_id > status.status_id
Ref: entity_status.previous_status_id > status.status_id
// @pos purchase_request 672 19
// @view 238 112 1.057
// @size 1567 781
```