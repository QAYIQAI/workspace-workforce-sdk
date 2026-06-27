# Planning

De planningmodule beheert vaste planning, dagplanning en afwijkingen.

## Functies

- Vaste planning per werknemer
- Locatiekoppeling
- Dagplanning genereren
- Extra inzet
- Ziek/afwezig
- Vervanger registreren
- Planning wijzigen
- Planning naar urenregistratie doorzetten

## Entiteiten

- fixed_schedules
- daily_planning
- planning_exceptions
- employee_location_assignments

## Flow

1. Werknemer wordt gekoppeld aan locatie.
2. Vaste planning wordt ingericht.
3. Dagplanning wordt gegenereerd.
4. Afwijkingen worden toegevoegd.
5. Urenregistratie gebruikt planning als basis.

## Rechten

- planning.view
- planning.create
- planning.update
- planning.delete
- planning.approve
