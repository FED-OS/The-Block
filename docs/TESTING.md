# Testing Guide

How The Block gets verified before every change ships. If you're
contributing, run the same checks (see [CONTRIBUTING.md](../CONTRIBUTING.md)).

## The one-minute smoke test

```bash
cd the-block-forum
python3 -m http.server 8090
```

Open `http://localhost:8090` and walk the happy path:

1. Page loads, no console errors (F12).
2. Theme toggle flips light/dark.
3. Scroll the whole page — no layout breaks.
4. Forum: pick a room tab, post a thread, attach an image, reply.
5. Delete your thread — confirm dialog, then gone.
6. No Cap: post, like, delete.
7. Sign out / sign in (Supabase mode).

## JavaScript syntax check

Every `<script>` block in `index.html` and `login.html` gets extracted and
run through Node's parser:

```bash
python3 - <<'EOF'
import re, subprocess
for f in ('the-block-forum/index.html', 'the-block-forum/login.html',
          'the-block-forum/oauth/consent/index.html'):
    s = open(f, encoding='utf-8').read()
    for i, b in enumerate(re.findall(r'<script>([\s\S]*?)</script>', s)):
        open('/tmp/blk.js', 'w').write(b)
        r = subprocess.run(['node', '--check', '/tmp/blk.js'], capture_output=True)
        print(f, i, 'OK' if r.returncode == 0 else 'FAIL')
EOF
```

(Inline scripts only — the CDN supabase script tag is skipped.)

## Auth checks (Supabase mode)

Tested against the live project before shipping:

| Case | Expected |
|---|---|
| Sign up (new email, 6+ char password) | Account made, signed in |
| Sign in, wrong password | "Invalid login credentials", stays signed out |
| Sign in, correct password | Signed in, name shows in the strip |
| Sign out | Signed-out form returns |
| Short password (<6) | Instant client-side message, no network call |
| Bad email format | Instant client-side message |
| Sign-in required to post | Toast: "Sign in first (email + password)." |

## Permission checks

| Case | Expected |
|---|---|
| Signed out visitor | Can read everything; delete buttons absent |
| Signed-in user | Delete buttons only on **their own** threads/replies/No Cap posts |
| Staff account | Delete buttons on everything, 🛡 flair |
| Direct API delete attempt (RLS) | Server rejects — 403/empty result |

## View counters

- Open a thread → its view count +1, chip updates in place.
- Re-render (delete a reply, navigate back) → **no** double-count
  (sessionStorage guard).
- Signed-out visitor → still counts (RPC, anonymous-safe).
- `ALL-TIME PULL-UPS` climbs per browser session.

## Images

- Up to 3 per thread/reply, 1 per No Cap post.
- Oversized images get resized/compressed in the browser (~260KB each).
- GIF/WebP/PNG/JPG all fine.

## Realtime (Supabase mode)

Open the site in two windows. Post in one — it appears in the other without
refresh.

## Cleanup after testing

Test accounts and test threads get deleted before shipping (Supabase
dashboard → Authentication → Users, and Table Editor for rows). The shipped
state: tables exist, no leftover test junk.

## The philosophy

No build step means the failure modes are simple: a typo in the HTML, a
policy mis-write in SQL, or a Supabase setting. Those are exactly what these
checks catch, so they're run on every change.
