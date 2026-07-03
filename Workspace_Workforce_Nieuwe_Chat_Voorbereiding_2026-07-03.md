# Workspace Workforce — Nieuwe Chat Voorbereiding

**Datum:** 2026-07-03  
**Project:** Workspace Workforce / DF Cleaning BV  
**Doel:** korte overdracht voor vervolgchat, zodat direct verder gewerkt kan worden zonder contextverlies.

---

## 1. Werkafspraak voor Lovable-prompts

Elke bouwprompt blijft credit-zuinig en start met:

```text
BUDGET / CREDITS
HARDE BEVEILIGINGSEISEN
```

Vaste regels:

- Geen brede refactors.
- Geen redesigns zonder expliciete opdracht.
- Geen database-reset.
- Geen auth/rechten wijzigen zonder expliciete opdracht.
- Geen service-role key in frontend.
- Geen `auth.admin` in frontend.
- Geen `supabase.auth.signUp()` in frontend.
- Geen hard delete tenzij expliciet en veilig via server-side functie.
- Gebruiker test functioneel zelf.
- Lovable doet alleen typecheck / compile check.

---

## 2. Planningtegel-flow — definitief uitgangspunt

De planningtegels moeten gelden voor:

```text
Planning → Agenda → Dag
Planning → Werkbrief
```

Deze twee schermen moeten dezelfde gedeelde component gebruiken, bijvoorbeeld:

```text
LocationDayCard
```

### Tegelstructuur

Niet:

```text
1 tegel per medewerker
```

Wel:

```text
1 locatie + 1 datum + 1 soort opdracht = 1 tegel
```

Voorbeeld:

```text
HSU Barendrecht (4)
[Tussenbeurt] [Chauffeur: Yusuf Kaya] [Status: Gepland/Gecontroleerd]

Locatie: 08:30–17:00

1. Milan Acar — 08:00–17:30
2. Piotr Kowalski — 08:00–17:30
3. Fatima Benali — 08:00–17:30
4. Sara Demir — 08:00–17:30

[✓ Bevestigen] [Telegram] [Rewind]
```

Belangrijk:

- Individuele medewerkertijden mogen de grouping niet splitsen.
- Primaire grouping-key:

```text
date + location_id + job_type_id
```

Tijdblok mag alleen als extra grouping worden gebruikt als het echt een locatie-/opdrachtblok is, niet als individuele medewerkerstart/eindtijd.

---

## 3. Data op de tegel

Op elke tegel:

- Locatienaam.
- Aantal medewerkers tussen haakjes.
- Soort opdracht als badge.
- Chauffeur als badge indien bekend.
- Statusbadge: Gepland / Gecontroleerd.
- Locatietijd.
- Genummerde medewerkerslijst.
- Per medewerker eigen starttijd en eindtijd.
- Actieknoppen:
  - Bevestigen.
  - Telegram.
  - Rewind.
- Locatie/Maps-link rechtsonder of onderaan bij actieknoppen.

---

## 4. Tijdlogica

### Locatietijd

Algemene tijd van locatie/opdrachtkaart.

Prioriteit:

```text
1. locations.start_time / locations.end_time
2. planninggroep/opdracht-tijd indien aanwezig
3. vroegste starttijd en laatste eindtijd van medewerkers binnen de groep
4. work_regimes.start_time / work_regimes.end_time
5. leeg
```

### Medewerkertijd

Individuele tijd per medewerker.

Prioriteit:

```text
1. planning_days.employee_start_time / employee_end_time indien aanwezig
2. planning_days.planned_start / planned_end
3. assignments.start_time / assignments.end_time
4. work_regimes.start_time / work_regimes.end_time
5. locations.start_time / locations.end_time
```

---

## 5. Kleur- en chauffeurlogica

### Kleur

Tegelkleur komt uit Soort opdrachten:

```text
1. planning_days.job_type_id → job_types.color
2. locations.job_type_id → job_types.color
3. work_regimes.color als fallback
4. neutraal
```

Niet doen:

- Geen hardcoded planningkleuren.
- Geen employee-kleur als primaire tegelkleur.
- Geen nieuwe vaste kleurmapping.

