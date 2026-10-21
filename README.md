# Neon Aether Studio Hub

A React + Vite shared production dashboard backed by Supabase Auth, Postgres, RLS, and Realtime.

## Features

- Email/password sign-up and sign-in
- Shared task creation, assignment, status and priority changes
- Search and status filters
- Team directory based on authorized workspace membership
- Milestones and activity log
- Live refresh when tasks, milestones, or activity change
- Row-level security is enforced by the database; the frontend uses only a publishable key

## 1. Configure local development

Requirements: Node.js 20+ and npm.

1. Copy `.env.example` to `.env.local`.
2. Fill in the Supabase Project URL, publishable key, and workspace ID.
3. Install and run:

   ```sh
   npm install
   npm run dev
   ```

Do not add a Supabase `service_role` key, secret key, or database password to a frontend `.env` file. `VITE_` variables are bundled into the browser, so only public/publishable credentials belong there.

## 2. Activate the workspace owner

The database schema and workspace were created in Supabase. For safety, creating an auth account does not automatically grant studio access.

1. Sign up in the app and confirm the email if Supabase requires it.
2. Open Supabase Dashboard → SQL Editor.
3. Open `supabase/bootstrap_owner.sql`, replace `YOUR_LOGIN_EMAIL` with the exact sign-in email, and run it.
4. Return to the app and click **Check access again** (or sign out and back in).

## 3. Add teammates

Each teammate signs up and confirms their email. Open `supabase/add_teammate.sql`, replace `TEAMMATE_LOGIN_EMAIL`, and run it in SQL Editor. Repeat for each teammate. The member role is used by default. The frontend intentionally does not let users self-join or invite arbitrary accounts.

## 4. Deploy to Vercel (recommended)

1. Put this folder in a GitHub repository (e.g. `neon-aether-studio-hub`).
2. In Vercel, import the repository.
3. Framework preset: **Vite**. Build command: `npm run build`. Output directory: `dist`.
4. Add these Environment Variables in Vercel Project Settings for Production (and Preview if needed):
   - `VITE_SUPABASE_URL`
   - `VITE_SUPABASE_PUBLISHABLE_KEY`
   - `VITE_WORKSPACE_ID`
5. Deploy. Any later code change pushed to GitHub triggers a new deployment.

### Supabase Auth URL configuration

In Supabase Dashboard → Authentication → URL Configuration, set **Site URL** to your deployed Vercel URL and add that URL to **Redirect URLs**. During local development, add `http://localhost:5173/**` as a redirect URL. If email confirmation is enabled, users must confirm their email before signing in.

## 5. GitHub Pages option

Vercel is simpler for this Vite app. GitHub Pages is also possible, but needs a repository-specific Vite `base` path and a GitHub Actions workflow. Vercel avoids that extra configuration and supports preview deployments.

## Data / security notes

- The frontend only uses a Supabase publishable key. Database permissions are enforced with Row Level Security.
- Members can only read/update workspace records if their account is in `workspace_members` for that workspace.
- This app currently supports a single configured workspace per deployment.
- Before public launch, review Supabase Security Advisor warnings and test with a separate non-owner account.
