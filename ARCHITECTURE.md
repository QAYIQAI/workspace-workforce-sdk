# Architectuur

Workspace Workforce SDK is ontworpen als modulaire, veilige en uitbreidbare architectuur voor workforce-platformen.

## Architectuurdoelen

- Scheiding tussen frontend, backend, database en storage
- Sterke rollen- en rechtenstructuur
- Documentbeveiliging
- Auditlogging
- API-koppelingen
- Automatisering
- AI-ondersteuning
- Multi-tenant voorbereiding
- Mobielvriendelijkheid
- Onderhoudbaarheid

## High-level lagen

```text
Presentation Layer
Application Layer
Domain Layer
Data Access Layer
Database Layer
Storage Layer
Integration Layer
Automation Layer
AI Assistance Layer
Security & Governance Layer
```

## Presentation Layer

Bevat alle interfaces:

- Admin UI
- Dashboard
- Planning
- Urenregistratie
- Weekstaten
- Documentbeheer
- Instellingen
- Klantportaal
- Medewerkerportaal

Eisen:

- Responsive
- Mobiel geschikt
- Dark mode en light mode
- Navigatie op rechtenbasis
- Geen gevoelige bedrijfslogica uitsluitend in frontend

## Application Layer

Verwerkt:

- Authenticatie
- Autorisatie
- Validatie
- API-routes
- Uploads
- Downloads
- Exports
- Auditlogs
- Foutafhandeling

## Domain Layer

Bevat kernlogica:

- Werknemers
- Opdrachtgevers
- Locaties
- Contracten
- Planning
- Uren
- Weekstaten
- Documenten
- Loonstroken
- Exports
- Rechten
- Notificaties

## Database Layer

Standaard tabellen:

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

## Storage Layer

Bewaart:

- Personeelsdocumenten
- ID-documenten
- Contracten
- Loonstroken
- Weekstaat-PDF’s
- Exportbestanden
- Rapportages

Principes:

- Geen publieke toegang tot gevoelige documenten
- Download via beveiligde backendroute
- Bestandstypecontrole
- Bestandsgroottebeperking
- Metadata in database
- Auditlog voor kritieke downloads

## Integrations

Ondersteunt koppelingen met:

- Boekhouding
- Loonadministratie
- E-mailproviders
- Make
- n8n
- CRM
- HR-systemen
- AI-providers

## Tenantmodellen

### Single-tenant

Elke klant heeft eigen omgeving en database.

### Multi-tenant

Meerdere klanten delen applicatie en database via tenant_id.

### Hybrid

Combinatie van single-tenant en multi-tenant.

## Security

Security wordt toegepast op:

- Login
- Sessies
- Rollen
- API-routes
- Bestanden
- Exports
- Tenantdata
- Integraties
- Logs

Backend beslist altijd definitief over rechten.