### Chauffeur

Bronvolgorde:

```text
1. locations.fixed_driver_employee_id
2. medewerker binnen dezelfde groep met employee_type = Chauffeur
3. geen chauffeurbadge
```

In de dagcontrole drawer moet chauffeur handmatig gekozen kunnen worden voor deze locatie/dag, zonder automatisch vaste stamdata te wijzigen.

---

## 6. Google Maps-link — werkende methode

Na test werkt deze methode:

```text
https://www.google.com/maps/search/?api=1&query=<encoded fullAddress>
```

Werkend voorbeeld:

```text
https://www.google.com/maps/search/?api=1&query=Logistiekweg%202%2C%202991%20LN%20Barendrecht%2C%20Nederland
```

Belangrijke adresopbouw:

```text
fullAddress = address + ", " + postal_code + " " + city + ", " + country
```

Goed:

```text
Logistiekweg 2, 2991 LN Barendrecht, Nederland
```

Niet:

```text
Logistiekweg 2, 2991 LN, Barendrecht, Nederland
```

Technische eis:

```text
mapsUrl = "https://www.google.com/maps/search/?api=1&query=" + encodeURIComponent(fullAddress)
```

Gebruik dezelfde helper overal:

```text
buildGoogleMapsLink(location)
→ fullAddress
→ mapsUrl
```

De Maps-link moet gebruikt worden in:

1. Rechtsonder/onderaan op de tegel als klikbare locatie/map-link.
2. Telegram-preview op de tegel.
3. Dagcontrole drawer.
4. Telegram-preview in de drawer.

Anchor-eis:

```html
<a href={mapsUrl} target="_blank" rel="noopener noreferrer">
```

Geen Google API-key, geen Maps JavaScript API, geen externe API-call.

---

## 7. Actieknoppen op tegel

Elke tegel heeft exact:

```text
[✓ Bevestigen] [Telegram] [Rewind]
```

### Bevestigen

- Opent dagcontrole drawer/popup.
- Mag niet direct `planning_days.status` wijzigen.
- Statuswijziging pas na `Controle afronden` in drawer.

### Telegram

- Toont alleen berichtvoorvertoning.
- Geen echte verzending.
- Geen API-call.
- Geen bot token of secret in frontend.
- Geen fake succesmelding.

### Rewind

- Draait controle/bevestiging terug.
- Zet groep terug naar bewerkbare/niet-gecontroleerde status, bijvoorbeeld `gepland`.
- Werkt op dezelfde groep:

```text
date + location_id + job_type_id
```

- Geen data verwijderen.
- Geen planningregels verwijderen.

---

## 8. Telegram-preview

Bij klik op Telegram, op tegel of in drawer:

```text
Beste chauffeur [chauffeursnaam],

Dit is het adres:
[fullAddress]

Google Maps-link:
[mapsUrl]

Dit zijn de mensen die aan u zijn toevertrouwd:
1. [medewerker 1]
2. [medewerker 2]
3. [medewerker 3]
4. [medewerker 4]
```

Gebruik:

- Chauffeur uit tegel/drawer-context.
- `fullAddress` uit centrale Maps-helper.
- `mapsUrl` uit centrale Maps-helper.
- Medewerkers uit dezelfde locatiegroep.

---

## 9. Dagcontrole drawer/popup

Klik op Bevestigen opent de drawer voor dezelfde locatiegroep.

### Blok A — Locatie / adres / Telegram

Toon:

- Locatienaam.
- Volledig adres.
- Google Maps-link.
- Chauffeur indien bekend.
- Mogelijkheid om chauffeur handmatig te kiezen voor deze dag/locatie.
- Telegram-knop.
- Telegram-preview.

### Blok B — Locatietijd

Toon:

- Locatie starttijd.
- Locatie eindtijd.
- Pauze indien beschikbaar.

Wijzigt alleen deze dagcontrole, niet vaste planning.

### Blok C — Medewerkerscontrole

Per medewerker:

- Nummer.
- Naam.
- Starttijd.
- Eindtijd.
- Status.
- Actieknoppen.

Statusopties minimaal:

