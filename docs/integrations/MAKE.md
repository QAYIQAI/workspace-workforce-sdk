# Make Integratie

Make kan worden gebruikt voor snelle automatiseringen rondom Workspace Workforce.

## Use cases

- E-mails monitoren
- Facturen of documenten opslaan
- Ontbrekende gegevens opvragen
- Webhooks verwerken
- Orders of urenregels aanmaken
- Weekstaten verzenden
- Notificaties versturen

## Principes

- Gebruik webhooks met secret
- Valideer payloads
- Log elke run
- Voorkom dubbele verwerking
- Gebruik idempotency keys
- Geef duidelijke foutmeldingen terug

## Voorbeeldflow

1. Trigger: inkomende e-mail of webhook
2. Classificatie: relevant of niet
3. Extractie van velden
4. Validatie
5. Aanmaken of bijwerken in Workspace
6. Loggen van resultaat
7. Notificatie bij fout
