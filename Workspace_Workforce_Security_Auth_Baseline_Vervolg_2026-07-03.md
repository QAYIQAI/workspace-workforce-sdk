# Workspace Workforce / Webplatform Uitzend — Security & Auth Baseline

**Datum:** 2026-07-03  
**Doel:** korte overdrachtsnotitie voor vervolgchat / vervolgontwikkeling.  
**Status:** werkende baseline met enkele openstaande P0/mobile punten.  
**Project:** Workspace Workforce / Webplatform Uitzend  
**Standaard:** AI_CHARTER.md + README2.md  

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
- Geen nieuwe modules combineren met security fixes.
- Geen databasewijzigingen tenzij strikt noodzakelijk.
- Geen service-role key in frontend.
- Eerst kleine patches, daarna handmatige controle door eigenaar.

---

## 2. Uitgevoerde security/auth fases

### Fase 1 — Signup / registratie verwijderd

Uitgevoerd:

- Signup-tab verwijderd uit `src/routes/auth.tsx`.
- `supabase.auth.signUp()` volledig verwijderd uit frontend.
- Alleen login blijft beschikbaar.
- Supabase instelling gecorrigeerd:
  - Email login: aan.
  - Allow new users to sign up: uit.
- `noindex`, `nofollow`, robots-blokkade en SEO/social-meta cleanup uitgevoerd.
- Migratie 13 uitgevoerd:
  - `handle_new_user()` maakt alleen nog `profiles` aan.
  - Geen automatische company.
  - Geen automatische membership.
  - Geen automatische `admin` / `super_admin`.

### Fase 1B — Uitnodigingen verwijderd

Uitgevoerd:

- Uitnodigingen-tab verwijderd.
- Invite-flow verwijderd uit UI.
- Dashboard-tegel voor open uitnodigingen verwijderd.
- `user_invites` tabel blijft bestaan maar wordt niet gebruikt.
- Geen invite-mails.
- Geen activatielinks.

### Fase 1C-A — Database voorbereid voor directe gebruiker-aanmaak

Migratie 14 uitgevoerd.

Toegevoegd:

- `profiles.must_change_password boolean NOT NULL DEFAULT true`
- `profiles.created_by uuid`
- `public.is_admin(_user uuid)`
- `profiles_guard_self_update`

Belangrijk:

- `handle_new_user()` blijft veilig.
- Alleen profielrij wordt aangemaakt.
- Geen automatische rechten.
- Geen automatische memberships.

### Fase 1C-B — Server-side gebruiker aanmaken

Uitgevoerd:

- Server-only admin create-user function gebouwd.
- Secrets ingesteld als:
  - `WW_SUPABASE_URL`
  - `WW_SUPABASE_PUBLISHABLE_KEY`
  - `WW_SUPABASE_SERVICE_ROLE_KEY`

Gecontroleerd:

- Service role is niet zichtbaar in frontend.
- Niet in `VITE_` variabelen.
- Niet in browser bundle.
- Alleen server-side gebruikt.

### Fase 1C-C — Admin UI gebruiker aanmaken

Uitgevoerd:

- Admin/super_admin kan direct gebruiker aanmaken.
- Geen invite.
- Geen e-mail.
- Geen activatielink.
- Tijdelijk wachtwoord wordt getoond.
- Copy-knop toegevoegd.
- Nieuwe user krijgt `must_change_password=true`.

### Password reset / actief-inactief

Uitgevoerd:

- Admin kan nieuw tijdelijk wachtwoord genereren.
- Password reset gebruikt server-side function.
- Tijdelijk wachtwoord wordt niet gelogd.
- `must_change_password=true` wordt gezet na reset.
- Actief/inactief werkt via `memberships.status`:
  - `active`
  - `suspended`
- Geen hard delete gebouwd.
- Geen prullenbak.
- Alleen activeren/deactiveren.

### Gebruikerslijst hersteld

Situatie:

