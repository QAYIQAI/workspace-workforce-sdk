# Workspace Workforce — Vercel Deployment Overdrachtsnotitie

**Datum:** 2026-07-04  
**Project:** Workspace Workforce / `workspace-team-hub`  
**Repo:** `QAYIQAI/workspace-team-hub`  
**Productiehosting:** Vercel  
**Database/Auth:** Supabase  
**Builder:** Lovable  

---

## 1. Doel van deze notitie

Deze notitie legt de nieuwe deploymentworkflow vast:

```text
Lovable → GitHub → Vercel
```

Lovable wordt niet meer gebruikt als publieke productiehosting. Lovable blijft alleen de builder/preview-omgeving en synced code naar GitHub. Vercel is de enige productiehosting.

---

## 2. Eindstatus

```text
Lovable: builder + preview + GitHub sync
GitHub: centrale broncode
Vercel: productiehosting
Supabase: database, auth en storage
Lovable Publish: gestopt / afgeschermd
.env: verwijderd uit GitHub
Vercel env-vars: ingesteld
Backup: gemaakt na herstel
```

Productie-URL:

```text
https://workspace-team-hub.vercel.app
```

---

## 3. GitHub-repository

Repository:

```text
QAYIQAI/workspace-team-hub
```

Branch:

```text
main
```

Belangrijke bestanden/mappen:

```text
.lovable/
docs/
public/
src/
supabase-setup/
.gitignore
AGENTS.md
bun.lock
bunfig.toml
components.json
eslint.config.js
package.json
tsconfig.json
vite.config.ts
```

`.env` is verwijderd uit de GitHub-root.

`.gitignore` bevat nu minimaal:

```gitignore
.env
.env.local
.env.*.local
```

---

## 4. Vercel configuratie

Vercel project:

```text
workspace-team-hub
```

Vercel detecteerde het project als:

```text
TanStack Start
```

Build settings zijn op automatisch/default gelaten:

```text
Root Directory: ./
Build Command: automatisch
Output Directory: dist
Install Command: automatisch
```

Production branch:

```text
main
```

Laatste controles:

```text
Vercel deployment: Ready
Production deployment: Ready
Automatische deployment vanaf GitHub main: werkt
```

---

## 5. Vercel environment variables

In Vercel staan deze environment variables ingesteld voor **Production and Preview**:

```env
VITE_SUPABASE_URL=https://edrscbtcnfacpunwoana.supabase.co
VITE_SUPABASE_PUBLISHABLE_KEY=sb_publishable_...
VITE_SUPABASE_PROJECT_ID=edrscbtcnfacpunwoana
```

Belangrijk:

```text
Geen service-role key in frontend.
Geen OpenAI/API secrets in GitHub.
Geen database-passwords in GitHub.
```

De Supabase publishable key is frontend/public en mag in Vercel env-vars staan. Beveiliging moet via Supabase RLS/policies worden afgedwongen.

---

## 6. Supabase URL Configuration

Supabase project:

```text
edrscbtcnfacpunwoana
```

Site URL:

```text
https://workspace-team-hub.vercel.app
```

Redirect URLs:

```text
https://id-preview--7c61649f-7c06-49ce-8b95-c6d8fb35b139.lovable.app
https://id-preview--7c61649f-7c06-49ce-8b95-c6d8fb35b139.lovable.app/*
https://id-preview--7c61649f-7c06-49ce-8b95-c6d8fb35b139.lovable.app/reset-password
https://workspace-team-hub.vercel.app
https://workspace-team-hub.vercel.app/*
http://localhost:5173/*
```

De Lovable preview URLs zijn voorlopig blijven staan zodat preview/test in Lovable bruikbaar blijft.

---

## 7. Lovable Publish-status

Lovable public hosting is gestopt of afgeschermd.

Belangrijke afspraak:

```text
Niet meer op Publish klikken.
Niet meer op Update Lovable site klikken.
Lovable URL niet meer gebruiken als productie.
```

Lovable blijft alleen voor:

```text
Preview
Bouwen
Codewijzigingen
GitHub sync
```

Productie loopt uitsluitend via Vercel.

---

## 8. Kritiek incident tijdens deployment

### Probleem

Na Vercel deployment waren deze onderdelen leeg of laadden niet:

```text
Vaste planning
Werkregimes
```

Network gaf 400-errors op:

```text
locations
assignments
work_regimes
```

Uiteindelijk bleek de echte fout:

```text
PostgREST error=42703
```

Betekenis:

```text
column does not exist
```

### Oorzaak

Lovable had nieuwe frontend-code naar GitHub gesynced die nieuwe databasekolommen verwachtte:

```text
locations.ends_next_day
work_regimes.ends_next_day
assignments.ends_next_day
planning_days.ends_next_day
```

Maar de bijbehorende SQL-migratie was nog niet uitgevoerd in Supabase:

```text
supabase-setup/23_ends_next_day_overnight.sql
```

Daardoor vroeg de frontend kolommen op die nog niet bestonden.

### Belangrijk

Dit was geen dataverlies.

Als data weg was geweest, was het waarschijnlijk:

```text
200 OK + lege array
```

Maar de fout was:

```text
400 Bad Request / 42703 column does not exist
```

### Oplossing

De SQL-migratie is uitgevoerd:

```text
supabase-setup/23_ends_next_day_overnight.sql
```

