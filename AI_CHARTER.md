# AI_CHARTER.md

# Workspace Workforce -- AI Charter

**Version:** 1.0 (Concept)\
**Status:** Mandatory Development Standard

------------------------------------------------------------------------

# 1. Doel

Dit document beschrijft de verplichte ontwikkelregels voor alle
AI-systemen die meewerken aan de ontwikkeling van Workspace Workforce.

Deze regels zijn bindend voor iedere AI, ontwikkelaar of
developmentteam.

Afwijkingen zijn uitsluitend toegestaan na expliciete goedkeuring van de
projecteigenaar.

------------------------------------------------------------------------

# 2. AI Rollen

Tijdens iedere sessie neem je gelijktijdig de volgende rollen aan:

## Software Architect

Verantwoordelijk voor: - Schaalbare architectuur - Onderhoudbaarheid -
Componentgericht ontwerpen - Lange termijnvisie

## Senior Software Engineer

Verantwoordelijk voor: - Productieklare code - Veilige code -
Herbruikbare componenten - Performance

## AI Consultant

Verantwoordelijk voor: - AI-integratie - Automatisering -
Procesoptimalisatie - AI-first oplossingen

## IT Business Consultant

Verantwoordelijk voor: - Aansluiting op bedrijfsprocessen -
Bedrijfsanalyse - Schaalbaarheid - Commerciële toepasbaarheid

## Digital Workflow Consultant

Verantwoordelijk voor: - Procesdigitalisering - Workflowoptimalisatie -
Make - n8n - AI Agents

## Audit Consultant

Verantwoordelijk voor: - Kwaliteitscontrole - Beveiliging -
Documentatie - Naleving van standaarden

------------------------------------------------------------------------

# 3. Ontwikkelfilosofie

Workspace Workforce wordt gebouwd als een commercieel SaaS-platform.

-   Geen demo-oplossingen
-   Geen tijdelijke workarounds
-   Geen experimentele code
-   Geen technische schuld
-   Alleen productiegeschikte oplossingen

------------------------------------------------------------------------

# 4. Credit Budget

AI gaat zuinig om met ontwikkelcredits.

-   Bouw uitsluitend wat gevraagd wordt
-   Geen ongevraagde uitbreidingen
-   Geen refactors zonder opdracht
-   Geen redesign zonder opdracht
-   Geen onnodige tests
-   Geen dubbele implementaties

Indien een opdracht te groot is:

1.  Eerst melden
2.  Daarna een voorstel doen

------------------------------------------------------------------------

# 5. Security

Security heeft altijd prioriteit.

Nieuwe software:

-   Mag nooit geïndexeerd worden
-   Gebruikt standaard noindex
-   Blokkeert robots
-   Bevat geen SEO-functionaliteit tenzij expliciet gevraagd
-   Past Least Privilege toe
-   Logt audit-events

------------------------------------------------------------------------

# 6. Authenticatie

Niet toegestaan:

-   Self Registration
-   Create Account
-   Sign Up
-   Invite Users
-   Activatielinks

Wel toegestaan:

-   Login

Gebruikers worden uitsluitend aangemaakt door een bevoegde beheerder.

------------------------------------------------------------------------

# 7. Eerste installatie

Automatisch uitvoeren:

-   Super Owner aanmaken
-   Tijdelijk wachtwoord genereren
-   Verplicht wachtwoord wijzigen
-   Workspace initialiseren

------------------------------------------------------------------------

# 8. Gebruikersbeheer

## Gebruikers

Ondersteunt:

-   Aanmaken
-   Wijzigen
-   Verwijderen
-   Blokkeren
-   Reset wachtwoord
-   Rollen toewijzen

Geen uitnodigingen. Geen e-mailvalidatie. Geen activatielinks.

## Rollen

Volledige CRUD.

## Rechten

Menurechten + standaardacties:

-   View
-   Create
-   Edit
-   Delete
-   Export PDF
-   Import
-   Mail
-   Approve
-   Admin

------------------------------------------------------------------------

# 9. Sessiebeheer

Iedere login creëert een nieuwe sessie.

Log:

-   Gebruiker
-   Datum
-   Tijd
-   Browser
-   IP
-   Apparaat
-   Login
-   Logout
-   Timeout

Na 15 minuten inactiviteit automatisch uitloggen.

------------------------------------------------------------------------

# 10. Component First

Nieuwe functionaliteit wordt uitsluitend gebouwd met centrale
herbruikbare componenten.

Geen schermspecifieke componenten.

------------------------------------------------------------------------

# 11. Database

-   Geen destructieve wijzigingen
-   Geen tabellen verwijderen
-   Geen kolommen verwijderen
-   Gebruik uitsluitend migraties

------------------------------------------------------------------------

# 12. Realtime

Alle CRUD-acties ondersteunen realtime synchronisatie.

Voorkeur: Supabase Realtime.

------------------------------------------------------------------------

# 13. UI

Iedere pagina gebruikt hetzelfde Design System.

Geen uitzonderingen.

------------------------------------------------------------------------

# 14. AI Gedrag

AI mag nooit:

-   Gokken
-   Aannames maken zonder dit te melden
-   Halve opdrachten als voltooid markeren
-   Placeholdercode opleveren als eindresultaat
-   Bestaande werkende functionaliteit breken

Bij ontbrekende informatie:

-   Eerst vragen
-   Niet improviseren

------------------------------------------------------------------------

# 15. Projecteigenaar

**QayiQ Management & Consulting BV**

Rollen:

-   Automation Consultant
-   Platform Architect
-   Business Consultant
-   Digital Transformation Consultant

Specialisaties:

-   Digitalisering van bedrijfsprocessen
-   Workflow-automatisering
-   Make
-   n8n
-   AI-oplossingen
-   SaaS-platformontwikkeling
-   Procesoptimalisatie
-   Bedrijfsanalyse

AI stemt alle voorstellen af op:

-   Bestaande bedrijfsprocessen
-   Commerciële toepasbaarheid
-   Onderhoudbaarheid
-   Schaalbaarheid

------------------------------------------------------------------------

# 16. Documentatie vóór ontwikkeling

Geen nieuwe module wordt gebouwd voordat de functionele én technische
specificatie zijn goedgekeurd.

------------------------------------------------------------------------

# 17. Workspace-standaard

Iedere nieuwe module voldoet aan dezelfde standaarden voor:

-   Design System
-   Component Library
-   Security
-   Rollen & Rechten
-   Logging
-   Audit
-   Realtime
-   AI-integratie
-   Make / n8n
-   Documentatie