- Gebruikerslijst toonde tijdelijk 0 gebruikers.
- Supabase-data bleek correct.
- UI-query is hersteld.

Resultaat:

- Admin ziet actieve users.
- Admin ziet suspended users.
- Company roles en platform roles worden correct meegenomen waar relevant.

### Mobiel

Status:

- Mobiel had even tijd nodig maar werkt nu grotendeels goed.
- Mobiele gebruikersbeheerweergave is gelijkgetrokken met desktop.
- Geen mobiele register/signup/invite-flow meer zichtbaar.
- Mobiele user-acties werken met icon-only acties:
  - blauwe pen = bewerken.
  - oranje sleutel = nieuw wachtwoord.
  - rood verbodsteken = deactiveren.
  - groene check = activeren.
- Geen prullenbak / hard delete.

---

## 3. Openstaand probleem mobiel

### Probleem

Op mobiel is nog één belangrijk UX/security-shell probleem open:

- Er is geen duidelijke **Log uit** knop zichtbaar.
- Het is niet duidelijk **wie is ingelogd**.

### Gewenst gedrag mobiel

In de mobiele drawer/menu moet altijd zichtbaar of duidelijk bereikbaar zijn:

- echte gebruiker:
  - `profiles.full_name` als beschikbaar;
  - fallback: `auth.user.email`;
  - fallback laatste optie: `Gebruiker`.
- rol apart/subtiel:
  - bijvoorbeeld `Beheerder` of `Super admin`;
  - rol mag niet de persoonsnaam vervangen.
- knoplabel:
  - `Log uit`
  - niet `Uit`.

### Layout-eis mobiel

- Mobile menu/drawer mag scrollen.
- Gebruiker-info en logout moeten onderaan duidelijk bereikbaar blijven.
- Bij lange menu’s mag logout niet verdwijnen.
- Geen redesign; alleen shell/footer fix.

---

## 4. Openstaande P0/P1 punten

### 4.1 Forced password change

Nog te controleren/fixen:

- Nieuwe gebruiker met `must_change_password=true` moet verplicht naar `/wachtwoord-wijzigen`.
- Dashboard/menu/routes moeten geblokkeerd blijven zolang `must_change_password=true`.

Er was eerder een fout:

```text
Current password required when setting new password
```

Correcte oplossingsrichting:

- Niet client-side `supabase.auth.updateUser({ password })` gebruiken voor first-login flow.
- Wel server-side function gebruiken, bijvoorbeeld:

```text
changeOwnTemporaryPassword
```

Deze function moet:

- ingelogde gebruiker controleren;
- alleen eigen wachtwoord wijzigen;
- alleen toestaan als `profiles.must_change_password=true`;
- service-role alleen server-side gebruiken;
- `profiles.must_change_password=false` zetten na succes;
- geen wachtwoorden loggen.

### 4.2 Suspended user blokkeren

Nog te controleren/fixen:

- Suspended user mag niet alleen “geen data” zien.
- Correct gedrag:
  - geen dashboard;
  - geen menu;
  - geen platformshell;
  - blokkeren of direct uitloggen.

Databasewaarde:

```text
memberships.status = 'suspended'
```

### 4.3 15 minuten idle logout

Nog nodig volgens AI Charter:

- Automatische logout na 15 minuten inactiviteit.
- Timer reset bij:
  - mousemove;
  - mousedown;
  - keydown;
  - touchstart;
  - scroll.
- Subtiele timer tonen, bijvoorbeeld:

```text
Sessie 14:59
```

### 4.4 Sidebar/logout desktop

Nog te controleren/fixen:

- Linkse sidebar moet viewport-hoog blijven.
- Rechter content moet zelfstandig scrollen.
- Logout/gebruiker-info mag niet naar beneden verdwijnen bij lange content.
- Label moet `Log uit` zijn, niet `Uit`.
- Gebruiker-info moet echte naam/e-mail tonen, niet alleen `Beheerder`.

---

## 5. Belangrijke technische feiten

### Database enums

