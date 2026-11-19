# SJCIT Tech Quiz Arena — Complete Netlify Project

This folder contains the complete website project for the SJCIT team-wise technical quiz.

## Features
- Team registration (team name and member names)
- Round 1: 15 multiple-choice questions with automatic scoring
- Round 2: four debugging challenges (D1–D4)
- Round 3: four programming challenges (P1–P4)
- Monaco live code editor with C, Python and Java
- OnlineCompiler.io API integration through a Netlify Function
- Optional Supabase shared leaderboard
- Responsive layout for desktop and mobile

## Project files
- `index.html` — website page
- `style.css` — layout and styling
- `app.js` — quiz interface, rounds and scoring
- `netlify.toml` — Netlify publish and function configuration
- `netlify/functions/execute.js` — secure code-execution API proxy and hidden tests
- `netlify/functions/scores.js` — optional Supabase leaderboard API
- `.env.example` — environment variable names (no secret keys)

## 1. Deploy the website free on Netlify
1. Extract this project folder.
2. Upload the project to a GitHub repository (the files inside this folder should be at the repository root).
3. Sign in to Netlify and choose **Add new site → Import an existing project**.
4. Connect the GitHub repository.
5. Set the build command to blank and publish directory to `.`.
6. Deploy the site.

Netlify hosts the frontend and runs the included serverless functions. It does not itself compile C, Python or Java.

## 2. Configure code execution
1. Create an account with OnlineCompiler.io and obtain its REST API key from its dashboard.
2. In Netlify open **Site configuration → Environment variables**.
3. Add `ONLINECOMPILER_API_KEY` and paste your private REST API key as its value.
4. Redeploy the site.

The key is deliberately not included in the project files. Do not put it in `app.js`, `index.html`, or any other browser-visible file. The backend function reads it from the Netlify environment.

The code-execution integration uses `https://api.onlinecompiler.io/api/run-tests/` and compiler IDs `gcc-15` (C), `python-3.14` (Python), and `openjdk-25` (Java). Check the provider dashboard for current free-tier quotas, concurrency limits and supported compiler versions before the event. Free-tier limits can change.

## 3. Optional shared leaderboard with Supabase
Without Supabase, the quiz can still run, but shared scores across devices will not be available.

1. Create a free Supabase project.
2. In its SQL Editor run:

```sql
create table if not exists public.quiz_scores (
  team_id text primary key,
  team_name text not null,
  members text,
  score integer not null default 0,
  updated_at timestamptz not null default now()
);

alter table public.quiz_scores enable row level security;
```

3. In Netlify environment variables add:
   - `SUPABASE_URL` — your Supabase project URL
   - `SUPABASE_SERVICE_ROLE_KEY` — your server-side service role key
4. Redeploy.

Keep the Supabase service role key private. Never add it to frontend JavaScript or HTML.

## 4. Scoring
- Round 1 MCQ: 1 point each (15 maximum)
- Round 2 debugging: 10 points per challenge (40 maximum)
- Round 3 programming: 10 points per challenge (40 maximum)
- Total: 95 points

## 5. Test before the competition
- Deploy a private test site first.
- Add the OnlineCompiler.io API key.
- Add Supabase variables if using the shared leaderboard.
- Test one C, one Python and one Java submission.
- Test every challenge and its hidden cases.
- Check mobile layout, refresh behaviour, score syncing and leaderboard.
- Test with multiple teams at the same time and confirm API quotas are sufficient.

## Important limitations
- Team identity is stored in the browser; teams should use the same device and browser to resume.
- The displayed timer is client-side and is not a server-enforced deadline.
- This starter project does not include organiser-controlled start/stop, team login, proctoring or comprehensive anti-cheat protection.
- MCQ grading logic is in the frontend. For a high-stakes event, move MCQ grading to a server function and add server-side authentication and timing controls.
