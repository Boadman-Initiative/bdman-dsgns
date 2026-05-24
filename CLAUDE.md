# CLAUDE.md · `boadman-designs/`

> Read this **before touching anything in this folder**. It captures the
> visual-grammar contract, the file conventions, and the working model
> agreed with the user. Skipping it means re-doing work.

## 0 · What this folder is

The Boadman v2 design system + interactive prototype + backend roadmap
in one place. This is **not a throwaway mockup**: it's the spec the
production app builds from. Tokens and component classes are lifted into
the real app as-is.

There are two siblings (do **not** touch):

- `../docs/design-v2/` — earlier experimental v2 (kept frozen for diff)
- `../docs/design-v3/`, `../docs/design-v3-preview/` — other experiments

## 1 · The user's working model

- **Design-first.** The backend was built before the design was settled —
  acknowledged mistake. Going forward, the design defines the spec and
  the backend either supports it, gets refactored to support it, or the
  design flags the gap in `backend-gaps.md`.
- **One feature at a time.** For each feature surface (e.g. tournaments,
  KYC, wallet), dispatch a `general-purpose` subagent into
  `../boadman-api/` to diff what's wired against the design. Update
  `backend-gaps.md` with the findings. Then decide: keep, refactor,
  restructure, or new build.
- **Stakeholder visibility.** This folder may be shown to non-technical
  stakeholders. Visible "Backend gap" chips are OK on affected sections,
  but the screens should mostly read as the v1 vision, not a list of
  TODOs.
- **Iterative review.** Big design decisions happen via batch reviews —
  show 2–3 hero screens first to validate visual grammar, then mass-produce.
- **Desktop first for now.** Mobile gets its own pass later. Every
  screen is responsive but desktop is the canvas to perfect.

## 2 · Visual-grammar contract

Codified in [`brand-spec.md`](./brand-spec.md). Headline rules:

| Surface | Theme | Accent | Tone |
|---|---|---|---|
| Player · Brand Manager · Publisher · Public | Dark default (`#0B0C10` page) | Orange `#FF5733` (from real `BoadmanLogo.svg`) | Gamified · kinetic · confident |
| Admin · MLRO · Compliance | Deep slate (`#0F1015` / `#181922`) — **not white** | Purple `#9B6BFF` | Calm · institutional · deliberate |

A player holding a coin balance and an MLRO freezing an AML wallet
should feel they are in different rooms of the same building. Same
brand, different temperature.

### The non-negotiables

- **Boadman logo** = the Spartan helmet from `_assets/svgs/BoadmanLogo.svg`.
  No abstract substitutes. Always at the top of the sidebar — not in the
  page header.
- **Type stack** = Inter (body) + Space Grotesk (display/money) +
  **Barlow Condensed 800** (HUD stamps, nav, badges, military voice).
  Money always `font-variant-numeric: tabular-nums`.
- **Money is sacred.** Every coin / GBP / NGN amount is rendered in
  Space Grotesk, never abbreviated for prizes. Explicit units.
- **Compliance copy is non-euphemistic.** "Cash-out frozen" — not
  "Temporarily restricted". Regulators demand honesty.
- **Provisional rates footnote** on every economics-revealing surface:
  > Rates illustrative — pending SP8-ECON economics review.
- **No filler.** Every element earns its place. No data slop, no
  decorative icons next to every label, no meaningless stats.

### Reused components (in `_tokens/components.css`)

Lift these, don't re-implement:

