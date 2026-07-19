---
{"dg-publish":true,"permalink":"/diagram/mkhtt-erd-dbml/","title":"Database Schema","dg-note-properties":{"title":"Database Schema"}}
---


```dbml
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
  type varchar
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

Table transactions {
  transaction_id integer [pk, increment]
  entity_type varchar
  entity_id integer
  created_date date
  field_71 varchar
}
// @pos external_systems 1065 775
// @pos users 165 597
// @pos user_permissions 41 316
// @pos role_permissions 315 84
// @pos department_permissions 479 502
// @view 193 65 0.661
```