# Workspace Workforce — Vervolgnotitie Planning / Dagcontrole

**Datum:** 2026-07-03  
**Doel:** overdrachtsnotitie voor nieuwe chat.  
**Project:** Workspace Workforce / Webplatform Uitzend  
**Status:** Personeel, Documenten, Loonstroken, Relaties, Werkregimes, Soort opdrachten en Vaste planning staan grotendeels werkend. Volgende stap is Planningtegel + dagcontrole.

---

## 1. Werkafspraak voor vervolg

Alle Lovable-opdrachten blijven compact en credit-zuinig.

Iedere bouwprompt start met:

```text
BUDGET / CREDITS
HARDE BEVEILIGINGSEISEN
```

Regels:

- Geen brede refactors.
- Geen redesigns zonder expliciete opdracht.
- Geen nieuwe modules combineren met fixes.
- Geen databasewijzigingen tenzij strikt noodzakelijk.
- Geen service-role key in frontend.
- Geen `auth.admin` in frontend.
- Geen `supabase.auth.signUp()` in frontend.
- Gebruiker doet functionele tests zelf.
- Lovable doet alleen typecheck / compile check.

---

## 2. Huidige werkende onderdelen

### Personeel

Werkend / akkoord:

- Werknemerslijst.
- Werknemer-detail drawer via oog/pen.
- Stamgegevens professioneel opgebouwd.
- Soort werknemer multi-select met badges.
- Loonkostberekening.
- Documenten-tab gekoppeld aan documentmenu.
- Loonstroken-tab gekoppeld aan loonstrokenmenu.
- Documenten en loonstroken upload via private storage.
- Mobiele layout is nu goed genoeg.

### Documenten en loonstroken

Werkend / akkoord:

- Menu Documenten.
- Menu Loonstroken.
- Drawer-tabs bij werknemer gebruiken dezelfde data via `employee_id`.
- Geen dubbele opslag.
- Private bucket `employee-files`.
- Download/bekijken via signed URL.
- Documenten/loonstroken worden veilig via UI geüpload, niet via dummy SQL.

### Relaties

Hoofdmenu Relaties bevat:

- Opdrachtgevers.
- Locaties.
- Contracten.

Werkend / akkoord:

- Oog = bekijken.
- Pen = bewerken.
- Rood = inactief.
- Groen = actief.
- Geen hard delete.
- Drawers zijn één scherm, geen tabs.
- Boekhouder/export bewust niet opgenomen bij Opdrachtgevers.

### Soort opdrachten

Belangrijk voor planningkleur.

Bestaande soorten:

| Soort opdracht | Kleur |
|---|---|
| Rijklaar | `#BFE3FF` |
| Tussenbeurt | `#FFD2C2` |
| Grote beurt | `#2F6BFF` |

Belangrijke afspraak:

- Agenda/planningkleuren komen uit `job_types.color`.
- Kleuren mogen niet hardcoded in planning staan.
- Soort opdrachten hebben nu inline bewerken.
- Laatste gewenste wijziging: kleurveld moet een echte colorpicker zijn zoals voorbeeldfoto, niet alleen ronde swatches/keuzerondjes.

### Werkregimes

Werkregimes zijn bedoeld voor vaste agenda / vaste planning.

Bronvelden:

- Naam.
- Status.
- Werkdagen.
- Tijdblok start/eind.
- Pauze minuten.
- Kleur.
- Opmerking.

Werkregime wordt gekozen in Vaste planning en vult automatisch:

- Werkdagen.
- Starttijd.
- Eindtijd.
- Pauze minuten.

Gebruiker mag daarna handmatig overschrijven.

### Vaste planning

Tabel: `public.assignments`.

Toegevoegd via migratie 21:

- `work_regime_id`
- `start_time`
- `end_time`
- `break_minutes`

Vaste planning koppelt:

```text
Medewerker + Locatie + Werkregime
→ Week genereren
→ Planningregels
```

---