- `.vault-card` (the tactical-vault coin card)
- `.hold-card` (48 h prize hold tile)
- `.banner` + `--warning` / `--danger` / `--compliance` etc. (regulatory banners)
- `.t-hero` (tournament hero with game banner bg + vignette + grain)
- `.podium-display` (top-3 winners)
- `.recorder` (host's winner-selection panel)
- `.live-readout` (live-match status block)
- `.roster-slot` (participant card)
- `.hud-stat` (KPI stat block)
- `.sponsor-row`, `.tx-row`, `.audit-row`
- `.tier-emblem--gold/silver/bronze`
- `.nav-rail` + `.nav-rail__brand` (sidebar with logo at top)
- `.rail-balance` (in-rail balance preview)
- `.state-switcher` (prototype-only state-toggle chrome — strip for prod)

## 3 · File conventions

```
boadman-designs/
├── CLAUDE.md                    # ← you are here
├── README.md                    # human-readable overview
├── brand-spec.md                # visual-grammar contract
├── backend-gaps.md              # design-driven engineering roadmap
├── index.html                   # navigator (simplified — link list, no iframe previews)
│
├── _tokens/
│   ├── tokens.css               # CSS custom properties — single source of truth
│   └── components.css           # reusable component classes — no JS
│
├── _components/
│   ├── showcase.html            # live component library
│   └── logo-exploration.html    # logo lockup variations
│
├── _assets/
│   ├── svgs/                    # icon set + Boadman logo
│   ├── images/                  # full-resolution PNGs
│   ├── games/                   # game banner art
│   ├── avatars/                 # illustrated player avatars
│   └── partners/                # partner brand logos
│
├── player/                      # client (Player) actor screens
├── brand/                       # Brand Manager screens
├── publisher/                   # Publisher screens
├── admin/                       # Admin / MLRO compliance screens
├── public/                      # marketing + legal (anonymous)
└── emails/                      # transactional email templates
```

### Rules when adding a screen

1. **One HTML per screen.** Easy to lift into the production app.
2. **Link to shared tokens + components.** `<link rel="stylesheet" href="../_tokens/tokens.css">` and `.../components.css`. Page-specific styles go in a `<style>` block at the top.
3. **Sidebar always carries the logo at the top.** Use the `.nav-rail__brand` pattern. Header keeps only search + bell + avatar.
4. **State variants = state-switcher.** When a screen has multiple non-trivial states (e.g. wallet default vs frozen, tournament open/live/awaiting/completed), use the `.state-switcher` pattern from `_tokens/components.css`. One file, multiple states, prototype-only switcher bar at top.
5. **Backend-gap surfaces get a chip.** `<span class="badge badge--unwired">Backend gap — X needed</span>` on affected sections. Add the line item to `backend-gaps.md`.
6. **Mobile is tolerated, not perfected.** Desktop is the canvas. Mobile responsive is a free bonus from the component library — don't fight for it now.

### Compliance theme

Add `data-theme="compliance"` to `<html>`. Tokens auto-swap to deep slate
+ purple accent. See `admin/aml-flag-detail.html` for the canonical example.

## 4 · The state-switcher pattern

Used on screens with multiple non-trivial product states. Toggle bar at
the top of the page, sections gated by `data-state` attribute, JS-toggled
via `data-active-state` on the parent.

```html
<main data-active-state="open">
  <div class="state-switcher">
    <button class="state-switcher__btn is-active" data-set-state="open">Open</button>
    <button class="state-switcher__btn" data-set-state="live">Live</button>
    <!-- … -->
  </div>

  <section data-state="open">…</section>
  <section data-state="live">…</section>
</main>

<script>
  document.querySelectorAll('[data-set-state]').forEach(btn => {
    btn.addEventListener('click', () => {
      document.querySelector('main').dataset.activeState = btn.dataset.setState;
      document.querySelectorAll('[data-set-state]').forEach(b =>
        b.classList.toggle('is-active', b === btn)
      );
    });
  });
</script>
```

CSS does the show/hide via attribute selectors in `components.css`. **Strip
the switcher when lifting into production** — real shipping screens render
one state at a time based on actual data.

## 5 · Subagent investigation workflow

When the user says "investigate feature X":

1. Spawn a `general-purpose` subagent with a prompt that names the feature
   and points at `../boadman-api/` and the relevant design file.
2. Ask the subagent to:
   - List existing endpoints, tables, jobs touching the feature
   - Compare against the design's implied surface
   - Output a diff: WIRED / PARTIAL / MATCHES_DESIGN / MISSING
   - Reference exact file:line for each finding
3. Pull the findings into `backend-gaps.md` and update entries from
   "design implies" to "diff vs. current code."
4. Then make the keep / refactor / restructure / new-build call with the
   user.

## 6 · What this folder does **not** do

- No JavaScript beyond simple toggles. Real behaviour is wired in the
  production app by the engineering team.
- No backend integration, no auth, no Stripe / Paystack / Didit calls.
- No marketing-page animations, no scroll-triggered effects. This is a
  product, not a campaign.
- No mobile-specific layouts (until the dedicated mobile pass starts).

## 7 · Provisional values (load-bearing disclaimer)

Surfaces showing rates carry this footnote inline:

> *Rates illustrative — pending SP8-ECON economics review.*

Current interim values used across screens:

| Rate | Provisional value |
|---|---|
| Challenge fee | 12 % of pot |
| Tournament commission · player-hosted | 12 % (9 % platform + 3 % host) |
| Tournament commission · brand-hosted | 15 % (no host cut) |
| Publisher royalty | 15 % of platform commission |
| Minimum wager | 100 coins |
| Coin rate | 1 coin = £0.10 |

If economics review changes these, the impact is a tokens-and-copy sweep
— no structural redesign required.

## 8 · Provenance & source docs

- `../docs/features/flows.md` — every user-visible flow
- `../docs/features/glossary.md` — vocabulary used verbatim in UI copy
- `../docs/features/screen-inventory.md` — full screen catalogue
- `../docs/features/notification-channels.md` — channel matrix
- `../docs/features/future-implementation.md` — designed-but-not-wired items
- `../app-screenshots/Screenshot 2026-04-16 at 17.36*.png` — visual reference
- `../boadman-landing/src/assets/` — real brand assets (logo, games, avatars)
- `../boadman-api/` — the codebase the design either matches or implies a
  diff against (see `backend-gaps.md`)
