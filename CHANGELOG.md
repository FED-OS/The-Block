# Changelog

Every change to The Block, newest first. This project doesn't do surprise rewrites — if something shipped, it's on this page.

## [Unreleased]

### Planned

- Post editing (delete-and-repost is the current workaround — see [FAQ](docs/FAQ.md))
- Thread search across rooms
- Notification stack improvements
- Keyboard shortcuts for power users

## [0.5.0] — 2026

### Fixed

- **Voting, for real.** Up/down votes in live mode were hardcoded to zero and the "you voted" highlight only read the demo-mode store, so live voting looked dead. Live mode now fetches every vote, sums it in the browser, marks your own vote, and clicking the arrow you already voted removes the vote entirely (toggle off). The server doesn't support aggregate queries, so the math is done client-side on the raw vote rows.
- **Usernames on posts.** New "Post as" field on the thread composer and the reply box. Pick a name once and it's saved to your profile in live mode (or to the browser in demo mode), pre-filled next time, and shown as the author on your posts and in the header strip instead of your raw email.

### Changed

- **Image previews on thread cards.** Cards used to show only a "🖼 N" chip when a post had images; now they show real thumbnail previews of each image plus the chip.
- **Full-size images in post detail.** The post view capped images at a small box; images now render up to 640px wide at full aspect ratio (still tappable to open the lightbox for the original).
- **View count moved to the end of the post card.** The reply count now sits with the date in the card's meta row and the view count closes out the card's right edge, as requested.
- **Top navigation strip restored.** Removed in v0.4.0, back by popular demand in v0.5.0 with all eight section links, a mobile menu button, and dark-theme link colors that actually work. The hero stats read the honest numbers: 9 rooms, 1 file, 0 build steps.

### Security

- Confirmed (again) that no frontend file contains the `sb_secret_…` key — only the publishable key ships in HTML.

## [0.4.0] — 2026

### Added

- **Email + password authentication** everywhere. Replaced the magic-link email flow with a real sign-in strip in the forum header — email and password fields, Sign in and Sign up buttons, inline validation, real Supabase error messages. [AUTH.md](docs/AUTH.md)
- **`login.html` — standalone login page.** Single-file, embeddable, brand-matched. Email + password, sign-up toggle, GitHub and Google OAuth buttons, signed-in view with sign-out. Connects to the same Supabase project and the same accounts as the forum.
- **`oauth/consent/index.html` — OAuth consent screen.** For Supabase's OAuth Server feature: shows the requesting app and its scopes from the `authorization_id`, with Approve/Deny. Handles missing IDs, signed-out users (redirects to login), and old CDN builds.
- **Full delete capability.** Users can delete their own posts, threads, and replies — confirm dialog, then the post and its images/votes/replies are removed from Supabase. Moderators can delete anyone's.
- **Full documentation set.** [SETUP.md](docs/SETUP.md), [SUPABASE.md](docs/SUPABASE.md), [AUTH.md](docs/AUTH.md), [DEPLOYMENT.md](docs/DEPLOYMENT.md), [TESTING.md](docs/TESTING.md), [FAQ.md](docs/FAQ.md), plus [CONTRIBUTING.md](CONTRIBUTING.md), this changelog, [LICENSE](LICENSE), and `.gitignore`.

### Changed

- **Removed the top navigation strip and the "15 ROOMS 15 TABS" marketing stat.** The forum has nine rooms, not fifteen; the strip overstated it and half its links pointed at sections that didn't earn their slot. Navigation now runs through the room tabs, footer links, and in-page CTAs — all of which go somewhere real. (Restored in v0.5.0.)
- Rewrote **README.md** as a proper front page for the repository — what it is, quick start, file map, rooms table, auth summary, and links to every doc.
- Supabase client config hardened: PKCE flow, session persistence, auto token refresh, URL session detection.

### Security

- Confirmed the `sb_secret_…` key appears in **no** frontend file. Only the publishable key ships in HTML; all data access is enforced by Row Level Security. Key policy documented in [SUPABASE.md](docs/SUPABASE.md) and [FAQ.md](docs/FAQ.md).

## [0.3.0] — 2026

### Added

- **BLM Agency tab** — the ninth room, connecting community members to Black-led movement work and resources.
- **Favicon suite** — `favicon.svg`, `favicon.ico`, and PNG sizes 16/32/48/192/512 plus `apple-touch-icon.png`, all carrying the brand mark.
- **`assets/css/forum.css`** — extracted print stylesheet so threads print clean on paper.

## [0.2.0] — 2026

### Added

- **No Cap feed** — anonymous short posts with likes, `nocap_posts` + `nocap_likes` tables, fully moderated via the reports system.
- **Site + per-thread view counters** — computed with database RPCs (`bump_thread_views`, `bump_site_views`, `get_site_views`) so counters can't be inflated from a console.
- **Image posting** — upload to the `forum-images` Supabase storage bucket, inline rendering, client-side size limits; data URLs in demo mode.

## [0.1.0] — 2026

### Added

- Initial release. Nine-room forum (Landing/Culture, Music + Audio, Art + Design, Code + Engineering, Business + Money, AI/ML, Web3, Games, plus BLM Agency), threads, replies, upvotes, profiles, reports, staff/moderation model, demo mode with localStorage, live mode on Supabase with Row Level Security, realtime updates.
- `supabase-setup.sql` — the complete database schema, policies, buckets, and RPCs in one runnable script.

---

Format loosely based on [Keep a Changelog](https://keepachangelog.com/). Versions before 0.1.0 were development builds and aren't listed.
