# Workspace Workforce — Vervolgchat Start Fase 2 na oplevering V1

**Datum:** 2026-07-06  
**Project:** Workspace Workforce / Nurhat Uitzend / DF Cleaning BV  
**Status:** V1 / Fase 1 opgeleverd en gefactureerd  
**Doel:** nieuwe chat starten voor Fase 2 zonder contextverlies

---

## 1. Startzin voor nieuwe chat

Gebruik onderstaande tekst als eerste bericht in de nieuwe chat:

```text
Lees dit bestand als vervolgcontext na oplevering V1 / Fase 1. Geen samenvatting nodig. We starten nu met Fase 2 van Workspace Workforce. Werk stap voor stap, eerst plan geven, daarna pas bouwen na akkoord.
```

---

## 2. Huidige projectstatus

V1 / Fase 1 is opgeleverd aan de klant en gefactureerd.

Fase 1 bevat de basis van het platform:

```text
- Login / toegang via beheerder
- Basis layout en navigatie
- Dashboard
- Planningsoverzicht
- Agendaweergave
- Dagtegels per locatie/dag/soort opdracht
- Goedkeuren-flow
- Dagcontrole-drawer
- Vaste planning
- Werknemers
- Opdrachtgevers / relaties
- Locaties
- Contracten
- Werkregimes
- Soort opdrachten
- Labels
- Gebruikers & rechten basis
- Meertaligheid basis NL/TR
- Sessie-indicatie
- Nachtwerk / eindtijd volgende dag
- Meerdere labels per locatie
- Dupliceeracties bij stamgegevens
```

Fase 1 is technisch vastgelegd in eerdere checkpoint- en opleverdocumentatie. Vanaf deze nieuwe chat gaan we niet opnieuw Fase 1 bouwen of herstructureren.

---

## 3. Belangrijkste vaste werkafspraken

Alle bouwprompts blijven starten met:

```text
BUDGET / CREDITS
HARDE BEVEILIGINGSEISEN
```

Vaste regels:

```text
- Credit-zuinig werken.
- Eerst plan geven, daarna pas uitvoeren na akkoord.
- Geen brede refactors.
- Geen redesigns zonder expliciete opdracht.
- Geen database-reset.
- Geen auth-wijzigingen zonder expliciet akkoord.
- Geen RLS-wijzigingen zonder expliciet akkoord.
- Geen service-role key in frontend.
- Geen auth.admin in frontend.
- Geen supabase.auth.signUp() in frontend.
- Geen hard delete, tenzij expliciet ontworpen en veilig uitgevoerd.
- Geen .env of secrets terugplaatsen in code/backups.
- Geen productie-publicatie vanuit buildplatform.
- Productie loopt via deployplatform.
- Buildplatform is alleen voor bouwen, preview en code-sync.
- Gebruiker doet functionele test zelf.
- Builder doet alleen noodzakelijke typecheck / compile check.
```

---

## 4. Generieke terminologie

In klantgerichte én interne overdrachtsdocumentatie gebruiken we voortaan geen concrete platform-/leveranciersnamen.

Gebruik generieke termen:

| Generieke term | Betekenis |
|---|---|
| Buildplatform | Platform waarin gebouwd/gepreviewd wordt |
| Databaseplatform | Database, auth en storage |
| Backupplatform / code-backupplatform | Centrale broncode/back-up |
| Deployplatform | Productiehosting/deployment |
| Automatiseringsplatform | Externe workflow-/automatiseringslaag |
| Serverfunctie / edge functie | Veilige backendfunctie |
| AI-laag | AI-functionaliteit indien later nodig |

---

## 5. Kritieke deploymentregel

Bij ieder buildrapport eerst zoeken naar:

```text
Migratie / SQL — nog uitvoeren
```

Als dit aanwezig is:

```text
1. Niet verder testen alsof alles live is.
2. Eerst SQL handmatig uitvoeren in databaseplatform.
3. Daarna preview/productie testen.
4. Daarna backup maken.
5. Daarna pas verder bouwen.
```

Reden: eerder ontstonden laadfouten door frontend-code die nieuwe databasekolommen verwachtte terwijl de migratie nog niet was uitgevoerd.

---

## 6. Status Fase 1 — afgeronde technische stappen

Fase 1 bevat onder andere de volgende afgeronde stappen:

```text
Stap 1 — Weekdagvolgorde Zo Ma Di Woe Do Vr Za
Stap 2 — Nachtwerk / eindtijd volgende dag
Stap 3 — Vaste planning groeperen per medewerker
Stap 4 — Medewerkerkaart tab Planning koppelen
Stap 5 — Goedkeuren-flow dagtegels
Stap 6 — Labels-menu + meerdere labels per locatie
Stap 7 — Dupliceerknoppen stamgegevens
```

