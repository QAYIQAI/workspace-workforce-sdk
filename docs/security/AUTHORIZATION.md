# Authorization

## Rechtenmodel

Autorisatie geldt op:

- Menu
- Pagina
- API-route
- Actieknop
- Upload
- Download
- Export
- Goedkeuring
- Instelling

## Backend leidend

Frontend mag menu’s verbergen, maar backend moet elke actie opnieuw controleren.

## Permission format

Voorbeeld:

- employees.view
- employees.create
- employees.update
- documents.download
- timesheets.approve
- exports.create
- settings.manage

## Role mapping

Rollen worden gekoppeld aan permissions via role_permissions.
