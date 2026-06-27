# Data Model

Dit document beschrijft het functionele datamodel.

## Organization

Een organisatie is de hoofdentiteit van een klantomgeving.

Velden:

- id
- name
- legal_name
- kvk_number
- vat_number
- address
- email
- phone
- website
- status

## Employee

Een werknemer is een persoon die wordt ingepland en waarvoor uren en documenten worden beheerd.

Velden:

- id
- organization_id
- first_name
- last_name
- date_of_birth
- email
- phone
- employee_number
- status
- start_date
- end_date

## Client

Een opdrachtgever is een klant waarvoor werk wordt uitgevoerd.

Velden:

- id
- organization_id
- name
- contact_name
- email
- phone
- invoice_email
- status

## Location

Een locatie is een werkplek gekoppeld aan een opdrachtgever.

Velden:

- id
- client_id
- name
- address
- city
- contact_person
- status

## Time Entry

Een urenregel bevat gewerkte uren.

Velden:

- id
- employee_id
- client_id
- location_id
- work_date
- start_time
- end_time
- break_minutes
- total_hours
- status
- comment
- approved_by
- approved_at
