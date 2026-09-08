---
{"dg-publish":true,"permalink":"/alaqsam/alrkhs/mkhtt-erd-mermaid-alrkhs/","dg-note-properties":{}}
---

```mermaid
erDiagram
    %% ============================================
    %% PERMIT SECTION (Hybrid Design)
    %% ============================================

    %% ============================================
    %% CORE LOOKUP
    %% ============================================
    permit_type {
        int permit_type_id PK
        varchar name
        varchar display_name
        text description
        boolean is_active
        jsonb fields_schema   "Defines dynamic fields: [{'key':'depth','label':'Depth','type':'decimal'}]"
        int version           "Tracks schema changes"
    }

    %% ============================================
    %% TRANSACTION TABLES (Source)
    %% ============================================
    transaction {
        int transaction_id PK
        int transaction_type_id
    }

    %% ============================================
    %% PERMIT TABLES (Core)
    %% ============================================
    permit {
        int permit_id PK
        varchar source_type          "'transaction' (polymorphic)"
        int source_id               "transaction.transaction_id"
        int permit_type_id          "FK → permit_type"
        varchar permit_number
        date issue_date
        date expiry_date
        int issued_by_user_id
        varchar location
        decimal latitude
        decimal longitude
        text detailed_description
        varchar qr_code
        jsonb custom_values         "Actual data: {'depth': 5.5, 'area': 200}"
        jsonb status_history        "Audit trail: [{'status_id':1, 'changed_at':'...', 'comment':'Submitted'}]"
        int current_status_id       "Optional: denormalized for fast lookup (last status_id)"
    }

    %% ============================================
    %% REFERENCED TABLES (Black Box)
    %% ============================================
    user_black_box {
        int user_id PK
        varchar username
    }

    %% ============================================
    %% RELATIONSHIPS
    %% ============================================
    permit }o--|| permit_type : "has type"
    permit }o--|| user_black_box : "issued by"
    permit }o--|| transaction : "originates from"
```