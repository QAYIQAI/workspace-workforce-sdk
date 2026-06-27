# Supabase

Supabase kan worden gebruikt als managed backend voor Workspace Workforce.

## Onderdelen

- Supabase PostgreSQL
- Supabase Auth
- Supabase Storage
- Row Level Security
- Edge Functions
- Realtime optioneel

## Gebruik

Supabase is geschikt voor:

- Snelle SaaS-start
- Auth zonder eigen loginserver
- Managed database
- Documentstorage
- API via REST
- RLS-gebaseerde tenantbeveiliging

## RLS-principes

- Elke tenantgebonden tabel bevat tenant_id.
- Gebruikers mogen alleen records van hun tenant zien.
- Service role key wordt nooit in frontend gebruikt.
- Frontend gebruikt alleen publishable/anon key.
- Backend of Edge Functions verwerken service-acties.

## Storage

Gevoelige documenten worden niet publiek gemaakt.

Toegang verloopt via:

- Signed URLs met korte levensduur
- Backendcontrole
- RLS policies
- Auditlogging waar nodig

## Tabellen

Minimaal nodig:

- profiles
- organizations
- user_organizations
- roles
- permissions
- role_permissions
- employees
- clients
- locations
- contracts
- planning
- time_entries
- documents
- payslips
- audit_logs
