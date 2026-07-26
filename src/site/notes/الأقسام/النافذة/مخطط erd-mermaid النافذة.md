---
{"dg-publish":true,"permalink":"/alaqsam/alnafdht/mkhtt-erd-mermaid-alnafdht/","dg-note-properties":{}}
---

```mermaid
erDiagram
    %% ============================================
    %% CORE LOOKUP TABLES
    %% ============================================
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

    status {
        int status_id PK
        varchar name
        varchar display_name
        text description
        varchar color_code
    }

    %% ============================================
    %% ENTITY TABLES (Black Box)
    %% ============================================
    citizen {
        int citizen_id PK
        varchar full_name
        varchar phone
        varchar email
        varchar id_number
    }

    directorate {
        int directorate_id PK
        varchar name
        varchar phone
        varchar email
    }

    %% ============================================
    %% TRANSACTION TABLES (Core)
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
    %% SHARED / CROSS-MODULE TABLES
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
        varchar delivery_status
        boolean is_read
        datetime read_at
        datetime sent_at
    }

    transaction_fee {
        int transaction_fee_id PK
        varchar entity_type
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
    %% STATUS TRACKING (Global)
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
    %% REFERENCED TABLES (Black Box - For Context)
    %% ============================================
    user {
        int user_id PK
        varchar username
    }

    department {
        int department_id PK
        varchar name
    }

    %% ============================================
    %% RELATIONSHIPS
    %% ============================================
    citizen ||--o{ citizen_transaction : "يقدم"
    directorate ||--o{ directorate_transaction : "يرسل"

    citizen_transaction }o--|| transaction_type : "has type"
    citizen_transaction }o--|| channel : "submitted via"
    citizen_transaction }o--|| department : "assigned to dept"
    citizen_transaction }o--|| user : "assigned to user"

    directorate_transaction }o--|| transaction_type : "has type"
    directorate_transaction }o--|| channel : "received via"
    directorate_transaction }o--|| department : "assigned to dept"
    directorate_transaction }o--|| user : "assigned to user"

    attachment }o--|| citizen_transaction : "attached to (polymorphic)"
    attachment }o--|| directorate_transaction : "attached to (polymorphic)"

    notification }o--|| citizen_transaction : "sent for (polymorphic)"
    notification }o--|| directorate_transaction : "sent for (polymorphic)"

    transaction_fee }o--|| citizen_transaction : "linked to (polymorphic)"
    transaction_fee }o--|| directorate_transaction : "linked to (polymorphic)"

    entity_status }o--|| status : "has status"
    entity_status }o--|| status : "had previous status"
    entity_status ||--o{ citizen_transaction : "tracks"
    entity_status ||--o{ directorate_transaction : "tracks"
```