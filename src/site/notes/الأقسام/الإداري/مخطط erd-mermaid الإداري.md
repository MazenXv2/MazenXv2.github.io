---
{"dg-publish":true,"permalink":"/alaqsam/alidary/mkhtt-erd-mermaid-alidary/","dg-note-properties":{}}
---

```mermaid
erDiagram
    %% ============================================
    %% GLOBAL LOOKUP TABLES (Shared)
    %% ============================================
    status {
        int status_id PK
        varchar name
        varchar display_name
        text description
        varchar color_code
    }

    %% ============================================
    %% BLACK BOXES (Referenced by ID)
    %% ============================================
    user_black_box {
        int user_id PK
    }
    
    employee_black_box {
	    int employee_id
    }

    directorate_black_box {
        int directorate_id PK
    }

    %% ============================================
    %% AUDIT LOG (BLACK BOX)
    %% ============================================
    audit_log_black_box {
        int audit_log_id PK
        int user_id
    }

    %% ============================================
    %% ATTENDANCE (YOUR DESIGN)
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
    %% CIRCULARS (YOUR DESIGN)
    %% ============================================
    circular_proposal {
        int proposal_id PK
        varchar title
        text content
        text justification
        int proposed_by_user_id
        datetime sent_to_municipality_at
        text municipality_feedback
        text approved_version
        datetime approved_at
        int approved_by_directorate_id
        int final_circular_id
    }

    circular {
        int circular_id PK
        varchar circular_number
        varchar title
        text content
        date issue_date
        date effective_date
        int issued_by_user_id
    }

    %% ============================================
    %% UNIVERSAL STATUS TRACKING
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
    %% RELATIONSHIPS
    %% ============================================
    attendance_event }o--|| employee_black_box : ""

    circular_proposal }o--|| user_black_box : ""
    circular_proposal }o--|| directorate_black_box : ""
    circular_proposal |o--o| circular : ""

    circular }o--|| user_black_box : ""

    audit_log_black_box }o--|| user_black_box : ""

    entity_status }o--|| status : ""
    entity_status }o--|| circular_proposal : ""
```