Belangrijk: deze stappen zijn afgerond en vormen nu de stabiele basis.

---

## 7. Belangrijke Fase 1-besluiten die blijven gelden

### 7.1 Planningkaart

Niet:

```text
1 tegel per medewerker
```

Wel:

```text
1 locatie + 1 datum + 1 soort opdracht = 1 tegel
```

Primaire grouping-key:

```text
date + location_id + job_type_id
```

### 7.2 Operationele werkelijkheid tonen

Planningkaart en dagcontrole moeten tonen wie werkelijk werkt.

Voorbeelden:

```text
Karim Boutahar (ipv Milan Acar - ziek)
Daan Jansen (ipv Aylin Yilmaz - verlof)
Pietje (extra)
Ahmet Youssef (afwezig)
Chauffeur: Milan Acar (ipv Anna Nowak - verlof) · 08:30–16:00
```

### 7.3 Goedkeuren-flow

Bestaande flow blijft:

```text
Tegel → Goedkeuren → keuze-popup
```

Keuzes:

```text
Zonder aanpassing → direct status gecontroleerd
Ik wil aanpassen → bestaande dagcontrole-drawer openen
```

Niet wijzigen zonder expliciete opdracht.

### 7.4 Nachtwerk

Nachtwerk gebruikt `ends_next_day`.

Voorbeeld:

```text
19:00 → 04:00 + ends_next_day=true = geldig
19:00 → 04:00 + ends_next_day=false = ongeldig
```

### 7.5 Generated column planned_minutes

`planned_minutes` is een generated stored column en mag nooit expliciet worden geschreven.

Niet doen:

```text
planned_minutes meesturen in insert/update
```

Wel:

```text
planned_minutes alleen selecteren/lezen/weergeven
```

---

## 8. Openstaande backlog na Fase 1

Deze lijst is geregistreerd, maar nog niet definitief gefaseerd. Fase 2 start met de meest logische operationele kern.

### 8.1 Planning / dagplanning uitbreidingen

```text
- Extra werk toevoegen via nieuwe knop.
- Losse knop “extra medewerker toevoegen” vervangen/verwijderen.
- Extra werk drawer:
  - Kies locatie
  - Kies één of meerdere medewerkers
  - Opmerking toevoegen
  - Status kiezen
  - Start- en einduren invullen
- Extra werk koppelen aan urenregistratie.
- Afwijkingen in uren tonen.
- Meer/minder uren markeren met geel + info-hover.
```

### 8.2 Urenregistratie en weekstaten

```text
- Urenregistratie bouwen.
- Weekstaten bouwen.
- Weekoverzicht per medewerker.
- Weekoverzicht per locatie.
- Weekoverzicht per opdrachtgever.
- Verschil gepland versus gewerkt tonen.
- Basis voor verloning en facturatie voorbereiden.
```

### 8.3 Werknemers weekrooster / Excel-achtige lijst

```text
- Weekrooster werknemers als Excel-achtige lijst.
- Codes verwerken:
  - V = verlof
  - Z = ziekte
  - F = feestdag
- Soort werk per dag/week bijhouden.
- Later koppelen aan app-aanvragen.
- Verlof/ziekte aanvragen later via app.
- Na goedkeuring aanvragen automatisch inschieten in weekrooster.
```

### 8.4 Werknemersmodule uitbreidingen

```text
- Klachtenregistratie voor werknemers.
- Medewerkerhistorie.
- Personeelsdocumenten verder uitwerken.
- Loonstrokenmodule verder uitwerken.
```

### 8.5 Administratie

```text
- Administratie-dashboard.
- Exportmodule.
- Historie.
- PDF-generatie.
- PDF’s via e-mail versturen.
- Rapportages.
- Facturatievoorbereiding.
- Verloningsvoorbereiding.
```

### 8.6 Klantenportaal

```text
- Klantenportaal met eenvoudige login.
- 1-page goedkeurpagina.
- Klant keurt digitaal werkuren van betreffende medewerkers goed.
- Klant ziet alleen eigen locaties/medewerkers/uren.
- Goedkeurhistorie klant.
```

### 8.7 Vacature / sollicitantenmodule

```text
- Vacature landingspage.
- Publiek aanvraagformulier voor potentiële medewerkers.
- Apart intern menu voor sollicitanten.
- Kandidatenstatussen:
  - Nieuw
  - In behandeling
  - Geschikt
  - Afgewezen
  - Aangenomen
- Goedgekeurde kandidaat later omzetten naar werknemer.
```

