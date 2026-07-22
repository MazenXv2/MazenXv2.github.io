---
{"dg-publish":true,"permalink":"/diagram/mkhtt-erd-dbml/","title":"Database Schema","dg-note-properties":{"title":"Database Schema"}}
---


```dbml
// =============================================
// 1. CORE STRUCTURE (Security & Organization)
// =============================================

Table modules {
  module_id integer [pk, increment]
  module_name varchar
  parent_module_id integer [ref: > modules.module_id]
  icon varchar
  route varchar
  display_order integer
  is_active boolean
  description text
  created_at timestamp
  updated_at timestamp
}

Table departments {
  department_id integer [pk, increment]
  name varchar
  parent_id integer [ref: > departments.department_id]
  manager_user_id integer [ref: > users.user_id]
  description text
}

Table roles {
  role_id integer [pk, increment]
  role_name varchar
  description varchar
  created_at timestamp
  updated_at timestamp
}

Table permissions {
  permission_id integer [pk, increment]
  module_id integer [ref: > modules.module_id] // NULL for resources
  name varchar // e.g., "view_requests" or "user_management"
  type varchar // 'action' or 'resource'
  description varchar
  created_at timestamp
  updated_at timestamp
}

Table external_systems {
  external_system_id integer [pk, increment]
  system_name varchar
  api_endpoint varchar
  auth_method varchar
}

Table users {
  user_id integer [pk, increment]
  username varchar
  password_hash varchar
  email varchar
  phone varchar
  role_id integer [ref: > roles.role_id]
  department_id integer [ref: > departments.department_id]
  external_system_id integer [ref: > external_systems.external_system_id]
  created_at timestamp
  last_login timestamp
}

Table user_permissions {
  user_id integer [pk, ref: > users.user_id]
  permission_id integer [pk, ref: > permissions.permission_id]
  granted_by_user_id integer [ref: > users.user_id]
  granted_at timestamp
  start_date date
  end_date date
}

Table role_permissions {
  role_id integer [pk, ref: > roles.role_id]
  permission_id integer [pk, ref: > permissions.permission_id]
  can_grant boolean
}

Table department_permissions {
  department_id integer [pk, ref: > departments.department_id]
  permission_id integer [pk, ref: > permissions.permission_id]
  can_grant boolean
}

// =============================================
// 2. REQUESTER ENTITIES (Master Data)
// =============================================

Table citizens {
  citizen_id integer [pk, increment]
  full_name varchar
  national_id varchar [unique]
  phone varchar
  email varchar
  address text
  created_at timestamp
  updated_at timestamp
}

Table external_companies {
  company_id integer [pk, increment]
  name varchar
  cr_number varchar [unique] // Commercial Registration Number
  phone varchar
  email varchar
  address text
  contact_person varchar
  created_at timestamp
  updated_at timestamp
}

Table government_agencies {
  agency_id integer [pk, increment]
  name varchar
  code varchar [unique] // Agency code
  contact_person varchar
  phone varchar
  email varchar
  created_at timestamp
  updated_at timestamp
}

// =============================================
// 3. CITIZEN REQUESTS (Polymorphic)
// =============================================

Table request_categories {
  category_id integer [pk, increment]
  category_name varchar
  description varchar
  default_department_id integer [ref: > departments.department_id]
  sla_hours integer
}

Table channels {
  channel_id integer [pk, increment]
  channel_name varchar
}

Table request_statuses {
  status_id integer [pk, increment]
  status_name varchar
  color_hex varchar
}

Table citizen_requests {
  request_id integer [pk, increment]
  requester_type varchar // 'citizen', 'employee', 'company', 'agency', 'anonymous'
  requester_id integer // ID in the respective table
  category_id integer [ref: > request_categories.category_id]
  channel_id integer [ref: > channels.channel_id]
  status_id integer [ref: > request_statuses.status_id]
  created_by_user_id integer [ref: > users.user_id] // Employee who registered it
  assigned_to_department_id integer [ref: > departments.department_id]
  assigned_to_user_id integer [ref: > users.user_id] // Employee assigned to handle it
  description text
  resolution_notes text
  created_at timestamp
  updated_at timestamp
  resolved_at timestamp
}

Table request_followups {
  followup_id integer [pk, increment]
  request_id integer [ref: > citizen_requests.request_id]
  user_id integer [ref: > users.user_id]
  note text
  old_status varchar
  new_status varchar
  created_at timestamp
}

// =============================================
// 4. INCOMING EMAILS
// =============================================

Table incoming_emails {
  email_id integer [pk, increment]
  sender_email varchar
  sender_name varchar
  subject varchar
  body text
  attachment_links text
  department_id integer [ref: > departments.department_id]
  assigned_to_user_id integer [ref: > users.user_id]
  status_id integer [ref: > request_statuses.status_id]
  classification varchar
  response_body text
  received_at timestamp
  responded_at timestamp
  created_at timestamp
  updated_at timestamp
}

// =============================================
// 5. DIGITAL ARCHIVE
// =============================================

Table archived_documents {
  document_id integer [pk, increment]
  document_number varchar
  title varchar
  source varchar
  document_type varchar
  description text
  file_path varchar
  file_hash varchar
  file_size bigint
  uploaded_by_user_id integer [ref: > users.user_id]
  department_id integer [ref: > departments.department_id]
  received_date date
  archive_date timestamp
  is_digital boolean
}

Table document_access_control {
  document_id integer [pk, ref: > archived_documents.document_id]
  role_id integer [pk, ref: > roles.role_id]
  can_view boolean
  can_download boolean
}

// =============================================
// 6. NOTIFICATIONS
// =============================================

Table notifications {
  notification_id integer [pk, increment]
  user_id integer [ref: > users.user_id]
  request_id integer [ref: > citizen_requests.request_id]
  email_id integer [ref: > incoming_emails.email_id]
  citizen_id integer [ref: > citizens.citizen_id]
  recipient_type varchar // 'employee' or 'citizen'
  recipient_contact varchar // Snapshot of phone/email at sending time
  channel_type varchar // 'whatsapp', 'sms', 'email'
  subject varchar
  body text
  is_sent boolean
  sent_at timestamp
  created_at timestamp
}

// =============================================
// 7. AUDIT LOG
// =============================================

Table transactions {
  transaction_id integer [pk, increment]
  entity_type varchar // e.g., 'citizen_request', 'incoming_email', 'document'
  entity_id integer
  created_date date
  field_71 varchar // Reserved for extra data
}
// @view -176 -41 0.629
// @size 1737 662
```