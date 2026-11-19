# DevCosts — Unified API Billing Dashboard

Aggregates billing and usage data from multiple developer APIs (OpenAI, Anthropic,
AWS, Vercel, Stripe, Supabase, Google Cloud, and others) into a single dashboard,
with budget alerts before limits are hit.

## Problem

Tracking AI and infrastructure spend means checking five to ten separate vendor
dashboards, each with its own format, refresh cadence, and terminology. One missed
invoice is a real cost. DevCosts puts the number in one place and warns you before
the bill arrives.

## Features

- **Pluggable provider adapters** — a common `BaseProvider` interface with a registry;
  adding a provider means implementing one interface, not rewriting the app
- **Idempotent daily upserts** — re-running a sync never double-counts usage
- **AES-256-GCM credential encryption** — API keys encrypted at rest before storage
- **Budget alerts** — configurable thresholds with scheduled checks
- **Spend forecasting** — trend projection against monthly limits
- **Stripe webhooks** — billing events reflected without polling

## Architecture

```
/app
  /(marketing)      landing page, pricing (public)
  /(auth)           login, signup
  /(dashboard)      protected: dashboard, connections, alerts, settings
  /api
    /connections    CRUD + test + sync per connection
    /usage          aggregated usage records
    /alerts         budget alert rules
    /cron/sync-all  scheduled provider sync
    /cron/check-alerts
    /webhooks/stripe
/lib
  /providers        provider registry, base interface, one module per API
  /encryption.ts    AES-256-GCM credential encryption
  /supabase         browser / server / service-role clients
/supabase
  /migrations       SQL migrations
  /seed.sql
```

## Tech stack

Next.js 14 (App Router) · TypeScript · React 18 · Tailwind CSS · shadcn/ui ·
PostgreSQL (Supabase) · Vercel · Stripe · Resend · Recharts

## Security notes

- Provider credentials are encrypted with AES-256-GCM before being persisted
- Supabase row-level security separates users from their own connections and usage
- The service-role client is used only by server-side cron routes
- `.env.example` documents every required variable; no real secrets are committed

## Local setup

```bash
npm install
cp .env.example .env.local   # fill in your own keys
npm run dev
```

See `CLAUDE.md` for the full architecture reference and development commands.

---
*Repository was previously named `universal-api-viewer`; renamed to match the product.*