### 8.8 Taken / reminders / bel-icoon

```text
- Bel-icoon rechtsboven uitwerken.
- Taken/reminders vastleggen.
- Datum/tijd/status per taak.
- Taken afvinken na uitvoering.
- Open/verlopen taken signaleren.
```

### 8.9 Communicatie / Telegram / automatisering

```text
- Telegram-flow is geparkeerd.
- Later apart functioneel en technisch ontwerpen.
- Chat ID automatisch koppelen via persoonlijke Telegram-link.
- Serverfunctie/edge functie gebruiken.
- Geen token/secrets in frontend.
- Berichttemplates ontwerpen.
- Verzendlogs ontwerpen.
```

---

## 9. Voorlopige fase-indeling na Fase 1

Deze fase-indeling is voorlopig en mag in Fase 2-chat worden aangescherpt.

```text
Fase 1 — Basisplatform / planning / stamgegevens / goedkeuren
Status: opgeleverd en gefactureerd

Fase 2 — Operationele verwerking
- Urenregistratie
- Weekstaten
- Extra werk-flow
- Urenafwijkingen
- Werkbrief basis

Fase 3 — Administratie en export
- Administratie-dashboard
- Export
- Historie
- PDF’s
- E-mailverzending
- Rapportages

Fase 4 — Personeelsbeheer verdieping
- Werknemers weekrooster
- Verlof / ziekte / feestdagcodes
- Klachtenregistratie
- Documenten
- Loonstroken

Fase 5 — Klantenportaal
- Klantlogin
- Digitale klantgoedkeuring
- Klanturenoverzicht

Fase 6 — Vacature en sollicitanten
- Vacature landingspage
- Sollicitantenmodule
- Kandidaat naar werknemer-flow

Fase 7 — Communicatie en automatisering
- Telegram
- Serverfunctie / edge functie
- Automatiseringsflows
- Verzendlogs

Fase 8 — App-koppelingen en mobiele flows
- Verlof/ziekte aanvragen via app
- Mobiele voorman-/werknemerflow
```

---

## 10. Aanbevolen focus voor Fase 2

Fase 2 moet het platform van planningbasis naar operationele verwerking brengen.

Aanbevolen hoofdlijn:

```text
Planning
  ↓
Dagcontrole
  ↓
Urenregistratie
  ↓
Weekstaten
  ↓
Werkbrief / controleoverzicht
```

### Fase 2 — aanbevolen bouwvolgorde

| Stap | Onderdeel | Doel |
|---:|---|---|
| 2.1 | Urenregistratie basis | Gecontroleerde dagen omzetten naar werkelijke urenregels |
| 2.2 | Weekstaten basis | Weektotalen per medewerker, locatie en opdrachtgever |
| 2.3 | Extra werk-flow | Extra werkzaamheden toevoegen buiten vaste planning |
| 2.4 | Urenafwijkingen | Meer/minder uren zichtbaar maken met kleur en info |
| 2.5 | Werkbrief-preview | Dag-/weekoverzicht voor operationele controle |
| 2.6 | Dashboard signaleringen | Open dagen, ontbrekende controles, afwijkingen |

---

## 11. Fase 2 — belangrijke ontwerpprincipes

```text
- Eerst operationele kern, daarna administratie.
- Eerst preview/controle, daarna PDF/mail/export.
- Eerst handmatig betrouwbaar, daarna automatisering.
- Eerst bestaande data hergebruiken, daarna pas migraties.
- Geen Telegram in Fase 2 tenzij apart expliciet besloten.
- Geen klantenportaal in Fase 2 tenzij planning wijzigt.
- Geen vacaturemodule in Fase 2.
```

---

## 12. Eerste voorgestelde Fase 2-stap

### Stap 2.1 — Urenregistratie basis

Doel:

```text
Gecontroleerde planningdagen zichtbaar maken in Urenregistratie, zodat werkelijke uren per medewerker gecontroleerd en later gebruikt kunnen worden voor weekstaten.
```

Minimale scope:

```text
- Pagina Urenregistratie activeren.
- Alleen gecontroleerde of relevante planningdagen tonen.
- Per medewerker: datum, locatie, opdrachtgever, soort opdracht, geplande start/eind, werkelijke start/eind.
- Werkelijke start/eind standaard vullen met geplande start/eind.
- Pauze tonen/bewerkbaar maken indien bestaand.
- Status: concept / gecontroleerd / afwijking.
- Meer/minder uren berekenen.
- Geen export.
- Geen PDF.
- Geen loonstroken.
- Geen facturatie.
```

