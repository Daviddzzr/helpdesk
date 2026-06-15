# Setup Plan

## Prerequisites

- **Bun** installed (`brew install bun` or https://bun.sh)
- **PostgreSQL** running locally (create two databases: `helpdesk` and `helpdesk_test`)

```sql
-- In psql:
CREATE DATABASE helpdesk;
CREATE DATABASE helpdesk_test;
```

## Tasks

- [ ] **Verify prerequisites** — Ensure Bun is installed and PostgreSQL is running locally
  - Run: `bun --version`
  - Create databases: `CREATE DATABASE helpdesk; CREATE DATABASE helpdesk_test;`

- [ ] **Install dependencies** — Install all workspace packages from root
  - Run: `bun install`

- [ ] **Copy environment files** — Copy `.env.example` files to `.env`
  - Run: `cp server/.env.example server/.env` and `cp client/.env.example client/.env`

- [ ] **Configure `server/.env`** — Fill in required environment variables
  - `DATABASE_URL` = `postgresql://localhost:5432/helpdesk?schema=public`
  - `BETTER_AUTH_SECRET` = `openssl rand -base64 32`
  - `BETTER_AUTH_URL` = `http://localhost:3000`
  - `TRUSTED_ORIGINS` = `http://localhost:5173`
  - `WEBHOOK_SECRET` = any random string
  - `OPENAI_API_KEY` = your OpenAI key (required for AI features)
  - `SENDGRID_API_KEY` = your SendGrid key (for outbound email)
  - `SENDGRID_FROM_EMAIL` = verified SendGrid sender address
  - `SEED_ADMIN_EMAIL` = your desired admin login email
  - `SEED_ADMIN_PASSWORD` = your desired admin password

- [ ] **Run database migrations** — Set up schema and tables
  - Run from `server/` directory: `bunx prisma migrate dev`

- [ ] **Seed the database** — Create admin user and AI agent user
  - Run from `server/` directory: `bunx prisma db seed`

- [ ] **Start backend server** — In one terminal
  - Run from `server/` directory: `bun run dev`
  - Server will run on http://localhost:3000

- [ ] **Start frontend server** — In another terminal
  - Run from `client/` directory: `bun run dev`
  - App will run on http://localhost:5173

- [ ] **Verify setup** — Log in and check admin panel loads
  - Navigate to http://localhost:5173
  - Log in with seeded admin credentials
  - Verify admin panel and ticket list load without errors
  - Verify server logs show pg-boss starting successfully

## Key Gotchas

- All `bunx prisma` commands must run from the `server/` directory (Prisma 7 uses `prisma.config.ts`)
- Sign-up is disabled by design — you can only add users via the admin panel after seeding
- pg-boss (the job queue) auto-provisions its own schema on first server start, no extra steps needed
- For local email ingestion testing you'd need ngrok to expose port 3000 to SendGrid's webhook
