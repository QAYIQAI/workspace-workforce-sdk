# Rollen en Rechten

Het rollen- en rechtenmodel bepaalt wat gebruikers mogen zien en doen.

## Standaardrollen

- Super Admin
- Beheerder
- Manager
- Planner
- Administratie
- Boekhouding
- Medewerker
- Opdrachtgever
- Viewer
- Auditor

## Rechten

- view
- create
- update
- delete
- approve
- reject
- export
- import
- upload
- download
- send
- archive
- restore
- manage

## Principes

- Frontend verbergt menu’s op basis van rechten.
- Backend controleert rechten altijd opnieuw.
- Geen API-route vertrouwt alleen op frontend.
- Rollen zijn per organisatie configureerbaar.
