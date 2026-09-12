# FAQ

Common questions about The Block, straight answers, no runaround.

## General

### What is The Block?

The Block is a forum and landing page built for Black people in AI, coding, and tech. Nine themed rooms, a No Cap anonymous feed, a BLM agency tab, image posting, votes, view counters, and full moderation tooling — all in one HTML file for the forum itself, backed by Supabase when you connect it.

### Why did the top nav go away?

It was removed in v0.4.0. The strip said "15 ROOMS 15 TABS" when the forum has nine rooms, and its links didn't all earn their slot. Navigation now runs through the room tabs in the forum, the footer links, and the in-page CTAs — every one of those goes somewhere real.

### Why is the whole forum one HTML file?

On purpose. One file means zero build steps, zero dependencies to install, and zero "works on my machine." You can open `index.html` from a USB stick, a GitHub Pages URL, or any web server and it just works. All CSS and JavaScript are inline. The only external things are the Supabase CDN library (when live mode is on) and image uploads to Supabase storage.

### Does it work without Supabase?

Yes. The Block ships in **demo mode** — posts are stored in your browser's `localStorage`, sign-in is simulated, and every feature (rooms, posting, images, votes, No Cap, moderation) works locally so you can evaluate everything before connecting a backend. Flip one config value and it goes live against Supabase. See [SETUP.md](SETUP.md).

### What are the nine rooms?

Landing / Culture, Music + Audio, Art + Design, Code + Engineering, Business + Money, AI / ML, Web3, Games, and the BLM Agency tab. Each has its own category, threads, replies, and view counter in live mode.

### What's "No Cap"?

An anonymous feed — short takes with no names attached, no threads, no replies. Just posts and likes. The database keeps the author id for moderation purposes, but the UI never shows it.

## Accounts & login

### How do I sign in?

Enter your email and a password in the strip at the top of the forum and hit **Sign in**. That's it — no magic links, no email round-trips. New here? Hit **Sign up** with the same form.

### What are the password rules?

At least 6 characters, same as Supabase's default minimum. If your project tightens password requirements in the Supabase dashboard, the client-side check (6 characters) is a floor, not the real rule — the server always has the final say.

### Do I have to confirm my email before posting?

Depends on your Supabase project's "Confirm email" setting. If email confirmation is on, signing up shows "check your email to confirm" and you can't post until the email link is clicked. If confirmation is off (common on fresh projects), signup signs you straight in.

### Can I use GitHub or Google to sign in?

The standalone [login page](../login.html) has **Sign in with GitHub** and **Sign in with Google** buttons. They only work if you've enabled those providers in your Supabase dashboard under Authentication → Providers, and added your site URL to the redirect allowlist. The forum itself uses the email + password strip.

### What is `login.html` for then?

It's a standalone, embeddable login screen — same Supabase project, same accounts, plain HTML, no framework. Use it as a dedicated sign-in page, embed it in an iframe, or point users at it when you want a branded login flow separate from the forum.

### What is the OAuth consent page (`oauth/consent/`)?

That's for Supabase's OAuth **Server** feature, where your Supabase project acts as the identity provider and other apps sign users in *through* it. When another app starts that flow, Supabase sends the user to your consent URL with an `authorization_id` in the query string; the page shows the app name and the permissions requested, and Approve/Deny finishes the flow. You configure that URL in the Supabase dashboard's OAuth Server settings (Authorization Path).

### Why did my session log me out?

Supabase sessions expire (default 1 hour for the access token, with automatic refresh while the tab is open). If the tab was closed a long time or refresh failed (offline, project paused), you'll need to sign in again. Your posts stay; they're not tied to your open tab.

### How do I delete my account?

Ask a moderator — staff can remove accounts through the Supabase dashboard (Authentication → Users). Your posts carry your handle; deleting the account doesn't automatically delete posts, which is why moderator involvement is the safe path. See [AUTH.md](AUTH.md).

## Posting

### How do I post a picture?

