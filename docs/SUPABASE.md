# Supabase Guide

The backend, explained.

## Why Supabase

The Block needs real shared data (threads, replies, images, votes, counts) but
the whole site is one static HTML file. Supabase gives you a hosted Postgres
database, auth, file storage, and realtime — all reachable from plain browser
JavaScript with the publishable key. No server of your own to run.

## What `supabase-setup.sql` creates

Run it once in the SQL editor. It's idempotent for the important parts (safe
to re-run; it won't eat your threads).

### Tables

| Table | Holds |
|---|---|
| `profiles` | Display data per user (handle, flair, staff flag) |
| `categories` | The 9 rooms (slug, name, emoji, color, description) |
| `threads` | Forum posts — title, body, room, images, view count |
| `replies` | Thread replies |
| `votes` | One helpful-vote per user per thread |
| `nocap_posts` | No Cap feed posts (280 chars + one image) |
| `nocap_likes` | Likes on No Cap posts |
| `reports` | Flags from users for moderators |
| `staff` | User IDs with moderator powers |

### Security (row-level security)

Every table gets RLS policies. The pattern:

- **Read** — anyone (the whole internet can browse the forum).
- **Insert** — signed-in users, stamping their own `author_id`.
- **Update / delete own** — the author only.
- **Delete any** — staff (`public.is_staff()` checks the staff table).
- **Votes** — one per user per thread (unique index + policy).

### Storage

- `forum-images` bucket — **public reads**, **member-only writes** (a signed-in
  user can upload; nobody can overwrite someone else's file — path includes
  their user ID).

### RPCs (safe functions)

- `bump_thread_views(thread_id)` — +1 view on a thread, once per browser
  session (the site guards it client-side too).
- `bump_site_views()` — +1 on the all-time counter, anonymous-safe.
- `get_site_views()` — read the total.

### Realtime

The setup publishes changes on `threads`, `replies`, and `nocap_posts`. The
site subscribes, so new posts and replies pop in for everyone without a
refresh.

## Wiring it to the site

`FORUM_CONFIG` near the top of the forum script in `index.html`:

```js
const FORUM_CONFIG = {
  mode: 'supabase',            // or 'demo' for browser-only
  supabaseUrl: 'https://YOUR-PROJECT.supabase.co',
  supabaseAnonKey: 'sb_publishable_YOUR_KEY'
};
```

If `mode` is `demo` (or the URL/key are missing), the site silently falls
back to localStorage — threads, replies, images, votes, No Cap posts, the
works. That's why the same file works offline and live.

## The two keys

| Key | Looks like | Where it belongs |
|---|---|---|
| Publishable (anon) | `sb_publishable_…` (or `eyJ…` on older projects) | Frontend HTML/JS — it's in `index.html` and `login.html` on purpose |
| Secret (service) | `sb_secret_…` | **Never** in the frontend. Server-side scripts only. |

The publishable key is safe to expose because RLS does the real enforcing.
Any jerk with your publishable key can read public data and try to write —
but the policies block everything they shouldn't do.

If a secret key ever leaks (pasted in HTML, committed to a repo, shown in a
screenshot), **rotate it** in Project Settings → API Keys right away. Rotation
is instant and doesn't break the publishable key.

## Moderation

Make a user a moderator:

```sql
insert into public.staff (user_id)
select id from auth.users where email = 'them@example.com';
```

They can now delete any thread/reply/No Cap post from the UI, and their
posts show a 🛡 flair. Remove them by deleting the row.

For heavier moderation, the `reports` table exists — users flag a post, staff
see it in the dashboard (Table Editor → reports) and act. The UI hook for
report buttons isn't wired yet; it's a natural next step.

## Costs

Supabase's free tier is generous for a growing community: 500MB database,
1GB file storage, 50K monthly active users, realtime included. The Block fits
comfortably. When you outgrow it, the Pro tier is flat per month and nothing
in this repo changes.

## Backups

Supabase takes automatic backups on paid plans; on free, export your data
from the dashboard (Database → Backups, or Table Editor → export CSV) now and
then. Threads are the community — don't lose them.