```text
Gepland
Gewerkt
Ziek
Afwezig
Vervangen
```

Acties:

- Status wijzigen.
- Starttijd wijzigen.
- Eindtijd wijzigen.
- Medewerker vervangen.
- Medewerker verwijderen uit deze dag.
- Nieuwe medewerker toevoegen aan deze locatie/dag.

Belangrijk:

- Geen medewerker uit stamdata verwijderen.
- Geen hard delete.
- Geen vaste planning structureel wijzigen.

### Blok D — Bussen / productcontrole

Velden:

```text
10m bus aantal
11m bus aantal
12m bus aantal
```

Berekening:

```text
totaal_bussen = bus_10m + bus_11m + bus_12m
```

Als `minutes_per_product` bestaat:

```text
planned_minutes = totaal_bussen × minutes_per_product
planned_hours = planned_minutes / 60
```

Standaardwaarden moeten uit locatiegegevens/planningdata komen als die bestaan.

### Blok E — Laatste controle

Onderaan:

```text
[Annuleren] [Controle afronden]
```

Bij Controle afronden eerst akkoord-popup:

```text
Controle afronden voor deze locatiekaart?
```

Controlepunten:

- Tijden akkoord?
- Personeel akkoord?
- Bussen akkoord?
- Telegram/adres gecontroleerd?

Pas na akkoord:

```text
planning_days.status = "gecontroleerd"
```

voor alle regels binnen:

```text
date + location_id + job_type_id
```

Daarna invalidate/refetch:

```text
["planning:days"]
```

Agenda Dag en Werkbrief tonen dan dezelfde status.

---

## 10. Dashboard — Wie werkt er vandaag?

Wens: op Dashboard een sectie:

```text
Wie werkt er vandaag?
```

Deze moet de bovenstaande locatiekaartinformatie tonen in **kanban style** over de volledige canvas-/beeldbreedte.

Visuele richting:

- Brede dashboardcanvas.
- Kanban-kolommen mogelijk per:
  - Soort opdracht.
  - Locatiegebied.
  - Tijdlijn/compact overzicht.
- Kaarten gebruiken dezelfde informatie als LocationDayCard:
  - locatie
  - aantal medewerkers
  - soort opdracht
  - chauffeur
  - locatietijd
  - genummerde medewerkers met tijden
  - Maps-link
  - Bevestigen / Telegram / Rewind
- Visuele voorbeelden zijn gegenereerd in de vorige chat als referentie.

Nog niet definitief gebouwd; eerst als vervolgstap uitwerken.

---

## 11. Gebruikers & rechten — huidige stand

Scherm:

```text
Instellingen → Gebruikers & rechten
```

Huidige situatie:

- Status `active` bestaat.
- Status `suspended` bestaat.
- Inactief/heractiveren bestaat al.
- Oranje sleutel is voor wachtwoord opnieuw genereren/resetten, niet voor verwijderen.
- Verwijderen laten we voorlopig weg.

Acties zichtbaar:

- Blauwe pen: bewerken.
- Oranje sleutel: wachtwoord opnieuw genereren.
- Rood verbodsteken: waarschijnlijk blokkeren/suspended.
- Groene check: heractiveren.

---

## 12. Probleem: 3e user aangemaakt maar niet zichtbaar

Gebruiker aangemaakt, succesmelding ontvangen, maar user niet zichtbaar in UI.

Analyse Lovable:

- User bestaat wel in `auth.users`.
- User bestaat ook in `public.profiles`.
- User ontbreekt in `public.memberships`.
- User ontbreekt in `public.user_roles`.
- UI toont gebruikers via:

```text
memberships where company_id = activeCompanyId
→ profiles
→ user_roles
```

Conclusie:

```text
Zonder membership/user_roles valt user buiten de lijst.
```

Meest waarschijnlijke oorzaak:

- User is buiten de correcte admin-flow aangemaakt, of
- create-flow maakt wel auth/profile aan maar niet membership + user_roles, of
- succesmelding komt te vroeg.

---

## 13. SQL-check voor Supabase

Eerst checken:

