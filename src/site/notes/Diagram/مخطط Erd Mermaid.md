---
{"dg-publish":true,"permalink":"/diagram/mkhtt-erd-mermaid/","dg-note-properties":{}}
---


```mermaid
erDiagram
    %% ============================================
    %% 1. مجموعة الديوان - الكيانات الأساسية
    %% ============================================

    User {
        int user_id PK
        string username
        string password_hash
        string email
        string phone
        int role_id FK
        int department_id FK
        boolean is_active
        datetime created_at
        datetime last_login
    }

    Role {
        int role_id PK
        string role_name
        string description
        datetime created_at
    }

    Department {
        int department_id PK
        string department_name
        int parent_department_id FK
        int manager_id FK
        string description
    }

    Book {
        int book_id PK
        string book_number
        date book_date
        string direction
        string type
        int sender_department_id FK
        int receiver_department_id FK
        string subject
        text content
        string file_url
        int registered_by FK
        string status
        datetime created_at
        datetime updated_at
    }

    Complaint {
        int complaint_id PK
        string external_citizen_id
        string citizen_name
        string citizen_phone
        string citizen_email
        string citizen_id_number
        string complaint_type
        string subject
        text description
        string file_url
        string status
        string priority
        string source
        datetime submitted_at
        datetime resolved_at
        int assigned_to FK
        int created_by FK
    }

    Inquiry {
        int inquiry_id PK
        string external_citizen_id
        string citizen_name
        string citizen_phone
        string citizen_email
        string inquiry_type
        string subject
        text description
        string status
        string source
        datetime submitted_at
        datetime responded_at
        int assigned_to FK
        int created_by FK
    }

    Attachment {
        int attachment_id PK
        string entity_type
        int entity_id FK
        string file_name
        string file_url
        string file_type
        int uploaded_by FK
        datetime uploaded_at
    }

    Notification {
        int notification_id PK
        int user_id FK
        string notification_type
        string message
        boolean is_read
        string sent_via
        datetime sent_at
        datetime read_at
    }

    Transaction {
        int transaction_id PK
        string transaction_number
        string transaction_type
        string entity_type
        int entity_id FK
        date created_date
        int assigned_to FK
        string status
        int created_by FK
    }

    External_System {
        string system_name PK
        string api_endpoint
        string auth_method
    }

    %% ============================================
    %% 3. العلاقات بين الكيانات
    %% ============================================

    %% User relationships
    User }o--|| Role : "has role"
    User }o--|| Department : "belongs to"
    User ||--o{ Notification : "receives"
    User ||--o{ Book : "registers"
    User ||--o{ Attachment : "uploads"
    User ||--o{ Transaction : "creates"
    User ||--o{ Complaint : "creates"
    User ||--o{ Inquiry : "creates"

    %% Role relationships
    Role ||--o{ User : "assigned to"

    %% Department relationships
    Department ||--o{ User : "contains"
    Department ||--o{ Department : "has sub-department"
    Department ||--o{ Book : "sends/receives"
    Department ||--o{ Complaint : "processes"
    Department ||--o{ Inquiry : "processes"
    Department ||--o{ Transaction : "processes"

    %% Book relationships
    Book }o--|| Department : "sent by"
    Book }o--|| Department : "received by"
    Book }o--|| User : "registered by"

    %% Complaint relationships
    Complaint }o--|| User : "assigned to"
    Complaint }o--|| User : "created by"
    Complaint }o--|| Transaction : "leads to"

    %% Inquiry relationships
    Inquiry }o--|| User : "assigned to"
    Inquiry }o--|| User : "created by"
    Inquiry }o--|| Transaction : "leads to"

    %% Attachment relationships (polymorphic)
    Attachment }o--|| Complaint : "attached to"
    Attachment }o--|| Inquiry : "attached to"
    Attachment }o--|| Book : "attached to"
    Attachment }o--|| User : "uploaded by"

    %% Transaction relationships
    Transaction }o--|| User : "assigned to"
    Transaction }o--|| User : "created by"

    %% Notification relationships
    Notification }o--|| User : "sent to"

    %% External System relationships (logical)
    External_System ||--o{ Complaint : "sends via API"
    External_System ||--o{ Inquiry : "sends via API"

    %% ============================================
    %% 4. ملاحظات إضافية للعلاقات
    %% ============================================
    %% - علاقات متعددة الأطراف (One-to-Many)
    %% - علاقات بوليمورفيك (Polymorphic) يتم معالجتها في طبقة التطبيق
    %% - علاقات متعددة (Many-to-Many) تحتاج إلى جداول وسيطة
```