# Contributing to The Block

Real talk: this project works because it's simple. Before you send a pull request, read this whole page — most of it exists to keep it that way.

## The rules (the ones that matter)

**1. One file. Zero build steps.** The forum is `index.html` — inline CSS, inline JS, no bundler, no framework, no `npm install`, no compile. If your change requires a build step, a dependency, or a second file for the forum core, it will not be merged. The exceptions that already exist: `login.html` and `oauth/consent/index.html` (standalone pages), the `assets/css/` and favicon files, docs, and `supabase-setup.sql`.

**2. No frameworks, no libraries.** Vanilla JS only, plus the Supabase client from the CDN when live mode is active. If you need a date library, a component system, or a state manager, the feature is too complicated.

**3. Test both modes.** Every change gets tested in demo mode (no Supabase) AND live mode (connected project). If a feature only works in one mode, either make it work in both or gate it cleanly with a clear message. See [TESTING.md](docs/TESTING.md) for the full checklist.

**4. Plain language in the UI.** This product serves a community, not a developer conference. Buttons say what they do. Errors say what happened and what to do next. No jargon, no "Oops! Something went wrong" with no detail.

**5. Keep the palette and the vibe.** Colors live in the `:root` custom properties (`--paper`, `--green`, `--acid`, etc.). Use them — don't hardcode hex values in components. Fonts: Space Grotesk for UI, JetBrains Mono for code and handles.

## Getting set up

```bash
git clone <your-fork-url>
cd the-block-forum
# open index.html directly, or:
python3 -m http.server 8090
# → http://localhost:8090
```

Demo mode works immediately — no keys needed. To test live mode, you need your own Supabase project: [docs/SETUP.md](docs/SETUP.md) walks through it end to end, including running `supabase-setup.sql` and flipping `FORUM_CONFIG`.

For JavaScript changes, validate before you commit — extract and syntax-check the script blocks:

```bash
python3 - <<'EOF'
import re
html = open('index.html').read()
for i, block in enumerate(re.findall(r'<script>([\s\S]*?)</script>', html), 1):
    path = f'/tmp/blk{i}.js'
    open(path, 'w').write(block)
    print(path, len(block))
EOF
for f in /tmp/blk*.js; do node --check "$f" && echo "$f OK"; done
```

## What to work on

Good first contributions:

- **Bug fixes** with reproduction steps — check open issues first.
- **Accessibility** — keyboard paths, focus states, ARIA labels, contrast. Always welcome.
- **Documentation** — the `docs/` folder is part of the product; clearer docs are real contributions.
- **New rooms** — a new `categories` row plus room tab, if the room earns its slot (nine is not a hard cap, but every room needs a reason to exist).

Things that will get closed without merge:

- React/Vue/Svelte/component-framework rewrites of the forum core
- Build tooling, package.json, webpack, TypeScript compilation steps
- New CDN dependencies beyond the existing Supabase client and fonts
- Cryptocurrency, token-gating, or monetization bolt-ons
- Anything that breaks demo mode

## Pull request checklist

Before you open the PR, confirm every line:

- [ ] Tested in demo mode (fresh browser, no Supabase keys)
- [ ] Tested in live mode (own Supabase project, clean test data cleaned up after)
- [ ] `node --check` passes on every script block (command above)
- [ ] No new dependencies, no build steps, no frameworks
- [ ] Colors/fonts via CSS custom properties, not hardcoded values
- [ ] UI copy is plain language, on-brand, no lorem ipsum
- [ ] Mobile width (390px) doesn't break — horizontal scrolling is a bug
- [ ] Works at a subfolder path (e.g. `site.com/the-block-forum/`) — relative paths only
- [ ] Docs updated if behavior changed ([CHANGELOG.md](CHANGELOG.md) entry added)

## Reporting bugs

Open an issue with: what you did, what you expected, what happened instead, browser + OS, and demo or live mode. A link to a screenshot or the exact text of any console error gets you a faster fix. "Doesn't work" with no detail goes to the bottom of the pile.

## Conduct

This project exists to serve Black people in tech. Racism, sexism, harassment, or trolling in issues and PRs gets you blocked — no debate, no appeal. Keep it useful, keep it respectful.

## License

By contributing, your work is released under the project's [MIT License](LICENSE).

---

Questions not covered here? Check the [FAQ](docs/FAQ.md) first, then open an issue.