## 3. Planningkleur-keten

De juiste keten is:

```text
Soort opdrachten
→ job_types.color
→ locations.job_type_id
→ planning_days.job_type_id
→ agenda kleur
```

Kleurprioriteit:

1. `planning_days.job_type_id → job_types.color`
2. `locations.job_type_id → job_types.color`
3. `work_regimes.color` als fallback
4. neutraal

Belangrijk:

- Geen kleur hardcoded per agenda-card.
- Geen employee-kleur als primaire planningkleur.
- Werkregimekleur alleen fallback/hint.

---

## 4. Demo/testdata huidige context

Er is dummydata voorbereid/gebruikt voor:

- 1 opdrachtgever: HSU.
- 15 medewerkers.
- 15 HSU-locaties.
- 3 contracten.

Locaties zijn verdeeld over soort opdrachten:

| Soort opdracht | Locaties | Kleur |
|---|---:|---|
| Rijklaar | 9 | `#BFE3FF` |
| Tussenbeurt | 4 | `#FFD2C2` |
| Grote beurt | 2 | `#2F6BFF` |

Documenten en loonstroken worden niet via SQL gevuld, maar veilig via UI geüpload.

---

## 5. Nieuwe gewenste Planning UX

### 5.1 Planningkaart = locatiekaart

Niet meer één kaart per medewerker.

Wel:

```text
1 locatie + 1 datum + 1 soort opdracht = 1 planningtegel
```

Voorbeeld:

```text
HSU Barendrecht (4)
[Tussenbeurt] [Chauffeur: Yusuf Kaya]

Locatie: 08:30–17:00

1. Milan Acar — 08:00–17:30
2. Piotr Kowalski — 08:00–17:30
3. Fatima Benali — 08:00–17:30
4. Sara Demir — 08:00–17:30

[✓ Bevestigen] [Telegram]
```

Belangrijk:

- Locatietijd is apart van medewerkertijd.
- Locatietijd komt uit locatie/planninggroep.
- Medewerkertijd komt uit planningregel/assignment/employee-day data.
- Medewerkers staan genummerd onder elkaar.
- Tegel toont aantal medewerkers tussen haakjes.

### 5.2 Groeperingssleutel

Planningregels groeperen per:

- Datum.
- `location_id`.
- `job_type_id`.
- Starttijd/eindtijd of locatie-tijdblok.

---

## 6. Dagcontrole / bevestigings-popup

Op iedere planningtegel komt:

- Groene bevestigknop.
- Telegram-knop.
- Eventueel pen/edit als dezelfde drawer wordt gebruikt.

Bij klikken opent een popup/drawer voor die locatie op die dag.

### Blok A — Locatie, adres en Telegram

Toon:

- Locatie.
- Opdrachtgever.
- Volledig adres.
- Google Maps-link.
- Vaste chauffeur.
- Telegram-knop.
- Laatste Telegram-logregel indien beschikbaar.

Google Maps-link:

```text
https://www.google.com/maps/search/?api=1&query=<encoded full address>
```

Telegrambericht bevat:

- Locatie.
- Volledig adres.
- Google Maps-link.
- Datum.
- Locatie werktijd.
- Medewerkers onder verantwoordelijkheid van chauffeur.

Geen Telegram bot token of secret in frontend.

Als backend/integratie ontbreekt:

- Geen fake succesvolle verzending bouwen.
- Alleen knop/preview/log-click voorbereiden of nette melding tonen.

### Blok B — Tijdcontrole locatie

Velden:

- Locatie starttijd.
- Locatie eindtijd.
- Pauze indien beschikbaar.

Deze tijden zijn voor de locatie/opdrachtkaart en mogen per dag aangepast worden.

### Blok C — Personeelscontrole

Per medewerker:

| Veld | Gedrag |
|---|---|
| Naam | read-only of select bij nieuwe medewerker |
| Starttijd | wijzigbaar voor deze dag |
| Eindtijd | wijzigbaar voor deze dag |
| Status | dropdown |
| Actie | verwijderen/vervanger toevoegen |

