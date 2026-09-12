# Auth Guide

How signing in works on The Block.

## The short version

Sign-in is **email + password** through Supabase, with the **PKCE flow** (the
modern, safe OAuth-style flow that doesn't put secrets in the browser). There
are two places to sign in:

1. **The forum itself** (`index.html`) — a compact inline form in the forum
   header. Type email + password → **Sign in**. New here? **Sign up** makes
   the account right there.
2. **The standalone login page** (`login.html`) — a full-page card with sign
   in, sign up, and GitHub / Google OAuth buttons.

Both use the same Supabase project, so an account made on one works on the
other.

## On the forum page

The sign-in strip lives in the forum section header. When you're signed out
you see two inputs (email, password) and two buttons:

- **Sign in** — for existing accounts
- **Sign up** — makes a new account (6+ character password)

While you type, pressing **Enter** signs you in. After signing in, the strip
shows `Posting as you@email.com` with a **sign out** button. Deleting your own
posts only works while signed in (that's how the server knows they're yours).

## On the standalone login page

`login.html` is a self-contained page — same "1 file, 0 build steps" idea.
It has:

- Email + password **sign in**
- **Sign up** mode (toggle at the bottom)
- **GitHub** and **Google** OAuth buttons (only work once you enable those
  providers in the Supabase dashboard — Authentication → Providers)
- A signed-in view with your initial in a green circle and a **Sign out**
  button

The page connects with the publishable key only, uses the PKCE flow, keeps the
session in the browser, and auto-refreshes tokens.

## Password rules

Supabase requires **6+ characters** by default. Both forms check this before
sending anything, so you get an instant message instead of a server round
trip.

## Resetting a password

The forms don't ship a "forgot password" flow (keeps things lean). Two
options:

1. A user can ask an admin to send a reset from the Supabase dashboard
   (Authentication → Users → ⋯ → Send password recovery).
2. Add it yourself later: `supabase.auth.resetPasswordForEmail(email)` and a
   small form — a natural next step when the community grows.

## Deleting accounts

Users can't self-delete from the UI (that's deliberate — it would orphan
their threads). An admin can remove a user from Authentication → Users in the
dashboard. Their threads and replies stay up unless a moderator deletes them.

## The OAuth consent screen

If you enable the **Supabase OAuth Server** (Authentication → OAuth Server),
your project can act as an identity provider for third-party apps — they send
users to your authorization path to log in with their Block account.

- `oauth/consent/index.html` is that screen. When a third-party app starts a
  login, Supabase redirects the user to it with an `authorization_id` in the
  URL.
- The page checks the user is signed in (bounces to `login.html` if not),
  loads the request details, shows the app's name + requested permissions,
  and offers **Approve / Deny**.
- Approving sends the decision back to Supabase, which redirects the user to
  the third-party app with an authorization code. You don't handle tokens —
  Supabase does.

Dashboard settings for it: **Site URL** and **Authorization Path** must match
where you host these files (e.g. Site URL `https://your-site.com` and
Authorization Path `/oauth/consent`, with the file at
`your-site.com/oauth/consent/index.html`).

## Security model

- The browser only ever holds the **publishable** key. Every write is checked
  against row-level security on the server (authors write their own stuff,
  staff moderate, everyone reads).
- Sessions are stored in the browser and refreshed automatically. Signing out
  wipes them.
- The **secret** key (`sb_secret_…`) is never used in any frontend file. If
  you ever paste it into HTML by accident, rotate it in the dashboard
  immediately: Project Settings → API Keys.
