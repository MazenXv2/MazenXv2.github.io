---
{"dg-publish":true,"permalink":"/alaqsam/almeamlat/mkhtt-erd-mermaid-almeamlat/","dg-note-properties":{}}
---

```mermaid
erDiagram
    %% ============================================
    %% TRANSACTIONS SECTION (MERGED)
    %% ============================================

    %% ============================================
    %% CORE LOOKUP TABLES
    %% ============================================
    transaction_type {
        int transaction_type_id PK
        varchar name
        varchar display_name
        text description
        boolean is_active
    }

    channel {
        int channel_id PK
    }

    status {
        int status_id PK
        varchar entity_type
    }
    
    transaction_fee {
        int transaction_fee_id PK
        int transaction_id
        decimal amount
        int payment_method_id
        datetime payment_date
        varchar receipt_number
        date payment_deadline
        text notes
    }

    %% ============================================
    %% BLACK BOX TABLES
    %% ============================================
    citizen_black_box {
        int citizen_id PK
    }

    directorate_black_box {
        int directorate_id PK
    }

    user_black_box {
        int user_id PK
    }

    department_black_box {
        int department_id PK
    }

    %% ============================================
    %% TRANSACTION TABLE (MERGED)
    %% ============================================
    transaction {
        int transaction_id PK
        varchar source_type
        int source_id
        int transaction_type_id
        int channel_id
        text description
        varchar address
        decimal latitude
        decimal longitude
        varchar reference_number
        int assigned_to_department_id
        int assigned_to_user_id
    }

    %% ============================================
    %% APPROVAL SYSTEM
    %% ============================================
    approval_workflow {
        int workflow_step_id PK
        int transaction_type_id
        int step_order
        varchar step_name
        int approving_directorate_id
        int approving_department_id
    }

    approval_history {
        int approval_history_id PK
        int transaction_id
        int workflow_step_id
        int approver_user_id
        text comment
    }

    %% ============================================
    %% STATUS TRACKING (Global)
    %% ============================================
    entity_status {
        int entity_status_id PK
        int entity_id
        int status_id
    }

    %% ============================================
    %% RELATIONSHIPS
    %% ============================================
    
    approval_workflow }o--|| transaction_type : ""
    
    
    transaction }o--|| transaction_type : ""
    transaction }o--|| channel : ""
    transaction }o--|| department_black_box : ""
    transaction }o--|| user_black_box : ""

    approval_history }o--|| approval_workflow : ""
    approval_history }o--|| user_black_box : ""
    approval_history }o--|| transaction : ""
    
    transaction_fee }o--|| transaction : ""

    entity_status }o--|| status : ""
    entity_status }o--|| status : ""
    entity_status }o--|| transaction : ""
    
    directorate_black_box ||--o{ transaction : ""
    
    citizen_black_box ||--o{ transaction : ""
```