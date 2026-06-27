# Migratie Codex naar Lovable

Dit document beschrijft hoe een Codex-prototype wordt overgezet naar Lovable.

## Doel

Een lokaal gebouwd prototype moet worden vertaald naar een Lovable/Supabase productstructuur zonder functionele kennis te verliezen.

## Stappen

1. Inventariseer bestaande modules.
2. Leg datamodel vast.
3. Leg rollen en rechten vast.
4. Leg UI-flows vast.
5. Leg mobiele eisen vast.
6. Maak Supabase-tabellen.
7. Maak RLS policies.
8. Bouw layout in Lovable.
9. Bouw modules gefaseerd.
10. Test per module.
11. Vergelijk met prototype.
12. Migreer data indien nodig.

## Niet blind kopiëren

Een Codex-prototype kan andere keuzes bevatten dan Lovable.

Niet blind overnemen:

- Lokale storagepaden
- SQLite-specifieke queries
- Hardcoded sessies
- Prototype CSS
- Tijdelijke testdata
- Onveilige routes

## Wel overnemen

- Functionele flow
- Rollen
- Rechten
- Entiteiten
- Statussen
- Mobiele oplossingen
- Exportlogica
- Validaties
