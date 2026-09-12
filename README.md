# The Block 🧱

**A forum and community hub for Black people in AI, coding, and tech.**
One HTML file. Zero build steps. Live on Supabase.

> Pick a room. Tap in. Make a move.

---

## What this is

The Block is a complete, working community site — landing page, 9-room forum with
images and votes, a No Cap social feed, a talent agency section, and a real
auth system — all in a single `index.html` file you can host anywhere
(GitHub Pages, Netlify drop, a USB stick, whatever).

The philosophy: **1 FILE, 0 BUILD STEPS.** No npm, no bundler, no framework. If
you can open a file, you can run The Block.

## Quick start

```bash
git clone https://github.com/FED-OS/the-block-forum.git
cd the-block-forum
python3 -m http.server 8090
# open http://localhost:8090
```

Or just open `index.html` in a browser (demo mode works even from `file://`).
For the full guide, see [docs/SETUP.md](docs/SETUP.md).

## The repo

| File / folder | What it is |
|---|---|
| `index.html` | The entire site — landing, forum, No Cap, BLM agency, favicon |
| `login.html` | Standalone Supabase login page (email + password, PKCE, OAuth buttons) |
| `oauth/consent/index.html` | OAuth consent screen (for the Supabase OAuth Server) |
| `supabase-setup.sql` | One-paste backend: tables, security, storage, view counters |
| `assets/css/forum.css` | Standalone copy of the forum styles (reference) |
| `favicon.svg` + `favicon.ico` + `favicon-*.png` + `apple-touch-icon.png` | Icons |
| `docs/` | All the guides (setup, auth, Supabase, deployment, testing, FAQ) |
| `CHANGELOG.md` | Every change, newest first |
| `CONTRIBUTING.md` | How to contribute code, content, or bug reports |
| `LICENSE` | MIT |

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

## Modes

**Demo mode** — works instantly, everything stored in the visitor's browser
(localStorage). Great for trying it out.

**Supabase mode** — the real deal. Threads, replies, votes, images, view
counters, and auth are shared with everyone through your Supabase project.
Flip `FORUM_CONFIG` in `index.html` and run `supabase-setup.sql`. Details:
[docs/SUPABASE.md](docs/SUPABASE.md).

## Auth

Sign-in is email + password, straight on the forum page. Make an account with
the **Sign up** button, or use the standalone login page (`login.html`) which
also has GitHub / Google OAuth buttons. All auth runs through Supabase with
the PKCE flow. Details: [docs/AUTH.md](docs/AUTH.md).

## Docs

- [Setup guide](docs/SETUP.md) — from clone to live in 10 minutes
- [Supabase guide](docs/SUPABASE.md) — the backend explained
- [Auth guide](docs/AUTH.md) — email + password, OAuth, consent screen
- [Deployment guide](docs/DEPLOYMENT.md) — GitHub Pages, Netlify, Vercel, anywhere static
- [Testing guide](docs/TESTING.md) — how the site is verified before shipping
- [FAQ](docs/FAQ.md) — common questions

## Security notes

- The key in the frontend is the **publishable** key (`sb_publishable_…`) — safe
  to ship in browser code, guarded by row-level security on the server.
- The **secret** key (`sb_secret_…`) is for servers only and must never appear
  in this repo's HTML/JS. If you accidentally expose it, rotate it in the
  Supabase dashboard (Project Settings → API Keys) immediately.
- Row-level security (RLS) is on every table: anyone can read, only authors can
  write, staff can moderate. See `supabase-setup.sql`.

## License

MIT — see [LICENSE](LICENSE).

## Credits

Built for **FEDPromptly** — FED-OS · [github.com/FED-OS](https://github.com/FED-OS) ·
[ko-fi.com/fedpromptly](https://ko-fi.com/fedpromptly)

Black Leaders Management: blm@fedpromptly.com
