# Database

De database vormt de centrale waarheid van Workspace Workforce.

## Principes

- Relationeel ontwerp
- PostgreSQL als productiestandaard
- SQLite alleen voor prototypes
- Elke kritieke tabel heeft created_at en updated_at
- Soft-delete waar passend
- Auditlogs voor kritieke wijzigingen
- Tenant_id voorbereid voor SaaS
- Rechten worden databasegestuurd
- Exports worden als batches opgeslagen

## Kernentiteiten

- organizations
- users
- roles
- permissions
- role_permissions
- menu_items
- employees
- clients
- locations
- contracts
- fixed_schedules
- daily_planning
- time_entries
- weekly_timesheets
- documents
- payslips
- export_batches
- send_logs
- audit_logs
- settings
- notifications
- automation_runs

## Statussen

### Urenstatus

- concept
- ingevoerd
- gecontroleerd
- goedgekeurd
- afgekeurd
- geëxporteerd
- heropend

### Documentstatus

- ontbreekt
- geüpload
- geldig
- verlopen
- afgekeurd
- vervangen
- gearchiveerd

### Weekstaatstatus

- open
- compleet
- gecontroleerd
- goedgekeurd
- verzonden
- geëxporteerd
- heropend

## Auditlog

Auditlogs bevatten:

- actor_user_id
- entity_type
- entity_id
- action
- old_value
- new_value
- metadata
- ip_address
- user_agent
- created_at
