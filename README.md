# The Block — Forum Setup

The forum lives inside `index.html`. It works two ways — pick your mode.

## Mode 1 — Demo mode (zero setup, default)

Works out of the box on GitHub Pages, Netlify drop, or even opening the file directly.
Threads, replies, votes, handle, and **attached images** are stored in each visitor's
browser (localStorage). Perfect while you're building the community. Images are resized
and compressed in the browser (max 3 per post, ~260KB each) so they actually fit.

## Mode 2 — Supabase mode (live for everybody + email login)

1. Create a free project at supabase.com
2. Open **SQL Editor** → paste the whole `supabase-setup.sql` file → Run. That creates:
   - tables (profiles, categories, threads, replies, votes, reports)
   - the 9 rooms with matching slugs
   - row-level security rules (public read, author/staff write)
   - the `forum-images` storage bucket with public reads + member uploads
   - realtime updates (new posts pop in without refresh)
3. In `index.html`:
   - uncomment the Supabase script tag near the top (`@supabase/supabase-js@2`)
   - find `FORUM_CONFIG` and set `mode: 'supabase'`, plus your project URL + anon key
4. In Supabase dashboard: **Authentication → Providers → Email**: enable it. Magic-link
   sign-in is what the forum uses — users type their email on the page, get a link, done.

That's the whole setup. In live mode the sign-in box turns into a real email + magic
link form with a sign-out button once you're in.

## The rooms

| Slug | Room | What goes there |
|---|---|---|
| `front-door` | 🚪 The Front Door | House rules, how to move |
| `cookout` | 🔥 The Cookout | Intros — pull up, say who you be |
| `wave` | 🌊 The Wave | AI & ML — LLMs, agents, RAG |
| `cypher` | 🎤 The Cypher | Coding help — bring your code |
| `kitchen` | 🍳 The Kitchen | Projects & showcase — where we cook |
| `bag` | 💰 The Bag | Jobs, referrals, getting paid |
| `plug` | 🔌 The Plug | Tools, courses, links worth sharing |
| `pull-up` | 📍 Pull Up | Events, meetups, study hall |
| `barbershop` | 💈 The Barbershop | Off-topic — life, music, fitness, money |

## Black Leaders Management (the agency section)

`index.html` also ships a BLM agency section (`#blm`): a coding / AI / tech development
& management agency. Three talent lanes (Full Stack Dev, AI Engineer, Tech PM), a
4-step process (Pull up → Vetting → Level up → Get placed), and a contact CTA at
`blm@fedpromptly.com`. Swap the email or wire it to a form whenever you're ready.

## Files

- `index.html` — the whole site, forum + images + BLM agency included
- `supabase-setup.sql` — one-paste backend setup (tables, rooms, security, storage)
- `assets/css/forum.css` — standalone copy of the forum styles (reference only; the
  live styles are inlined in index.html so it stays a single file)
