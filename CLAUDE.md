# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Everafter is a photography/videography business site (public portfolio, promotional campaign landing pages, blog, visitor chat, booking/payments) with admin and user dashboards. It is a **Lovable project** (lovable.dev) — changes made in Lovable are auto-committed to this repo, and some files are Lovable-generated (see below).

## Commands

```sh
npm run dev        # Vite dev server on port 8080
npm run build      # production build (does NOT typecheck)
npm run build:dev  # development-mode build
npm run lint       # ESLint
npx tsc -p tsconfig.app.json --noEmit   # typecheck (not part of build)
```

- There is **no test framework** configured; don't look for or try to run tests.
- TypeScript is non-strict (`strict: false`, `noImplicitAny: false`); `@typescript-eslint/no-unused-vars` is disabled.
- Path alias: `@/` → `src/`.

## Architecture

**Stack**: Vite + React 18 + TypeScript, Tailwind CSS + shadcn/ui (`src/components/ui/`), React Router v6, TanStack Query, Supabase (Postgres + Auth + Storage + Realtime + Edge Functions).

### Routing (`src/App.tsx`)

`Index` (homepage) is eager-loaded for LCP; all other routes are lazy. Add new routes **above** the catch-all `*` route. Two nested-route dashboards:

- `/dashboard/*` → `src/pages/AdminDashboard.tsx` — requires the `admin` role; non-admins are redirected to `/`
- `/user-dashboard/*` → `src/pages/UserDashboard.tsx` — authenticated users

Roles live in `profiles.role` and are checked client-side via `useRole` (`src/hooks/useRole.ts`); real enforcement is Supabase RLS.

### Supabase

- `src/integrations/supabase/client.ts` — **auto-generated, do not edit** (URL/anon key are intentionally hardcoded by Lovable). Import as `import { supabase } from "@/integrations/supabase/client"`.
- `src/integrations/supabase/types.ts` — generated DB types (the source of truth for the schema shape); regenerated from the database, do not hand-edit.
- `supabase/migrations/` — 100+ timestamped SQL migrations (Lovable-generated). Schema changes go here as new migration files.
- `supabase/functions/` — Deno edge functions: visitor chat (`visitor-chat`, `chat-response`, `chat-webhook-callback`), n8n webhook proxies (`webhook-proxy`, `consultation-webhook-proxy`), Stripe (`create-booking-checkout`, `stripe-webhook`, `manual-payment`). All have `verify_jwt = false` in `supabase/config.toml` and handle their own auth/CORS.

### Data-access pattern

Components do not call Supabase directly — each data domain has a custom hook in `src/hooks/` (e.g. `useBlogPosts`, `usePromotionalCampaign`, `useLeads`) wrapping the query/mutation, usually with TanStack Query. Admin-side variants are separate hooks suffixed `Admin` (e.g. `useBlogPostsAdmin`, `useSiteSettingsAdmin`). Follow this pattern for new features.

### Dynamic theming — do not hardcode brand colors

Brand colors are stored in the database (site settings), loaded at app start by `useSiteSettings`, and applied as CSS variables (`--brand-*`). Use the Tailwind tokens they feed: `brand-primary-from`, `brand-text-accent`, `bg-brand-gradient`, etc. (see `tailwind.config.ts`). Hardcoding e.g. `rose-500` in components bypasses the admin's theme editor.

### Chat system

Visitor chat runs through the `visitor-chat` edge function (visitors are identified by a `visitor_id`, not auth). Messages can carry interactive cards (`src/components/chat/`, e.g. `PhoneCaptureCard`); per-card submitted state must live in that message's `messages.metadata` JSONB, not in global/localStorage state.

## Other notes

- `KNOWLEDGE_BASE.md` has useful background on the schema and design conventions, but is partly aspirational/stale (it describes testing, Husky/Prettier, and CSP setups that don't exist) — trust the code over it.
- Payments: Stripe Checkout via edge functions, with a `stripe-webhook` handler and shared logic in `supabase/functions/_shared/processBookingPayment.ts`.
- Affiliate/referral tracking initializes on app load (`src/utils/affiliateTracking.ts`); promotional campaigns get portal pages via `CampaignPortalContext`.