`membership_status` gebruikt:

```text
active
invited
suspended
```

Niet gebruiken:

```text
inactive
```

`app_role` gebruikt:

```text
admin
super_admin
```

Niet gebruiken:

```text
tenant_owner
super_owner
tenant_admin
```

---

## 6. Belangrijke user/company data

Company:

```text
DF Cleaning BV
company_id = cb114721-95b7-4dc0-a2f0-64f068f5f1f0
```

Owner/admin:

```text
ai_consult@qayiq.be
role: admin + super_admin
```

Testuser:

```text
dekker_123@hotmail.com
role: admin
status: active/suspended afhankelijk van test
must_change_password=true bij nieuw/reset password
```

---

## 7. Compacte volgende Lovable prompt

Gebruik deze prompt als eerste actie in de nieuwe chat als de mobile logout/user-info nog open staat.

```text
BUDGET / CREDITS
MINIMALE MOBILE SHELL HOTFIX. Bouw alleen de mobiele logout/gebruiker-info fix. Geen databasewijzigingen. Geen serverfunction-wijzigingen. Geen auth-guard wijzigingen. Geen redesign. Geen nieuwe features.

HARDE BEVEILIGINGSEISEN
- Geen signup
- Geen invite
- Geen activatielink
- Geen e-mailflow
- Geen service-role key in frontend
- Logout moet altijd bereikbaar zijn
- De ingelogde gebruiker moet duidelijk zichtbaar zijn

PROBLEEM
Op mobiel is geen duidelijke Log uit knop zichtbaar en het is niet duidelijk wie is ingelogd.

DOEL
Maak in de mobiele drawer/menu duidelijk zichtbaar:
1. De echte ingelogde gebruiker.
2. De rol subtiel apart.
3. Een duidelijke knop met label: Log uit.

TE DOEN
Controleer mobile drawer/menu/sidebar in:
- src/components/app-shell.tsx
- src/routes/_authenticated.tsx
- src/hooks/use-auth.ts
- eventuele mobile menu/drawer/sheet componenten

Gebruiker-info:
- Toon profiles.full_name als beschikbaar en niet leeg.
- Fallback: auth.user.email.
- Fallback laatste optie: Gebruiker.
- Toon rol apart/subtiel, bijvoorbeeld Beheerder of Super admin.
- Rol mag niet de hoofdnaam vervangen.

Logout:
- Knoplabel moet exact zijn: Log uit.
- Niet: Uit.
- Knop moet zichtbaar of duidelijk bereikbaar zijn in mobiele drawer.
- Bij lange menu's mag logout niet verdwijnen.
- Menu mag scrollen, maar footer met gebruiker-info/logout moet bereikbaar blijven.

NIET DOEN
- Geen redesign.
- Geen nieuwe navigatiestructuur.
- Geen auth-flow wijzigen.
- Geen databasewijziging.
- Geen serverfunction-wijziging.
- Geen signup/invite/e-mail/activatielink.

CONTROLE
- Typecheck.
- Mobiel: gebruiker-info zichtbaar.
- Mobiel: Log uit zichtbaar.
- Mobiel: echte naam/e-mail zichtbaar, niet alleen Beheerder.
- Desktop niet breken.
- Geen SERVICE_ROLE/auth.admin/signUp in frontend.

OUTPUT KORT
Rapporteer:
- Gewijzigde bestanden
- Waar mobiele gebruiker-info staat
- Waar mobiele Log uit knop staat
- Typecheck resultaat
- Bevestiging geen database/server wijzigingen
```

---

## 8. Prioriteit vervolg

Aanbevolen volgorde:

1. Mobile shell hotfix: gebruiker-info + Log uit zichtbaar.
2. Forced password change met server-side `changeOwnTemporaryPassword`.
3. Suspended user volledig blokkeren vóór platformshell.
4. 15 minuten idle logout + subtiele timer.
5. Daarna pas verdere modules zoals planning, agenda, documenten, loonstroken.
