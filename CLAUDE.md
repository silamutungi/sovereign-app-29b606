# Sovereign App — Claude Code Context

This app was built with Sovereign (sovereignapp.dev).
You own everything — this repo, the Vercel deployment, the Supabase database.

## Stack
- React + Vite + TypeScript
- Supabase (database + auth)
- Vercel (deployment — auto-deploys on push to main)
- Resend (email)

## Project structure
- src/ — React components and pages
- api/ — Vercel serverless functions
- public/ — static assets
- index.html — app entry point

## Rules for Claude Code

**Never break the build**
Run npm run build before pushing anything.
If the build fails, fix it before pushing.

**All Supabase access is server-side only**
Never query Supabase directly from browser JavaScript.
All database access goes through api/ routes.
The service role key is server-side only — never expose it in client code or VITE_ prefixed variables.

**Every Supabase table needs RLS**
When adding a new table, always:
1. ALTER TABLE table_name ENABLE ROW LEVEL SECURITY
2. Add explicit SELECT, INSERT, UPDATE policies
3. Never use USING(true) on private data tables

**Soft deletes only**
Never use DELETE FROM on user data.
All tables have deleted_at TIMESTAMPTZ DEFAULT NULL.
Delete = SET deleted_at = now() WHERE id = x
All queries filter WHERE deleted_at IS NULL

**Environment variables**
Never commit .env — it is in .gitignore
Never log env var values to the console
Never use VITE_ prefix for sensitive keys
See .env.example for all required variables

**Secure headers**
vercel.json contains CSP and security headers.
Do not remove or weaken these headers.
If adding new external resources (fonts, scripts), add their domains to the appropriate CSP directive.

**Never use url.parse()**
Use the WHATWG URL API instead:
  new URL(string, base)
  parsed.searchParams.get('param')
url.parse() has known security implications (DEP0169).

**Node version**
This project uses Node 20.x.
Do not upgrade to Node 24.x — it is not stable LTS.
Node version is set in package.json engines field.

**Vercel deployment**
This app auto-deploys when you push to main branch.
Test locally with npm run dev before pushing.
Check Vercel dashboard if deployment fails.

## Hard-Won Lessons

**package.json engines sets Node version**
Do not try to set Node version via Vercel project settings API — it rejects nodeVersion as an invalid field.
Use: { "engines": { "node": "20.x" } }
Learned: 2026-03-20

**All 6 scaffold files required for Vite build to succeed**
Vercel needs package.json, vite.config.js, index.html, .gitignore, README.md, and vercel.json.
Missing package.json causes immediate build failure.
Learned: 2026-03-20

## Adding new features

When adding a new feature, follow this checklist:
[ ] New API route added to api/ directory
[ ] Rate limiting added to the new route
[ ] Input validation on all inputs server-side
[ ] If new Supabase table: RLS enabled with policies
[ ] If new user data: soft delete column added
[ ] Error handled gracefully — no silent failures
[ ] Loading and error states in the UI
[ ] WCAG AA contrast on any new UI elements

## Your app's services

- GitHub repo: https://github.com/silamutungi/sovereign-app-29b606
- Vercel dashboard: https://vercel.com/dashboard
- Supabase dashboard: https://supabase.com/dashboard
- Built: 2026-03-20
- Built with: Sovereign (sovereignapp.dev)