In the reply box (or thread composer) use the image button, pick a file, and it uploads to the `forum-images` bucket on Supabase and renders inline. In demo mode the image is stored in your browser as a data URL. There's a size limit enforced client-side — keep files a few MB or under.

### Can I edit a post?

Not yet — the honest answer. You can delete and repost, and deletion is fully built (your own posts, anytime; moderators can remove anyone's). Editing is on the roadmap in [CHANGELOG.md](../CHANGELOG.md).

### Who can delete posts?

You can delete **your own** posts, threads, and replies at any time (confirm dialog, then it's gone from Supabase too — replies, images, votes and all). Moderators (users listed in the `staff` table) see delete buttons on **everything**, plus a Reports room with flagged content. That's the whole moderation model — deliberately simple.

### What's the vote system?

Upvotes on threads and replies — one per account per item, toggled by clicking again. Totals are public; who voted isn't.

### How do view counters work?

Every thread has one, and the site has a running total, computed in the database with RPC functions (`bump_thread_views`, `bump_site_views`) so it can't be spammed from the client. The hero stat on the landing page is the live site total in live mode.

## Self-hosting & deployment

### Where can I host it?

Anywhere that serves files: GitHub Pages, Netlify, Vercel, Cloudflare Pages, a $4 VPS, nginx, an S3 bucket. It's static. Full walkthrough in [DEPLOYMENT.md](DEPLOYMENT.md).

### Does the GitHub Pages subfolder break anything?

No — everything internal uses relative paths, so it works at `username.github.io/the-block-forum/` without config. The one step people forget: add your Pages URL to the Supabase auth redirect allowlist, or sign-ins will bounce.

### How much does it cost to run?

The static hosting is free or near-free everywhere listed above. Supabase's free tier covers a small community comfortably (500 MB database, 1 GB file storage, 50 MB bandwidth on egress for images — watch that one if images get popular). Realtime and auth are included in the free tier. Details in [SUPABASE.md](SUPABASE.md).

### Can I change the colors/branding?

Yes — everything is inline in `index.html`. The color palette lives in the `:root` CSS custom properties block near the top of the styles (`--paper`, `--green`, `--acid`, etc.). Change those and the whole site follows. Fonts are loaded from Google Fonts CDN; swap the `<link>` tags.

### Can I add more rooms?

Yes — add a row in `categories` (or the `CATEGORIES` array in demo mode), add the room tab to the room strip markup, and it's live. See the rooms table in [SETUP.md](SETUP.md).

## Security

### Where are the keys?

The publishable key (`sb_publishable_...`) is in the HTML — that's fine, it's designed to be public, and all data access is protected by Row Level Security. The secret key (`sb_secret_...`) is **never** in any HTML file; it stays in your Supabase dashboard and server-side tooling only. If a secret key ever lands in frontend code, rotate it in the dashboard immediately.

### Can someone hack the demo localStorage mode?

Demo mode is per-browser, offline, and fake — there's nothing to hack. It's a sandbox to evaluate the product.

### What does RLS actually protect?

Every table has Row Level Security policies: anyone can read, only signed-in users can write, only owners can edit/delete their own rows, and only staff can act on reports. Even with the public key, an anonymous script can't insert posts or read other people's data it shouldn't. Policies are in [supabase-setup.sql](../supabase-setup.sql), explained in [SUPABASE.md](SUPABASE.md).

## Contributing & support

### How do I report a bug or request a feature?

Open a GitHub Issue. If you can reproduce it, include steps, browser, and whether you were in demo or live mode. Feature requests are welcome — check [CHANGELOG.md](../CHANGELOG.md) first so you're not requesting something that shipped.

### Can I contribute code?

Yes — read [CONTRIBUTING.md](../CONTRIBUTING.md) first. The short version: keep it one file, no build steps, no frameworks, and test both demo and live modes before submitting.

---

*Still stuck? [SETUP.md](SETUP.md) has the troubleshooting table, and [TESTING.md](TESTING.md) shows how to verify every feature yourself.*