Belangrijke vraag vóór bouw:

```text
Gebruiken we planning_days direct als bron voor urenregistratie, of maken we een aparte timesheets/work_hours tabel?
```

Advies:

```text
Eerst onderzoeken welke tabellen al bestaan.
Daarna kleinste veilige ontwerp kiezen.
```

---

## 13. Eerste promptstrategie in Fase 2-chat

Niet direct bouwen. Eerst een inspectieprompt laten uitvoeren:

```text
BUDGET / CREDITS
ALLEEN ONDERZOEKEN. Niet bouwen. Niet refactoren. Geen databasewijzigingen.

HARDE BEVEILIGINGSEISEN
Geen database-reset.
Geen RLS-wijzigingen.
Geen auth-wijzigingen.
Geen service-role key in frontend.
Geen auth.admin in frontend.
Geen hard delete.
Geen nieuwe modules bouwen.

DOEL
Onderzoek hoe Urenregistratie het veiligst gebouwd kan worden op basis van de bestaande planning_days en eventuele bestaande tabellen voor uren/weekstaten.

CONTROLEER
1. Welke route/component bestaat voor Urenregistratie.
2. Welke route/component bestaat voor Weekstaten.
3. Of er al tabellen bestaan voor urenregistratie, timesheets, work_hours, weekstaten of exports.
4. Welke relevante kolommen in planning_days bestaan.
5. Of planning_days geschikt is als bron voor werkelijke uren.
6. Of planned_minutes generated is en dus niet geschreven mag worden.
7. Welke minimale migratie eventueel nodig zou zijn.
8. Hoe RLS/company-scoping nu werkt.
9. Welke querykeys en componenten hergebruikt kunnen worden.

OUTPUT
Rapporteer kort:
- Bestaande bestanden/routes.
- Bestaande tabellen/kolommen.
- Advies: planning_days hergebruiken of aparte uren-tabel maken.
- Mogelijke minimale migratie, alleen als echt nodig.
- Risico’s.
- Daarna nog niets bouwen.
```

---

## 14. Niet bouwen in eerste Fase 2-stap

```text
- Geen PDF-generatie.
- Geen mailverzending.
- Geen export.
- Geen klantenportaal.
- Geen Telegram.
- Geen vacaturemodule.
- Geen app-koppeling.
- Geen documenten/loonstroken.
- Geen brede dashboard rebuild.
- Geen complete administratie-module.
```

---

## 15. Documenten die al zijn gemaakt

Reeds aangemaakt in vorige chat:

```text
- Fase 1 technische documentatie generiek
- Fase 1 klantopleverdocument generiek
- Beknopte handleiding generiek
- Fasevoorstel-roadmap na Fase 1
- Concept Telegram-integratie MD
- Master Lovable Guardrails & Restricties v2
```

Deze hoeven niet opnieuw gemaakt te worden, tenzij er een update nodig is.

---

## 16. Belangrijke open ontwerpvragen voor Fase 2

```text
1. Wordt Urenregistratie gebaseerd op planning_days of aparte uren-tabel?
2. Wanneer is een dag klaar voor weekstaat: na Goedkeuren of na Urenregistratie-status?
3. Mag een medewerker meerdere urenregels op één dag hebben?
4. Hoe behandelen we extra werk in weekstaten?
5. Hoe behandelen we ziekte/verlof/feestdag in urenoverzicht?
6. Moeten bussen/productie meetellen in weekstaat of apart rapport?
7. Moet Weekstaat eerst intern zijn of direct klantdeelbaar?
8. Welke exportvorm is later nodig: PDF, CSV, Excel of boekhouderformaat?
```

---

## 17. Nieuwe chat werkwijze

In de nieuwe chat:

```text
1. Eerst dit document lezen als context.
2. Geen samenvatting vragen/geven tenzij nodig.
3. Start met Fase 2 plan.
4. Begin met onderzoeksstap Urenregistratie.
5. Na onderzoeksrapport pas kiezen voor bouwprompt.
6. Na elke bouwstap: testen, eventueel SQL uitvoeren, backup maken.
7. Daarna pas volgende stap.
```

---

## 18. Korte nieuwe-chat opdracht

```text
We starten Fase 2 na opgeleverde V1/Fase 1. Gebruik de Master Lovable Guardrails v2. Begin niet met bouwen. Maak eerst een inspectieprompt voor Urenregistratie en Weekstaten, met BUDGET / CREDITS en HARDE BEVEILIGINGSEISEN bovenaan.
```

