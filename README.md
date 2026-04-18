# Soccer Team Score Dashboard

This project is a static soccer team score dashboard built to work with Supabase as the shared persistent store. The application includes:

- Team dashboard with standings and aggregated team score
- Player dashboard with individual scores, goals, assists, and team assignment
- Admin section for creating/editing teams and players
- Score event logging and history view
- Reset functionality to clear scores while keeping history

## Setup

1. Create a Supabase project at https://app.supabase.com.
2. Open the SQL editor in Supabase and run the following schema:

```sql
create table teams (
  id uuid primary key default gen_random_uuid(),
  name text not null,
  color text default '#0055ff',
  created_at timestamptz default now()
);

create table players (
  id uuid primary key default gen_random_uuid(),
  team_id uuid references teams(id) on delete set null,
  name text not null,
  position text,
  score int default 0,
  goals int default 0,
  assists int default 0,
  created_at timestamptz default now()
);

create table score_events (
  id uuid primary key default gen_random_uuid(),
  player_id uuid references players(id) on delete cascade,
  team_id uuid references teams(id) on delete set null,
  action text not null,
  points int default 0,
  note text,
  created_at timestamptz default now()
);
```

3. In Supabase, under Settings → API, copy your `anon` public key and project URL.
4. Open `index.html` and replace `SUPABASE_URL` and `SUPABASE_ANON_KEY` with your Supabase values.

## Deploy to Vercel

1. Push this repository to GitHub.
2. Create a Vercel account at https://vercel.com and connect your GitHub repository.
3. Use the default settings and deploy the project as a static site from the repository root.
4. Vercel will use `vercel.json` to deploy `index.html` as a static site and apply production security headers.
5. After deploy, the app will load in the browser and persist data through Supabase.

## Security and repo readiness

- `vercel.json` is included to enable static deployment and attach secure response headers.
- `.gitignore` excludes local environment files, editor settings, and build artifacts.
- For stronger backend security, enable Supabase Row Level Security and create policies for the public anon key.

## Development

Since this is a static HTML app, you can preview locally by opening `index.html` in a browser or using a local static server.

## Notes

- The dashboard is built as a static site so it can be hosted on Vercel for free.
- Supabase stores all team, player, and history data so every device sees the same live league information.
- If you want stronger security, enable Row Level Security in Supabase and configure policies for the `anon` key.
