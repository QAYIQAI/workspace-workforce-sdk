# Security

Workspace Workforce verwerkt gevoelige persoonsgegevens en documenten.

## Kernregels

- Authenticatie verplicht
- Autorisatie server-side
- Geen publieke personeelsdocumenten
- Geen secrets in frontend
- Geen wachtwoorden loggen
- Geen volledige gevoelige documenten loggen
- Secure cookies
- HTTPS verplicht
- Back-ups verplicht
- Auditlogs voor kritieke acties

## Rollen

Gebruikers krijgen minimale rechten.

## Documenten

Documenten worden alleen via beveiligde routes geopend.

## Exports

Exports kunnen gevoelige data bevatten en zijn daarom alleen beschikbaar voor bevoegde rollen.

## Tenant-isolatie

Bij multi-tenant moet elke query tenant-aware zijn.
