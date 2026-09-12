# Deployment Guide

The Block is a static site — one HTML file (plus a login page, a consent
screen, icons, and docs). Any static host works. Pick your lane.

## GitHub Pages (the main lane)

1. Push this repo to GitHub (say `FED-OS/the-block-forum`).
2. Repo → **Settings** → **Pages**.
3. Source: **Deploy from a branch** → Branch: `main` → folder: `/ (root)` →
   **Save**.
4. Wait ~1 minute. Your site is at
   `https://fed-os.github.io/the-block-forum/`.

### One thing to change for GitHub Pages

Add your Pages URL to the Supabase allowlist or sign-in links bounce:
Dashboard → **Authentication** → **URL Configuration** → add
`https://fed-os.github.io/the-block-forum/**` to **Redirect URLs** (and set it
as the **Site URL** if that's the permanent home).

### Project pages live in a subfolder

`https://fed-os.github.io/the-block-forum/` — the site uses relative paths
(`login.html`, `oauth/consent/index.html`, favicons), so everything works in
a subfolder with zero changes.

## Netlify drop (fastest)

1. Go to [app.netlify.com/drop](https://app.netlify.com/drop).
2. Drag the whole `the-block-forum` folder onto the page.
3. Done — you get a `*.netlify.app` URL instantly.

Then add that URL to Supabase's Redirect URLs (same as above).

## Vercel

```bash
npm i -g vercel
cd the-block-forum
vercel          # answer the prompts, no framework detected = static
```

Add the produced URL to Supabase's Redirect URLs.

## Cloudflare Pages

Dashboard → Workers & Pages → Create → Pages → Connect to Git → pick the repo
→ framework preset: **None** → Save. Output is the repo root.

## Any web server (nginx, Apache, S3, a Raspberry Pi…)

Copy the folder contents to your document root. Make sure the server serves
`index.html` for `/` and keeps folder structure for
`oauth/consent/index.html`. No build step, no Node, no PHP.

## Hosting checklist

| Check | Why |
|---|---|
| `index.html` at the root | The site |
| `login.html` next to it | Standalone login |
| `oauth/consent/index.html` in place | OAuth consent (if you use the OAuth Server) |
| Favicon files present | Tab + home-screen icons |
| Supabase **Site URL** set to your real URL | Magic-link/OAuth redirects land correctly |
| Supabase **Redirect URLs** includes your origin | Sign-in round-trips work |
| Email provider enabled | Email + password auth |
| `supabase-setup.sql` has been run | Tables exist |

## Custom domain

On GitHub Pages: Settings → Pages → Custom domain → enter it → add a `CNAME`
file (Pages makes one). On Netlify/Vercel/Cloudflare: add the domain in their
DNS UI. Then update the Supabase Site URL + Redirect URLs to the new domain.

## Testing your deploy

1. Open the site — the forum should load with no setup banner (live mode).
2. Sign in with a real account (or make one with **Sign up**).
3. Post a thread with an image. Refresh — it should still be there.
4. Open the site in a private window — your thread should be visible
   (public reads work).
5. Check the view counter moved.
