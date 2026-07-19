---
{"dg-publish":true,"permalink":"/diagram/mkhtt-erd-mermaid/","dg-note-properties":{}}
---


```mermaid
erDiagram
    departments {
        int department_id PK
        varchar name
        int parent_id FK
        int manager_user_id FK
        text description
    }

    roles {
        int role_id PK
        varchar role_name
        varchar description
        timestamp created_at
        timestamp updated_at
    }

    permissions {
        int permission_id PK
        varchar type
        varchar description
        timestamp created_at
        timestamp updated_at
    }

    external_systems {
        int external_system_id PK
        varchar system_name
        varchar api_endpoint
        varchar auth_method
    }

    users {
        int user_id PK
        varchar username
        varchar password_hash
        varchar email
        varchar phone
        int role_id FK
        int department_id FK
        int external_system_id FK
        timestamp created_at
        timestamp last_login
    }

    user_permissions {
        int user_id PK FK
        int permission_id PK FK
        int granted_by_user_id FK
        timestamp granted_at
        date start_date
        date end_date
    }

    role_permissions {
        int role_id PK FK
        int permission_id PK FK
        boolean can_grant
    }

    department_permissions {
        int department_id PK FK
        int permission_id PK FK
        boolean can_grant
    }

    transactions {
        int transaction_id PK
        varchar entity_type
        int entity_id
        date created_date
        varchar field_71
    }

    %% Relationships
    departments ||--o{ users : "has"
    roles ||--o{ users : "assigns"
    external_systems ||--o{ users : "authenticates via"
    departments ||--o{ departments : "has parent"
    users ||--o{ user_permissions : "receives permission from"
    permissions ||--o{ user_permissions : "defines"
    users ||--o{ user_permissions : "granted by"
    roles ||--o{ role_permissions : "inherits"
    permissions ||--o{ role_permissions : "applies to role"
    departments ||--o{ department_permissions : "inherits"
    permissions ||--o{ department_permissions : "applies to dept"
```