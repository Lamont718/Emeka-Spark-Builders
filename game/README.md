# Emeka & Coco: Starlight Trail 🌟🐕

A self-contained early-learning game built for Emeka (and co-designed by her). Open **`index.html`** in any browser to play — no install, no internet required for the core game.

## How to play
- Fly **Coco** (the glowing comet) in 2D — drag your finger, move the mouse, or use the **arrow keys / WASD**.
- Touch the **letters/numbers in order** to weave words into glowing constellations.
- **Dodge** the falling shadow-drops (you have 3 hearts).
- Modes: the **Trail** (12 levels), **Clue Quest** (read a riddle, spell the answer), **Starlight Duel** (befriend the Gloom boss), and free-roam **Stargazing**.
- Words scale up to **3rd-grade** level (MOON → STAR → PLANET → JOURNEY → ADVENTURE).

## Online leaderboard (in progress)
The game has a built-in **global "Star Board"** leaderboard that activates once a Supabase backend is connected. It currently shows "coming soon" until the keys are wired in.

**To turn it on:** create a Supabase project + a `scores` table, then fill in the config block near the top of the `<script>` in `index.html`:

```js
var LEADERBOARD = {
  url: 'https://YOUR-PROJECT.supabase.co',
  key: 'YOUR-ANON-PUBLIC-KEY'
};
```

`scores` table SQL (public read + public insert):

```sql
create table if not exists public.scores (
  id bigint generated always as identity primary key,
  name text not null check (char_length(name) between 1 and 16),
  score integer not null check (score >= 0 and score <= 1000000),
  created_at timestamptz not null default now()
);
alter table public.scores enable row level security;
create policy "public read scores" on public.scores for select using (true);
create policy "public add scores" on public.scores for insert with check (
  char_length(name) between 1 and 16 and score >= 0 and score <= 1000000
);
create index if not exists scores_score_idx on public.scores (score desc);
```

Then deploy (e.g. Vercel) and share the link.

## Tech notes
- One self-contained HTML file: vanilla JS + Canvas + Web Audio + SpeechSynthesis. No libraries, no build step.
- The two character images (Coco, Emeka) are embedded as base64. Progress saves to `localStorage` (`emekaStarlight.v1`).
