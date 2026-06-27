# Tech Stack

Workspace Workforce SDK is technologie-onafhankelijk, maar bevat een aanbevolen stack voor productieontwikkeling.

## Aanbevolen productiestack

- Frontend: React + TypeScript
- Build tool: Vite
- Styling: Tailwind CSS
- UI: shadcn/ui met eigen Workspace-componentlaag
- Backend: Node.js met Express of NestJS
- Database: PostgreSQL
- ORM: Prisma of Drizzle
- Auth: Supabase Auth, Auth.js of eigen sessie-auth
- Storage: S3-compatible private storage of Supabase Storage
- API: REST /api/v1
- Jobs: BullMQ + Redis
- PDF: Playwright of Puppeteer
- XLSX: exceljs
- Validation: Zod
- Testing: Vitest + Playwright
- Monitoring: Sentry + uptime monitoring
- Deployment: Docker op VPS of managed cloud
- Automation: Make en/of n8n
- AI: OpenAI API via aparte AIService

## Prototype stack

Voor snelle lokale ontwikkeling:

- Node.js
- Express
- SQLite
- HTML/CSS/JS of React
- Lokale private storage
- Eigen sessie-auth
- PDF export
- GitHub

## SaaS stack

Voor cloud-native ontwikkeling:

- React + TypeScript
- Supabase PostgreSQL
- Supabase Auth
- Supabase Storage
- Row Level Security
- Vercel/Netlify/Cloudflare Pages
- Edge Functions of Node API
- Make/n8n
- OpenAI API

## Frontend principes

- Componentgebaseerd
- Responsive
- Dark mode geschikt
- Mobiel bruikbaar
- Toegankelijke formulieren
- Rechtenbewuste navigatie
- Herbruikbare tabellen
- Duidelijke statusbadges

## Backend principes

- Server-side validatie
- Server-side rechtencontrole
- Gestructureerde services
- Repositories voor database
- Auditlog bij kritieke mutaties
- Veilige upload- en downloadroutes
- API-versiebeheer

## Database principes

- PostgreSQL voor productie
- SQLite alleen voor prototypes
- Migraties verplicht
- Indexen voor filters
- Soft-delete waar passend
- Tenant_id voorbereid
- created_at en updated_at standaard
- Auditlogs voor kritieke acties

## Deployment opties

- VPS met Docker
- Managed hosting
- Supabase + frontend hosting
- Private klantomgeving
- Multi-tenant SaaS