```sql
select
  id,
  email,
  first_name,
  last_name,
  created_at,
  created_by,
  must_change_password
from public.profiles
where lower(email) in (
  lower('info@nozam-group.nl'),
  lower('info@nizam-group.nl')
);
```

Memberships:

```sql
select *
from public.memberships
where user_id = 'USER_ID_HIER';
```

Voor actieve company:

```sql
select *
from public.memberships
where user_id = 'USER_ID_HIER'
  and company_id = 'COMPANY_ID_HIER';
```

User roles:

```sql
select *
from public.user_roles
where user_id = 'USER_ID_HIER';
```

Voor actieve company:

```sql
select *
from public.user_roles
where user_id = 'USER_ID_HIER'
  and company_id = 'COMPANY_ID_HIER';
```

Kolomstructuur checken vóór insert:

```sql
select
  column_name,
  data_type,
  is_nullable,
  column_default
from information_schema.columns
where table_schema = 'public'
  and table_name = 'memberships'
order by ordinal_position;
```

```sql
select
  column_name,
  data_type,
  is_nullable,
  column_default
from information_schema.columns
where table_schema = 'public'
  and table_name = 'user_roles'
order by ordinal_position;
```

Mogelijke fix pas daarna, afhankelijk van verplichte kolommen:

```sql
insert into public.memberships (
  user_id,
  company_id,
  status,
  created_at
)
values (
  'USER_ID_HIER',
  'COMPANY_ID_HIER',
  'active',
  now()
)
on conflict do nothing;
```

```sql
insert into public.user_roles (
  user_id,
  company_id,
  role,
  created_at
)
values (
  'USER_ID_HIER',
  'COMPANY_ID_HIER',
  'planner',
  now()
)
on conflict do nothing;
```

Let op: insert aanpassen als tabellen extra verplichte kolommen hebben.

---

## 14. Promptidee voor Lovable — alleen create-flow controleren

```text
BUDGET / CREDITS
ALLEEN CONTROLEREN. Niet bouwen. Niet verwijderen. Niet refactoren.

HARDE BEVEILIGINGSEISEN
- Geen service-role key in frontend.
- Geen auth.admin in frontend.
- Geen supabase.auth.signUp in frontend.
- Geen users verwijderen.
- Geen RLS aanpassen.
- Geen database wijzigen zonder mijn akkoord.

PROBLEEM
Ik maak een gebruiker aan via Gebruikers & rechten > Gebruiker aanmaken. Ik krijg succesmelding, maar de gebruiker verschijnt niet in de lijst.

CONTEXT
De lijst toont users via memberships voor activeCompanyId. De nieuwe user lijkt wel in public.profiles te staan, maar mist mogelijk public.memberships en public.user_roles.

CONTROLEER
1. Welke functie wordt aangeroepen bij "Gebruiker aanmaken".
2. Of na auth/profile create ook membership wordt aangemaakt.
3. Of na auth/profile create ook user_roles wordt aangemaakt.
4. Of de gekozen rol uit de drawer daadwerkelijk wordt opgeslagen.
5. Of activeCompanyId wordt meegegeven.
6. Of succesmelding pas na alle inserts wordt getoond.
7. Of bij fout in membership/user_roles toch succes wordt getoond.
8. Of query invalidation daarna correct gebeurt.

OUTPUT
Rapporteer:
- Welke create-functie gebruikt wordt.
- Of membership-insert aanwezig is.
- Of user_roles-insert aanwezig is.
- Of activeCompanyId wordt meegestuurd.
- Of rol wordt opgeslagen.
- Waar de flow stopt.
- Waarom succesmelding verschijnt terwijl user niet zichtbaar is.
- Geen wijziging uitvoeren.
```

---

## 15. Belangrijk voor vervolgchat

Openstaande keuzes:

1. Eerst user via SQL herstellen of eerst create-flow door Lovable laten controleren.
2. Verwijderen voorlopig niet bouwen; `suspended` bestaat al.
3. Planningtegel/drawer prompt staat klaar en moet zorgvuldig toegepast worden.
4. Google Maps-linkmethode is getest en moet overal centraal gebruikt worden.
5. Dashboard “Wie werkt er vandaag?” kan later als aparte dashboard/kanban feature worden uitgewerkt.