Statusopties minimaal:

- Gepland.
- Gewerkt.
- Ziek.
- Afwezig.
- Vervangen.

Functionaliteit:

- Status per medewerker wijzigen.
- Start/eindtijd per medewerker wijzigen.
- Medewerker uit die dag verwijderen.
- Medewerker toevoegen.
- Bij ziek/afwezig vervanger kiezen.
- Dit wijzigt alleen de planning voor die dag, niet de vaste planning.

### Blok D — Bussen/productcontrole

Voor bussen standaard drie velden:

| Type | Veld |
|---|---|
| 10m bus | aantal |
| 11m bus | aantal |
| 12m bus | aantal |

Daarbij:

- Totaal bussen automatisch optellen.
- Geplande minuten/uren tonen indien `minutes_per_product` beschikbaar is.
- Gecontroleerd-vinkje of status.

Berekening:

```text
totaal_bussen = bus_10m + bus_11m + bus_12m
planned_minutes = totaal_bussen × minutes_per_product
planned_hours = planned_minutes / 60
afronden volgens round_up indien aanwezig
```

### Laatste bevestiging

Na klikken op **Bevestigen**:

Laatste korte popup:

- Tijden akkoord?
- Personeel akkoord?
- Bussen akkoord?
- Telegram/adres eventueel verzonden?

Daarna:

```text
status: Gepland → Bevestigd
```

Tegel toont daarna:

- Statusbadge Bevestigd.
- Groene vink zichtbaar.
- Bijgewerkte medewerkers/tijden/bussen.

---

## 7. Vaste chauffeur

Per planningtegel moet een vaste chauffeur zichtbaar zijn.

Bronvolgorde:

1. `locations.fixed_driver_employee_id` indien gevuld.
2. Anders medewerker in de groep met `employee_type = Chauffeur`.
3. Anders geen chauffeur-label.

Op tegel:

```text
[Chauffeur: Yusuf Kaya]
```

Telegrambericht gebruikt deze chauffeur-context.

---

## 8. Conceptprompt voor volgende chat

Deze prompt moet in de volgende chat worden herbekeken en pas daarna toegepast.

