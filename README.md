# Boadman · Design System &amp; Prototype

> **Status — 2026-05-21:** Showcase batch (3 hero screens + tokens +
> components + brand spec). Awaiting your sign-off on the visual grammar
> before batch-producing the remaining ~140 screens grouped by actor
> surface.

This folder is *both* the stakeholder-facing prototype **and** the
production design system. Tokens and component classes are lifted into
the real app as-is — no transformation step.

## How to open

```bash
open docs/design/index.html      # macOS
xdg-open docs/design/index.html  # Linux
```

Or drag any `.html` file into a browser tab. No server, no build step. All
font and asset URLs are remote (Google Fonts).

## What's in the box

| File | Purpose |
|---|---|
| [`brand-spec.md`](brand-spec.md) | The canonical design reference: philosophy, colour, type, component inventory, mockup-chrome convention. **Read this before designing anything new.** |
| [`_tokens/tokens.css`](_tokens/tokens.css) | CSS custom properties — every colour, type scale, spacing, radius, shadow, motion token. Single source of truth. |
| [`_tokens/components.css`](_tokens/components.css) | Reusable component classes (`.btn-primary`, `.card`, `.banner`, `.kpi`, `.bracket-node`, `.audit-row`, etc.). No JavaScript required. |
| [`_components/showcase.html`](_components/showcase.html) | Live component library — every class demonstrated with realistic content. Has a theme-toggle button to flip between dark and light surfaces. |
| [`index.html`](index.html) | Top-level navigator. Renders each hero screen at **desktop 1440** and **iPhone 15 Pro 393** side-by-side so the responsive behaviour is visible without resizing the window. |
| `player/wallet.html` | Hero #1 · Wallet with cash-out-frozen variant. |
| `player/tournament-detail.html` | Hero #2 · Tournament detail with active bracket + sponsorship attribution + host actions. |
| `admin/aml-flag-detail.html` | Hero #3 · MLRO AML flag review. Light theme + compliance purple accent. |

## Production-ready conventions

1. **One screen = one HTML file.** No multi-screen single-page apps for
   the prototype. The team lifts each file into a route in the real app.
2. **Responsive in a single document.** Mobile and desktop are
   breakpoints of the same file (`min-width: 1024px` flips on the
   sidebar; below it the bottom tab-bar appears). The component classes
   handle this automatically.
3. **No framework lock-in.** Pure HTML + CSS, no React, no Vue, no
   build. Whatever framework the team uses, they import
   `tokens.css` + `components.css` and the classes work.
4. **State variants live in separate files.** Where a screen has multiple
   non-trivial states (e.g. wallet default vs frozen), each variant gets
   its own file: `wallet.html`, `wallet-frozen.html`. Engineers can see
   the structural diff. The variant currently shipped is the
   frozen one (most demanding case).
5. **No JavaScript beyond toggles.** This is a static prototype meant to
   be lifted into a real codebase. The team wires real behaviour.

## Design philosophy snapshot

| Surface | Theme | Accent | Tone |
|---|---|---|---|
| Player / Brand Manager / Publisher / Public | Dark (default) | Orange `#F4661F` | Gamified · kinetic · confident |
| Admin / MLRO / Compliance | Light | Purple `#9B6BFF` | Calm · institutional · deliberate |

A player holding a coin balance and an MLRO freezing an AML wallet
should feel they are in different rooms of the same building. Same
brand, different temperature.

## Hand-off conventions for the engineering team

When you build a feature from these designs:

1. **Pull tokens first.** `tokens.css` belongs at the top of your CSS
   import chain. Every colour / spacing / radius / font reference in the
   real app should resolve to one of these custom properties. Hard-coded
   hex values are a code-review blocker.
2. **Reuse the component classes.** Don't reimplement `.btn-primary`
   from scratch — the design system version handles focus, hover,
   pressed, disabled, and sizing variants for free.
3. **State must be visible.** If you're adding a new state to a screen
   (e.g. "AML hold" on the wallet), it needs a banner / badge / chip in
   the design system. Don't infer state from icon-only signals.
4. **Compliance copy stays non-euphemistic.** "Cash-out frozen pending
   AML review" stays as-is. Don't soften it. The platform is honest with
   the user about regulatory consequences.
5. **Money always renders as `.money` or `.coin-balance`** with the
   tabular-nums and Space Grotesk treatment. Never an `<input>` with
   default styling.
6. **Footnote rates on every economics-revealing screen.** Single
   include of `<p class="footnote-rates">Rates illustrative — pending
   SP8-ECON economics review.</p>`. The text is canonical — don't paraphrase.

## Batch roadmap (remaining ~140 screens)

The work is decomposed by actor so each batch is end-to-end coherent. Each
batch produces real screen files + adds them to the actor-level
`index.html` navigator + checks them off the coverage roadmap in the
top-level `index.html`.

| Batch | Surface | Screens | Estimated size |
|---|---|---|---|
| **1** | Player · Account lifecycle (sign-up → KYC → deletion) | ~16 | medium |
| **2** | Player · Wallet rest-of-flows (buy / cash-out / payout methods) | ~13 | medium |
| **3** | Player · Competitions rest-of-flows (challenges, tournament hub & create, leagues) | ~21 | large |
| **4** | Brand Manager surfaces | ~6 | small |
| **5** | Publisher surfaces | ~5 | small |
| **6** | Disputes, complaints, evidence (Player side) | ~3 | small |
| **7** | Public marketing / legal / responsible-play | ~7 | small |
| **8** | Admin & MLRO back-office (rest-of-queues + detail) | ~27 | large |
| **9** | Notifications inbox + email templates | ~18 | medium |
| **10** | Error / edge / system states | ~6 | small |

Each batch closes with a manifest update + a couple of "verify in browser"
screenshots. The team should not be surprised by any screen.

## Provisional economics — load-bearing disclaimer

Every screen showing a rate carries this footnote inline:

> *Rates illustrative — pending SP8-ECON economics review.*

These are the interim values currently used across all screens:

| Rate | Provisional value | Real screen behaviour |
|---|---|---|
| Challenge fee | 12 % of pot | Hard-coded in challenge create + match auto-resolve |
| Tournament commission · player-hosted | 12 % (9 % platform + 3 % host) | Surfaced on prize-distribution panel |
| Tournament commission · brand-hosted | 15 % (no host cut) | Surfaced on brand-host create + release |
| Publisher royalty | 15 % of platform commission | Surfaced on Publisher hub |
| Minimum wager | 100 coins | Validated on challenge create |
| Coin rate | 1 coin = £0.10 | Surfaced on every coin-balance card |

If economics review changes any of these, the impact is a tokens-and-copy
sweep — no structural redesign required.

## Source documents

The design is derived from:

- [`docs/features/flows.md`](../features/flows.md) — every user-visible flow
- [`docs/features/glossary.md`](../features/glossary.md) — vocabulary used verbatim in UI copy
- [`docs/features/screen-inventory.md`](../features/screen-inventory.md) — the screen catalogue
- [`docs/features/notification-channels.md`](../features/notification-channels.md) — channel matrix
- [`docs/features/future-implementation.md`](../features/future-implementation.md) — items marked "Coming soon — not yet wired"
- `/app-screenshots/Screenshot 2026-04-16 at 17.36-17.37.*.png` — visual reference (palette + gamified energy preserved; IA freshly derived)
