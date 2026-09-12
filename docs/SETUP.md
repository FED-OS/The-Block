# Setup Guide

From zero to a live forum in about ten minutes.

## 1. Get the files

Clone the repo (or download the ZIP from GitHub):

```bash
git clone https://github.com/FED-OS/the-block-forum.git
cd the-block-forum
```

## 2. Try demo mode first (no setup)

Start a local server:

```bash
python3 -m http.server 8090
```

Open `http://localhost:8090`. Everything works — posting, replying, images,
votes, the No Cap feed — but it's all stored in **your browser only**
(localStorage). Nothing leaves your machine.

> Demo mode also works straight from `file://` in most browsers, but a local
> server is closer to how it'll really run.

## 3. Go live with Supabase

### 3a. Create a project

1. Go to [supabase.com](https://supabase.com) → **New project**.
2. Pick a name, a strong database password, and a region close to your users.
3. Wait a couple of minutes for it to provision.

### 3b. Run the setup SQL

1. In your project, open **SQL Editor** → **New query**.
2. Open `supabase-setup.sql` from this repo, copy **the whole thing**, paste,
   and **Run**.
3. That single paste creates every table, the 9 rooms, row-level security, the
   image storage bucket, the view counters, and the No Cap feed tables. It's
   safe to run again (it drops and recreates the rooms table but keeps your
   threads).

### 3c. Point the site at your project

In `index.html`, find `FORUM_CONFIG` (search for it — it's near the top of the
forum script):

```js
const FORUM_CONFIG = {
  mode: 'supabase',
  supabaseUrl: 'https://YOUR-PROJECT.supabase.co',
  supabaseAnonKey: 'sb_publishable_YOUR_KEY'  // publishable key only
};
```

- **Project URL** — Supabase dashboard → Project Settings → API.
- **Publishable key** — same page, starts with `sb_publishable_`. (Older
  projects show `eyJ…` anon keys — those work too.)
- **Never** put the `sb_secret_…` key in the HTML. That one is for servers.

### 3d. Turn on auth

1. Dashboard → **Authentication** → **Providers** → **Email** → enable it.
   Leave **Confirm email** off for the smoothest sign-ups, or on if you want
   verified addresses.
2. Dashboard → **Authentication** → **URL Configuration** → set the **Site
   URL** to where the site lives (e.g. `http://localhost:8090` while testing,
   your real URL when deployed) and add it to **Redirect URLs**.

## 4. Make an account and post

1. Reload the site. The banner under the forum header should be gone.
2. In the sign-in strip, type an email + password, hit **Sign up**.
3. Post a thread, attach a pic, reply, hit the No Cap feed. It's live.

## 5. Give yourself moderator powers (optional)

In the SQL editor:

```sql
insert into public.staff (user_id)
select id from auth.users where email = 'you@example.com';
```

Staff can delete any thread, reply, or No Cap post (RLS allows it), and get
the 🛡 moderator flair in the UI.

## 6. Deploy

See [DEPLOYMENT.md](DEPLOYMENT.md) for GitHub Pages, Netlify, Vercel, and
plain-static-hosting instructions.

## Troubleshooting

| Symptom | Fix |
|---|---|
| "Supabase is connected, but the forum tables aren't set up yet" banner | Run `supabase-setup.sql` in the SQL editor, then reload |
| Sign-in says "Invalid login credentials" | Make the account first with **Sign up** |
| Images fail to upload | Storage bucket missing — re-run the setup SQL |
| Nothing loads, console shows 401/403 | Wrong key — use the `sb_publishable_…` key, not the secret |
| View counters stuck at 0 in live mode | Re-run setup SQL (creates the counting RPCs) |
