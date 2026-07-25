---
{"dg-publish":true,"permalink":"/diagram/mkhtt-erd-mermaid/","dg-note-properties":{}}
---

```mermaid
erDiagram
    %% ============================================
    %% CORE LOOKUP TABLES
    %% ============================================
    status {
        int status_id PK
        varchar name
        varchar display_name
        text description
        varchar color_code
    }

    channel {
        int channel_id PK
        varchar name
        varchar display_name
        text description
        boolean is_active
    }

    transaction_type {
        int transaction_type_id PK
        varchar name
        varchar display_name
        text description
        boolean is_active
        int directorate_id
    }

    permit_type {
        int permit_type_id PK
        varchar name
        varchar display_name
        text description
        boolean is_active
    }

    fee_type {
        int fee_type_id PK
        varchar name
        varchar display_name
        text description
        boolean is_active
    }

    payment_method {
        int payment_method_id PK
        varchar name
        varchar display_name
        text description
        boolean is_active
    }

    %% ============================================
    %% USERS, STRUCTURE & SECURITY
    %% ============================================
    user {
        int user_id PK
        varchar username
        varchar password_hash
        varchar email
        varchar phone
        int role_id
        int department_id
        int directorate_id
        boolean is_active
        datetime last_login
    }

    role {
        int role_id PK
        varchar role_name
        text description
    }

    department {
        int department_id PK
        varchar name
        int parent_id
        int manager_user_id
        text description
        int directorate_id
    }

    resource {
        int resource_id PK
        varchar name
        text description
        varchar module
    }

    permission {
        int permission_id PK
        varchar name
        text description
        int resource_id
        varchar action
    }

    role_permission {
        int role_id PK
        int permission_id PK
        boolean can_grant
    }

    user_permission {
        int user_id PK
        int permission_id PK
        int granted_by_user_id
        datetime granted_at
        datetime start_date
        datetime end_date
    }

    department_permission {
        int department_id PK
        int permission_id PK
    }

    %% ============================================
    %% ATTENDANCE
    %% ============================================
    attendance_event {
        int event_id PK
        int user_id
        timestamp event_time
        varchar event_type
        int task_id
        boolean go_home_directly
        text notes
    }

    %% ============================================
    %% TRANSACTIONS
    %% ============================================
    citizen_transaction {
        int citizen_transaction_id PK
        int transaction_type_id
        int citizen_id
        varchar citizen_name
        varchar citizen_phone
        varchar citizen_email
        varchar citizen_id_number
        text description
        varchar location
        decimal latitude
        decimal longitude
        int source_directorate_id
        int channel_id
        int assigned_to_department_id
        int assigned_to_user_id
    }

    directorate_transaction {
        int directorate_transaction_id PK
        int transaction_type_id
        int source_directorate_id
        varchar reference_number
        text description
        varchar location
        decimal latitude
        decimal longitude
        int parent_transaction_id
        int channel_id
        int assigned_to_department_id
        int assigned_to_user_id
    }

    %% ============================================
    %% APPROVAL SYSTEM
    %% ============================================
    approval_workflow {
        int workflow_step_id PK
        varchar entity_type
        int entity_type_id
        int step_order
        varchar step_name
        int approving_directorate_id
        int approving_department_id
    }

    approval_history {
        int approval_history_id PK
        varchar entity_type
        int entity_id
        int workflow_step_id
        int approver_user_id
        text comment
    }

    %% ============================================
    %% WAREHOUSE
    %% ============================================
    purchase_request {
        int purchase_request_id PK
        int material_id
        decimal requested_quantity
        decimal current_stock
        varchar suggested_supplier
        decimal estimated_cost
        int requested_by_user_id
        datetime director_approved_at
        datetime completed_at
        int budget_allocation_id
        decimal quantity_purchased
    }

    inventory_audit_cycle {
        int audit_cycle_id PK
        varchar cycle_name
        date start_date
        date end_date
        int approved_by_user_id
        text notes
    }

    inventory_audit_item {
        int audit_item_id PK
        int audit_cycle_id
        int material_id
        decimal system_quantity
        decimal physical_quantity
        text notes
    }

    %% ============================================
    %% PERMITS
    %% ============================================
    permit {
        int permit_id PK
        enum source_type
        int source_id
        int permit_type_id
        varchar permit_number
        date issue_date
        date expiry_date
        int issued_by_user_id
        varchar location
        decimal latitude
        decimal longitude
        text detailed_description
        varchar qr_code
    }

    %% ============================================
    %% SHARED / CROSS-MODULE
    %% ============================================
    attachment {
        int attachment_id PK
        varchar entity_type
        int entity_id
        varchar file_name
        varchar file_path
        int file_size
        varchar mime_type
        text description
        int uploaded_by_user_id
    }

    notification {
        int notification_id PK
        int recipient_user_id
        int recipient_citizen_id
        varchar recipient_phone
        varchar recipient_email
        varchar entity_type
        int entity_id
        varchar subject
        text message
        int channel_id
        enum delivery_status
        boolean is_read
        datetime read_at
        datetime sent_at
    }

    transaction_fee {
        int transaction_fee_id PK
        enum entity_type
        int entity_id
        int fee_type_id
        decimal amount
        int payment_method_id
        datetime payment_date
        varchar receipt_number
        date due_date
        text notes
    }

    %% ============================================
    %% UNIVERSAL STATUS SYSTEM
    %% ============================================
    entity_status {
        int entity_status_id PK
        varchar entity_type
        int entity_id
        int status_id
        int previous_status_id
        text comment
    }

    %% ============================================
    %% RELATIONSHIPS (Foreign Keys)
    %% ============================================
    user }o--|| role : "has role"
    user }o--|| department : "belongs to"
    attendance_event }o--|| user : "belongs to"
    role_permission }o--|| role : "links to"
    role_permission }o--|| permission : "links to"
    user_permission }o--|| user : "assigned to"
    user_permission }o--|| permission : "has permission"
    user_permission }o--|| user : "granted by"
    department_permission }o--|| department : "assigned to"
    department_permission }o--|| permission : "has permission"
    permission }o--|| resource : "belongs to"

    citizen_transaction }o--|| transaction_type : "has type"
    citizen_transaction }o--|| channel : "submitted via"
    citizen_transaction }o--|| department : "assigned to dept"
    citizen_transaction }o--|| user : "assigned to user"

    directorate_transaction }o--|| transaction_type : "has type"
    directorate_transaction }o--|| channel : "received via"
    directorate_transaction }o--|| directorate_transaction : "parent refers to"
    directorate_transaction }o--|| department : "assigned to dept"
    directorate_transaction }o--|| user : "assigned to user"

    approval_history }o--|| approval_workflow : "follows step"
    approval_history }o--|| user : "approved by"

    purchase_request }o--|| user : "requested by"

    inventory_audit_item }o--|| inventory_audit_cycle : "belongs to cycle"
    inventory_audit_cycle }o--|| user : "approved by"

    permit }o--|| permit_type : "has type"
    permit }o--|| user : "issued by"

    attachment }o--|| user : "uploaded by"

    notification }o--|| user : "sent to internal"
    notification }o--|| channel : "sent via"

    transaction_fee }o--|| fee_type : "has type"
    transaction_fee }o--|| payment_method : "paid via"

    entity_status }o--|| status : "has status"
    entity_status }o--|| status : "had previous status"
```