Daarna kwamen de data en schermen terug.

---

## 9. Uitgevoerde SQL-migratie

Migratiebestand:

```text
supabase-setup/23_ends_next_day_overnight.sql
```

Volgens het Lovable-rapport bevat deze migratie:

```text
1. work_regimes.ends_next_day boolean not null default false
2. assignments.ends_next_day boolean not null default false
3. locations.ends_next_day boolean not null default false
4. planning_days.ends_next_day boolean not null default false
5. planning_days.planned_minutes herdefinieerd als generated stored column
6. generate_planning_for_week RPC aangepast voor overnight/nachtwerk
```

Prioriteit in de RPC:

```text
assignment → work_regime → location
```

Voorbeeld nachtberekening:

```text
19:00 → 04:00 + ends_next_day=true = 540 minuten
```

---

## 10. Backup

Na herstel is een backup gemaakt via de Backup-pagina in de applicatie.

Aanbevolen backuptype:

```text
Volledige databackup als JSON
Belangrijke onderdelen ook los als JSON + CSV
```

Belangrijke onderdelen:

```text
Personeel
Locaties
Vaste planning
Werkregimes
Opdrachtgevers
Gebruikers & rechten
```

Aanbevolen naamgeving:

```text
workspace-workforce-df-cleaning-backup-2026-07-04-na-ends-next-day-fix.json
```

---

## 11. Testresultaten

### Vercel productiecontrole

Gecontroleerd en akkoord:

```text
Login werkt
Dashboard opent
Planning opent
Locaties opent
Werkregimes opent
Vaste planning opent
Personeel opent
Instellingen opent
Backup-pagina opent
Mobiel akkoord
```

### GitHub → Vercel workflowtest

Er is een kleine testwijziging uitgevoerd in Lovable:

Bestand:

```text
src/routes/auth.tsx
```

Wijziging:

```text
Login-footertekst aangepast naar:
"Intern platform · Toegang uitsluitend via beheerder."
```

Resultaat:

```text
Lovable synced naar GitHub
Vercel deployde automatisch
Production status Ready
Vercel-productie toonde de wijziging
Lovable Publish was niet nodig
```

Conclusie:

```text
Lovable → GitHub → Vercel werkt zonder Publish
```

---

## 12. Nieuwe vaste deploymentworkflow

Vanaf nu altijd deze workflow gebruiken:

```text
1. Wijziging bouwen in Lovable
2. Lovable-rapport lezen
3. Controleren of SQL-migratie nodig is
4. Als SQL nodig is: eerst handmatig uitvoeren in Supabase
5. Lovable synced naar GitHub
6. Vercel deployt automatisch vanaf main
7. Vercel-productie testen
8. Backup maken na belangrijke databasewijzigingen
```

---

## 13. Kritieke werkregel voor Lovable-rapporten

Bij elk Lovable-rapport eerst zoeken naar:

```text
Migratie / SQL — nog uitvoeren
```

Als dit aanwezig is:

```text
1. Niet meteen testen/deployen als productiecontrole
2. Eerst SQL uitvoeren in Supabase SQL Editor
3. Daarna Vercel deployment controleren
4. Daarna backup maken
5. Daarna pas verder bouwen
```

Deze regel voorkomt opnieuw 400/42703-fouten door schema/query mismatch.

---

## 14. Standaardpromptregel voor toekomstige Lovable-builds

Gebruik bovenaan toekomstige Lovable-prompts:

```text
BUDGET / CREDITS
Werk extreem credit-zuinig. Geen redesigns. Geen brede refactors. Geen database-reset. Geen RLS-wijzigingen tenzij expliciet gevraagd. Geen auth-wijzigingen. Geen service-role key in frontend. Geen auth-admin in frontend.

HARDE BEVEILIGINGSEISEN
Niet publishen vanuit Lovable. Niet deployen vanuit Lovable. Alleen code aanpassen en sync naar GitHub. Productie loopt uitsluitend via Vercel. Geen secrets toevoegen. Geen .env terugplaatsen in GitHub.

BELANGRIJK
Als er SQL nodig is: maak alleen een migratiebestand in supabase-setup en rapporteer dat ik die eerst handmatig in Supabase moet uitvoeren. Niet aannemen dat databasewijzigingen live zijn.
```

---

## 15. Niet meer doen

```text
Lovable Publish gebruiken als productie
Lovable Update gebruiken als productie
.env terugzetten in GitHub
Service-role key in frontend zetten
Database/RLS/auth wijzigen zonder expliciete opdracht
Vercel env-vars verwijderen
Supabase redirect URLs verwijderen zonder controle
```

---

## 16. Veilig vervolg

Volgende technische vervolgstappen kunnen later stapgewijs:

```text
1. Custom domain koppelen aan Vercel
2. vercel.json/fallback controleren indien interne route-refresh ooit 404 geeft
3. Deployment checklist documenteren in projectdocs
4. Backup/restoreprocedure verder formaliseren
5. SQL-migratieproces strakker maken met nummering en changelog
```

---

## 17. Samenvatting in één schema

```text
Lovable
  ↓ code bouwen + GitHub sync
GitHub main
  ↓ automatische deployment
Vercel productie
  ↓ runtime env-vars
Supabase database/auth/storage
```

Productiebron:

```text
Vercel
```

Niet-productie:

```text
Lovable preview
```