```text
BUDGET / CREDITS
MINIMALE PLANNING BEVESTIGINGSPATCH. Bouw alleen de planningtegel-bewerking/bevestiging per locatiekaart. Geen redesign. Geen database-reset. Geen auth/rechten wijzigen. Geen documenten/loonstroken wijzigen. Geen planninggeneratie volledig herbouwen. Geen brede refactor. Ik test zelf; doe alleen de must-have functionaliteit.

HARDE BEVEILIGINGSEISEN
- Geen service-role key in frontend.
- Geen auth.admin in frontend.
- Geen supabase.auth.signUp in frontend.
- Geen data verwijderen.
- Geen hard delete.
- Geen user/auth/rechten aanpassen.
- Bestaande RLS en rechten blijven leidend.
- Geen bestaande planningregels verwijderen.
- Geen publieke externe integratie secrets in frontend.

DOEL
Maak van de planningtegel een locatiekaart die per dag gecontroleerd en bevestigd kan worden.

Kern:
- Planningkaart = locatie + dag + soort opdracht.
- Medewerkers staan onder elkaar binnen dezelfde locatiekaart.
- Op de kaart komen actieknoppen:
  - groene bevestigknop
  - Telegram-knop
  - eventueel pen/edit-knop indien nodig
- Bij klikken op bevestigen/edit opent een popup/drawer voor dagcontrole.

==================================================
1. PLANNINGKAART GROEPEREN
==================================================

In Planning > Agenda/Dag/Werkbrief waar van toepassing:

Groepeer planningregels per:
- date
- location_id
- job_type_id
- start_time/end_time of locatie-tijdblok

Niet meer:
- aparte kaart per medewerker

Wel:
1 locatiekaart met medewerkers onder elkaar.

Voorbeeld gewenste tegel:

HSU Barendrecht (4)
[Tussenbeurt] [Chauffeur: Yusuf Kaya]

Locatie: 08:30–17:00

1. Milan Acar — 08:00–17:30
2. Piotr Kowalski — 08:00–17:30
3. Fatima Benali — 08:00–17:30
4. Sara Demir — 08:00–17:30

[✓ Bevestigen] [Telegram]

Belangrijk:
- Locatietijd en medewerkertijd zijn twee aparte lagen.
- Locatietijd komt uit location/planninggroep.
- Medewerkertijd komt uit planningregel/assignment/employee-day data.
- Als medewerker geen eigen tijd heeft, gebruik fallback van assignment of locatie.

==================================================
2. TEGELDATA UIT BESTAANDE TABELLEN
==================================================

Bij week genereren en bij rendering moet tegeldata uit bestaande tabellen komen:

Locatie:
- locations.name
- locations.address
- locations.postal_code
- locations.city
- locations.country
- locations.start_time
- locations.end_time
- locations.bus_count / default_bus_count
- locations.minutes_per_product
- locations.default_person_count
- locations.fixed_driver_employee_id
- locations.job_type_id

Opdrachtgever:
- clients.name

Soort opdracht:
- job_types.name
- job_types.color

Vaste planning:
- assignments.employee_id
- assignments.location_id
- assignments.work_regime_id
- assignments.start_time
- assignments.end_time
- assignments.break_minutes
- assignments.weekdays
- assignments.active

Werkregime:
- work_regimes.days
- work_regimes.start_time
- work_regimes.end_time
- work_regimes.break_minutes

Medewerker:
- employees.first_name
- employees.last_name
- employee_types, vooral type Chauffeur indien beschikbaar

==================================================
3. KLEURREGEL
==================================================

Agenda-kleur blijft uit Soort opdrachten:

- planning_days.job_type_id
- job_types.color
- fallback via location.job_type_id
- fallback via work_regime color
- anders neutraal

Niet doen:
- geen kleuren hardcoden in planningkaart
- geen employee kleur als primaire kleur
- geen nieuwe kleurmapping bouwen

==================================================
4. VASTE CHAUFFEUR
==================================================

Bij week genereren / rendering moet per locatiekaart een vaste chauffeur zichtbaar zijn.

Bronvolgorde:
1. locations.fixed_driver_employee_id indien gevuld
2. anders medewerker in de groep met employee_type = Chauffeur
3. anders geen chauffeur-label tonen

Op tegel tonen:
[Chauffeur: Yusuf Kaya]

Telegrambericht moet naar deze chauffeur-context gekoppeld worden.

Geen echte Telegram API integratie bouwen als er nog geen backend/config bestaat.
Wel UI-knop + logstructuur voorbereiden zoals hieronder.

==================================================
5. GOOGLE MAPS LINK
==================================================

Bij locatie moet een Google Maps-link gegenereerd kunnen worden uit adresgegevens.

Bron:
- address
- postal_code
- city
- country

Linkformaat:
https://www.google.com/maps/search/?api=1&query=<encoded full address>

Gebruik dit in:
- planning drawer
- Telegrambericht preview
- eventueel locatie detail

Geen externe API nodig.
Gewoon link genereren op basis van adres.

==================================================
6. TEGEL ACTIES
==================================================

Elke gegroepeerde locatiekaart krijgt:

1. Groene vink / Bevestigen
- opent dagcontrole drawer/popup

2. Telegram-knop
- mag direct op tegel staan
- opent eventueel bevestiging/preview of triggert voorbereid verzendmoment
- als echte Telegram integratie nog niet bestaat: bouw geen fake verzenden, maar registreer alleen voorbereid/gelogd als “aangeklikt/te verzenden” volgens bestaande logmogelijkheid of toon nette melding

3. Pen/edit optioneel
- als dezelfde actie als bevestigen logisch is, gebruik één knop “Bevestigen/bewerken”

==================================================
7. DAGCONTROLE DRAWER / POPUP
==================================================

Bij klikken op groene vink of edit opent een drawer/popup voor die locatie + datum.

Drawer bevat 4 blokken.

BLOK A — LOCATIE / ADRES / TELEGRAM

Toon:
- locatie
- opdrachtgever
- volledig adres
- Google Maps-link
- vaste chauffeur
- Telegram-knop
- laatste Telegram-logregel indien beschikbaar:
  - datum/tijd
  - gebruiker
  - chauffeur
  - status

Telegramberichtinhoud:
- locatie
- volledig adres
- Google Maps-link
- datum
- tijdblok locatie
- medewerkers onder verantwoordelijkheid van chauffeur

Geen Telegram bot token of secret in frontend.
Als backend/integratie ontbreekt: alleen knop/preview/log-click voorbereiden, geen fake succesvol verzenden.

BLOK B — TIJDCONTROLE LOCATIE

Velden:
- locatie starttijd
- locatie eindtijd
- pauze indien beschikbaar

Deze tijden zijn voor de opdracht/locatiekaart.
Gebruiker mag aanpassen voor deze dag.

BLOK C — PERSONEELSCONTROLE

Toon medewerkers onder elkaar.

Per medewerker:
- naam
- starttijd
- eindtijd
- status dropdown
- verwijderen-knop
- vervanger toevoegen indien ziek/afwezig

Statusopties minimaal:
- Gepland
- Gewerkt
- Ziek
- Afwezig
- Vervangen

Functionaliteit:
- medewerkerstatus per persoon wijzigen
- individuele start/eindtijd wijzigen
- medewerker verwijderen uit deze dag
- medewerker toevoegen
- bij ziek/afwezig vervanger kiezen via dropdown
- vervanger wordt toegevoegd als nieuwe medewerkerregel

Belangrijk:
- Dit geldt alleen voor deze datum/planningdag.
- Niet de vaste planning structureel wijzigen.
- Geen medewerker uit stamdata verwijderen.

BLOK D — BUSSEN / PRODUCTCONTROLE

Toon standaard waarden uit locatie/planning.

Velden:
- 10m bus aantal
- 11m bus aantal
- 12m bus aantal

Daarnaast:
- totaal bussen automatisch berekenen
- geplande minuten/uren tonen indien bestaande berekening beschikbaar is
- gecontroleerd-vinkje of status

Berekening:
- totaal_bussen = bus_10m + bus_11m + bus_12m
- indien minutes_per_product beschikbaar:
  planned_minutes = totaal_bussen × minutes_per_product
  planned_hours = planned_minutes / 60
  afronden volgens bestaande round_up indien aanwezig

Belangrijk:
- busvelden zijn dagcontrole-data.
- hiermee wordt de opdracht bevestigd.

==================================================
8. LAATSTE BEVESTIGING
==================================================

Na klikken op “Bevestigen” in drawer:

toon korte laatste bevestigingspopup:

- tijden akkoord?
- personeel akkoord?
- bussen akkoord?
- Telegram/adres eventueel verzonden?

Daarna status:
- Gepland → Bevestigd

Toon op tegel:
- statusbadge Bevestigd
- groene vink zichtbaar
- bijgewerkte medewerkers/tijden/bussen

==================================================
9. DATABASE / MIGRATIE
==================================================

Controleer bestaande planningtabellen eerst.

Mogelijke tabel:
- planning_days

Gebruik bestaande tabel.
Geen dubbele tabel maken.

Als velden ontbreken, maak minimale migratie, bijvoorbeeld:
supabase-setup/22_planning_day_confirmation.sql

Mogelijke benodigde velden:
- planning_days.status text default 'planned'
- planning_days.employee_status text default 'planned'
- planning_days.employee_start_time time nullable
- planning_days.employee_end_time time nullable
- planning_days.confirmed_at timestamptz nullable
- planning_days.confirmed_by uuid nullable
- planning_days.bus_10m_count integer default 0
- planning_days.bus_11m_count integer default 0
- planning_days.bus_12m_count integer default 0
- planning_days.telegram_sent_at timestamptz nullable
- planning_days.telegram_sent_by uuid nullable
- planning_days.telegram_status text nullable

Maar:
- voeg alleen toe wat echt nodig is.
- als er al audit/log/planning statusvelden bestaan, hergebruik die.
- geen bestaande data verwijderen.
- geen planningregels wissen.
- geen hard delete.

Let op:
Als planning_days één record per medewerker bevat, dan:
- medewerkerstatus en individuele tijden kunnen op planning_days staan.
- buscontrole en locatiebevestiging zijn groepsdata; gebruik bestaande groepsveld indien aanwezig.
- als er geen groepstabel bestaat, kan buscontrole tijdelijk op alle planning_days van dezelfde groep worden opgeslagen of minimale group/confirmation tabel worden toegevoegd.
- kies de kleinste veilige oplossing.

==================================================
10. UI / COMPONENTEN
==================================================

Maak zo klein mogelijk:

- planning card grouping helper
- PlanningDayConfirmDrawer of vergelijkbaar
- kleine employee row editor binnen drawer
- buscontroleblok

Geen grote herbouw van planningpagina.

==================================================
11. NIET DOEN
==================================================

- Geen volledige planningmodule herbouwen.
- Geen database-reset.
- Geen dummydata wijzigen.
- Geen vaste planning wijzigen.
- Geen werkregimes wijzigen.
- Geen soort opdrachten wijzigen.
- Geen locatiebeheer wijzigen behalve lezen van velden.
- Geen personeelsbeheer wijzigen behalve lezen/selecteren.
- Geen documenten/loonstroken wijzigen.
- Geen auth/rechten wijzigen.
- Geen hard delete.
- Geen echte Telegram API integratie met secrets in frontend.
- Geen mail/PDF bouwen.
- Geen urenregistratie/weekstaten bouwen.
- Geen brede redesign.

==================================================
12. CONTROLE DOOR LOVABLE
==================================================

Alleen:
- typecheck
- compile check

Gebruiker test functioneel zelf.

==================================================
13. OUTPUT KORT
==================================================

Rapporteer:
- Gewijzigde bestanden
- Databasewijziging ja/nee
- Migratiebestand naam indien aangemaakt
- Of planningkaarten nu gegroepeerd zijn per locatie/dag
- Welke grouping-key is gebruikt
- Waar dagcontrole drawer staat
- Welke velden per medewerker wijzigbaar zijn
- Welke busvelden zijn toegevoegd/gebruikt
- Hoe status Gepland → Bevestigd werkt
- Hoe vaste chauffeur wordt bepaald
- Hoe Google Maps-link wordt gegenereerd
- Wat Telegram-knop nu wel/niet doet
- Bevestiging dat geen echte Telegram secret/frontend integratie is gebouwd
- Typecheck resultaat
- Wat bewust niet is gebouwd
```

---

## 9. Openstaand vóór toepassing prompt

In de volgende chat eerst controleren:

1. Hoe `planning_days` exact is opgebouwd.
2. Of planningregel per medewerker is of per locatiegroep.
3. Welke velden al bestaan voor status, tijden en bevestiging.
4. Of er al een log/audit-tabel bestaat.
5. Of Telegram voorlopig alleen UI/log moet blijven.
6. Of buscontrole beter in `planning_days` of aparte groepsconfirmatie-tabel moet komen.

---

## 10. Belangrijke beslissing voor volgende chat

Niet meteen bouwen zonder check. Eerst kort schema inspecteren, dan definitieve Lovable-prompt toepassen.

Kernregel:

```text
Eerst alleen locatiekaart + dagcontrole.
Geen echte Telegram-integratie zolang er geen veilige backend/config is.
Geen planninggenerator opnieuw bouwen.
```
