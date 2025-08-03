# Tiered Events Showcase

Demo app demonstrating tier-based access to events using:
- **Frontend:** Next.js 14 (App Router) + Tailwind CSS  
- **Auth:** Clerk.dev (single-session mode)  
- **Database:** Supabase (PostgreSQL)  
- **Tier Logic:** Users have tiers (`free`, `silver`, `gold`, `platinum`). Events above a user’s tier are visible but locked.

## Features

- Clerk authentication with single-session semantics  
- Catch-all sign-in route that stays on `/sign-in` when already logged in (shows sign-out)  
- Tier stored in Clerk `publicMetadata` and optionally persisted into Supabase on **Refresh**  
- Dropdown to simulate/upgrade tier:
  - Defaults to authoritative tier from DB (fallbacks to Clerk tier if not persisted)  
  - Current tier option is disabled  
  - Changing tier updates Clerk metadata immediately and reflects in UI (without persisting)  
- Refresh button persists current Clerk tier into Supabase (`sync`)  
- All events are shown; higher-tier events are greyed out/locked with tooltip explaining upgrade requirement  
- Root `/` redirects to `/sign-in`

## Prerequisites

- Node.js 18+  
- npm, pnpm, or yarn  
- Supabase account & project  
- Clerk account & application  

## Setup

### 1. Clone and install

```bash
git clone <repo-url>
cd tiered-events
npm install
```

### 2. Supabase

1. Create a Supabase project.  
2. In SQL Editor, run `supabase/schema.sql` (or paste the following manually) to:
   - Create `tier` enum  
   - Create `events` table (seeded with sample events)  
   - Create/alter `user_tiers` table (with `clerk_id` as `text`)  
   - Enable RLS (optional; currently filtering is done in app logic)
    
3. Copy your Supabase project values:
   - `Project URL` → `NEXT_PUBLIC_SUPABASE_URL`  
   - `anon/public` key → `NEXT_PUBLIC_SUPABASE_ANON_KEY`  
   - `Service Role Key` → `SUPABASE_SERVICE_ROLE_KEY` (keep secret, used only server-side)

### 3. Clerk

1. Create a Clerk application.  
2. Configure redirect URLs to include:
   - `http://localhost:3000/*`  
   - If deployed, your production domain (e.g., `https://yourapp.vercel.app/*`)  
3. In Clerk settings, ensure **single-session** mode is enabled (default) so signing in with an active session auto-redirects.  
4. Get your keys:
   - Publishable/frontend API → `NEXT_PUBLIC_CLERK_FRONTEND_API`  
   - Secret key → `CLERK_SECRET_KEY`

   
### 4. Run Locally

```bash
npm run dev
```

Visit: `http://localhost:3000` → auto-redirects to `/sign-in`.

### 5. Deployment

Deploy to Vercel or similar:
- Set the same env vars in the dashboard.  
- Update Clerk redirect URLs to include your production domain.  
- Root path redirects to `/sign-in` automatically via `app/page.tsx`.

## Enhancements

- Add a modal or payment simulation to upgrade tiers formally.  
- Cache event data client-side with SWR for smoother UX.  
- Implement audit log of tier changes in Supabase.  
- Add tests (Vitest / Jest) for API route behavior.