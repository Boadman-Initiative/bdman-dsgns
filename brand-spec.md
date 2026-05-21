# Boadman — Brand & Design Spec  ·  v2

> Canonical reference for every design decision in `boadman-designs/`. The
> HTML prototype is the proof; this document is the rule. When in doubt,
> the spec wins. Tokens resolve to CSS custom properties in
> `_tokens/tokens.css`; component classes resolve in
> `_tokens/components.css`.

**Status — v2 frozen 2026-05-21.** Source-of-truth for v1 launch (July 2026).
Provisional economics (challenge fee, tournament commission, publisher
royalty, min wager) are **interim — pending SP8-ECON economics review**.
Every screen that surfaces a rate carries an inline footnote.

### v2 changes vs v1

- **Brand mark is the real Spartan helmet** from `boadman-landing` (was an
  abstract orange square).
- **Type stack adds Barlow Condensed 800** for HUD stamps, nav labels,
  badges, military voice (alongside Inter body + Space Grotesk display).
- **Brand accent is `#FF5733`** (extracted from the helmet SVG), not the
  earlier `#F4661F` approximation.
- **Coin card is the tactical "vault"** — engraved double-border, helmet
  seal, recessed display slot with corner reinforcements, scan-line
  interior, serial + sync-pulse foot.
- **Tournaments are single-match free-for-all** — bracket / matrix /
  leaderboard concepts removed. Host configures `winner_count` and picks
  winners from the roster after the match.
- **Compliance theme is deep slate** (`#0F1015` / `#181922`), not pure
  white. Purple accent (`#9B6BFF`) replaces orange to prevent
  cross-context CTA confusion.
- **Logo sits at the top of the sidebar**, not in the page header.
- **State-switcher pattern** for screens with multiple non-trivial states
  (one file, multi-state, prototype-only chrome at top — stripped in prod).
- **Backend-gaps register** (`backend-gaps.md`) is now a first-class
  artefact alongside this spec.

---

## 1. Design philosophy

> **Esports operator with banker discipline.**

Two halves coexist in this product without ever blurring:

1. **Competitive surfaces** (Player, Brand-Manager, Publisher, Public marketing,
   Tournament/League/Challenge) feel **gamified, kinetic, confident**. Dark
   theme by default, single hot-orange accent, generous heroes, illustrated
   character avatars, hi-contrast tier emblems.
2. **Compliance surfaces** (KYC, AML, MLRO, Sanctions, Rectification, Brand
   Application Review, Financials Queue, Audit Trail) feel **calm,
   institutional, deliberate**. Light theme on dense tables, secondary
   purple accent in place of orange (so an Admin cannot confuse a queue
   action with a player-facing CTA), generous data hierarchy, tabular
   numbers everywhere.

A user holding a coin balance and a user freezing an AML wallet should
feel they are in different rooms of the same building.

### Operating principles

- **Money is sacred.** Every coin / GBP / NGN amount is rendered in
  Space Grotesk with `font-variant-numeric: tabular-nums`. Always with an
  explicit unit. Never abbreviated for prizes (`12,400 coins · £1,240.00`
  not `12.4k`). Pence and decimals are honest.
- **One accent at a time.** Orange is reserved for the primary CTA, the
  active navigation state, the focus ring, and one hero glow per page.
  If you find yourself reaching for orange a fourth time on the same
  screen, you are decorating. Use neutrals.
- **Compliance copy is non-euphemistic.** "Account suspended" — not "Take
  a break". "Cash-out frozen pending AML review" — not "Temporarily
  restricted". The platform is honest with the user about regulatory
  consequences because UK MLR 2017 demands it and because trust depends
  on it.
- **State is visible.** Every screen with an interesting state machine
  (wallet, dispute, KYC, account, brand application, prize hold, AML flag)
  exposes the current state explicitly in a labelled badge — never relies
  on color alone.
- **Provisional rates are footnoted.** Every screen showing a rate has
  the footer "Rates illustrative — pending SP8-ECON economics review" in
  small muted type. Stakeholders are never misled.
- **No filler.** Every element earns its place. No data slop ("Players
  online: 1,247 ↑"), no decorative icons next to every label, no
  meaningless stats.

---

## 2. Colour system

All values are also defined as CSS custom properties — see
[`_tokens/tokens.css`](./\_tokens/tokens.css).

### 2.1 Dark theme (default — Player, Brand, Publisher, Public, Compliance-on-mobile)

| Token | Hex | Use |
|---|---|---|
| `--bg-page` | `#0E0F13` | Page background |
| `--bg-panel` | `#15171F` | Sidebar, card surface |
| `--bg-elevated` | `#1C1F2A` | Modals, dropdowns, hover surface |
| `--bg-input` | `#0A0B0F` | Form field background |
| `--border-subtle` | `rgba(255,255,255,0.06)` | Card dividers, list separators |
| `--border-default` | `rgba(255,255,255,0.10)` | Standard borders |
| `--border-strong` | `rgba(255,255,255,0.18)` | Focused / emphasised borders |
| `--text-primary` | `#F2F3F5` | Body copy, headings |
| `--text-secondary` | `#9AA0AB` | Meta, labels, sub-copy |
| `--text-muted` | `#6B7280` | Disabled, helper, footnotes |
| `--text-inverse` | `#0E0F13` | Text on accent backgrounds |

### 2.2 Light theme (Admin tables, public legal pages, Responsible Play)

| Token | Hex | Use |
|---|---|---|
| `--bg-page` | `#F7F7F9` | Page background |
| `--bg-panel` | `#FFFFFF` | Card surface |
| `--bg-elevated` | `#FFFFFF` + shadow | Modals, dropdowns |
| `--bg-input` | `#F7F7F9` | Form field background |
| `--border-subtle` | `#ECEDF1` | Card dividers, table rows |
| `--border-default` | `#DDE0E6` | Standard borders |
| `--border-strong` | `#B5BAC4` | Focused / emphasised borders |
| `--text-primary` | `#14161F` | Body copy, headings |
| `--text-secondary` | `#5C6373` | Meta, labels |
| `--text-muted` | `#8B92A0` | Disabled, helper |

### 2.3 Brand accents (consistent across themes)

| Token | Hex | Use |
|---|---|---|
| `--orange-500` | `#F4661F` | Primary CTA, active nav, brand wordmark accent |
| `--orange-600` | `#DC5816` | CTA hover/pressed |
| `--orange-400` | `#FF834B` | Brighter highlight (sparingly) |
| `--orange-tint` | `rgba(244,102,31,0.12)` | Accent background washes |

### 2.4 Semantic colours

| Token | Hex | Meaning |
|---|---|---|
| `--green-500` | `#2BC472` | Active, success, prize-matured, verified |
| `--green-tint` | `rgba(43,196,114,0.14)` | Success badge background |
| `--red-500` | `#EE4456` | Blocked, banned, dispute-upheld, sanctions-hit |
| `--red-tint` | `rgba(238,68,86,0.14)` | Danger badge background |
| `--amber-500` | `#F5A623` | Pending, under review, prize-hold, AML-flagged |
| `--amber-tint` | `rgba(245,166,35,0.14)` | Warning badge background |
| `--blue-500` | `#4D87FF` | Informational, FX rate, system notice |
| `--blue-tint` | `rgba(77,135,255,0.14)` | Info badge background |
| `--purple-500` | `#9B6BFF` | Compliance/MLRO primary in admin theme |
| `--purple-tint` | `rgba(155,107,255,0.14)` | MLRO contextual background |

### 2.5 Tier accents (used on leaderboards, prize placements)

| Token | Hex |
|---|---|
| `--tier-gold` | `#E5B454` |
| `--tier-silver` | `#B8BCC4` |
| `--tier-bronze` | `#CC7B43` |

### 2.6 Accessibility floors

- Body text on `--bg-page` (`#F2F3F5` on `#0E0F13`): contrast ratio **15.9 : 1** ✓
- Secondary text on `--bg-panel` (`#9AA0AB` on `#15171F`): **5.8 : 1** ✓ (AA)
- Orange CTA text (`#FFFFFF` on `#F4661F`): **3.9 : 1** ✓ (AA large)
- Light-theme secondary (`#5C6373` on `#FFFFFF`): **6.9 : 1** ✓

---

## 3. Typography

Two faces. No third unless explicit need arises.

### 3.1 Type stack

```css
--font-display: 'Space Grotesk', ui-sans-serif, -apple-system, BlinkMacSystemFont, sans-serif;
--font-sans:    'Inter', ui-sans-serif, -apple-system, BlinkMacSystemFont, sans-serif;
--font-mono:    'JetBrains Mono', ui-monospace, 'SF Mono', monospace;
```

Loaded from Google Fonts in every screen page.

### 3.2 Roles

| Face | Role | Weights used |
|---|---|---|
| **Space Grotesk** | Display headings (h1-h2), wordmark "BOADMAN", coin/GBP amounts, statistic numbers, tier labels | 500, 600, 700 |
| **Inter** | Body, h3-h6, UI controls, table cells, navigation labels | 400, 500, 600 |
| **JetBrains Mono** | Audit-log entries, redemption IDs, hashes, MLRO codes | 400, 500 |

### 3.3 Scale (mobile · desktop)

| Token | Mobile | Desktop | Weight | Use |
|---|---|---|---|---|
| `--t-display` | 32 / 38 | 48 / 56 | 700 | Page hero (wordmark, marketing headline) |
| `--t-h1` | 24 / 30 | 32 / 40 | 700 | Screen title |
| `--t-h2` | 20 / 26 | 24 / 32 | 600 | Section heading |
| `--t-h3` | 16 / 22 | 18 / 26 | 600 | Card title |
| `--t-body` | 14 / 20 | 15 / 22 | 400 | Body copy |
| `--t-meta` | 12 / 16 | 13 / 18 | 500 | Labels, table headers, meta info |
| `--t-micro` | 11 / 14 | 11 / 14 | 500 | Footnotes, fine print |
| `--t-money-lg` | 32 / 38 | 48 / 56 | 600 | Headline coin balance |
| `--t-money` | 18 / 24 | 22 / 28 | 600 | Inline amount |

**Money always carries `font-variant-numeric: tabular-nums`.**

---

## 4. Spacing & radii

4-px baseline. The tokens `--s-1` (4) through `--s-12` (48) cover almost
everything. Use multiples — never half-units.

| Radius | Value | Use |
|---|---|---|
| `--r-sm` | 6 px | Chips, small badges |
| `--r-md` | 10 px | Inputs, buttons, list rows |
| `--r-lg` | 14 px | Cards |
| `--r-xl` | 20 px | Hero panels, modals |
| `--r-pill` | 999 px | Avatars, status pills, segmented controls |

### Breakpoints

```css
--bp-mobile-up: 480px;   /* large phone */
--bp-tablet:    768px;   /* portrait tablet */
--bp-desktop:  1024px;   /* small laptop */
--bp-wide:     1440px;   /* large laptop / desktop */
```

Sidebar appears at `≥1024px`. Bottom tab bar appears at `<1024px`.

---

## 5. Components

Defined in [`_tokens/components.css`](./\_tokens/components.css). Live in the
component library at [`_components/showcase.html`](./\_components/showcase.html).

### 5.1 Core inventory

| Class | Purpose |
|---|---|
| `.btn-primary` | Orange-filled CTA |
| `.btn-secondary` | Border-only neutral CTA |
| `.btn-ghost` | Text-only |
| `.btn-danger` | Red-filled destructive action (suspend / ban / forfeit) |
| `.btn-icon` | Square 40px icon button |
| `.card` | Surface container with `--bg-panel` |
| `.card-elevated` | Higher contrast card (e.g. wallet balance) |
| `.input`, `.select`, `.textarea` | Form fields with focus ring |
| `.label`, `.field-helper`, `.field-error` | Form helpers |
| `.badge`, `.badge--success` / `--danger` / `--warning` / `--info` / `--compliance` / `--gold` / `--silver` / `--bronze` | State chips |
| `.status-pill` | Slightly larger badge — used on dashboards |
| `.banner`, `.banner--warning` / `--danger` / `--info` | Inline page-level banners (cash-out freeze, AML hold, KYC step-up) |
| `.modal`, `.modal-backdrop` | Dialog primitives |
| `.tabs`, `.tab` | Segmented page tabs |
| `.chip-filter` | Pill filter chips with active state |
| `.table` | Dense compliance/admin table |
| `.kpi` | Stat block for compliance overview |
| `.tier-emblem--gold/silver/bronze` | Placement squares |
| `.coin-balance` | Reusable money-display block |
| `.bracket-node` | Tournament bracket cell |
| `.sponsorship-line` | Public attribution row |
| `.audit-row` | Mono-typed audit-log entry |
| `.nav-rail`, `.tab-bar` | Desktop sidebar and mobile bottom-nav |
| `.app-header` | Top header (logo + search + bell + avatar) |
| `.phone-frame`, `.desktop-frame` | Mockup chrome for the navigator |

### 5.2 Banner usage rules

The banner is the platform's most legally-loaded UI element. It carries
information the user **must** see (cash-out freeze, AML hold, KYC step-up,
sanctions review). Rules:

- Always sits above the main page content, never inside a card.
- Always carries an actionable secondary link ("Contact support",
  "Verify your identity", "View hold details").
- Never dismissable on a regulatory state. Players cannot wave away a
  freeze.

### 5.3 The "Rates illustrative" footnote

```html
<p class="footnote-rates">
  Rates illustrative — pending SP8-ECON economics review.
</p>
```

Appears on every screen that shows a fee or commission percentage.

---

## 6. Iconography

- Stroke-style line icons throughout (Lucide weight ~1.5).
- 20-px square unless inside a `.btn-icon` where they go 24.
- Inline SVG — no icon font, no emoji-as-icon (slop).
- One icon per navigation row max. **No decorative icons** next to body
  paragraphs.
- Reserved icon meanings:
  - 🛡 shield → compliance / KYC
  - ⚡ lightning → AML flag (urgent)
  - ⏳ hourglass → prize hold / pending state
  - 🔒 lock → frozen / suspended
  - 🏆 trophy → tournament prize
  - ⚔ swords → 1v1 challenge
  - 📜 scroll → audit / policy

(Listed here as Unicode shorthand for the inventory. In production they
are inline SVG paths — see `components.css` for the spritesheet
embeds.)

---

## 7. Mockup chrome

Every screen page in `docs/design/<actor>/*.html` is **a single responsive
HTML file**. The actor-level `index.html` (and the top-level navigator)
embeds each screen twice via iframes:

- a **desktop frame** at 1440 × ~viewport
- an **iPhone 15 Pro frame** at 393 × 852 (with bezel and Dynamic Island)

The screen file itself contains no chrome — chrome is applied by the
parent navigator. This lets the team lift each `*.html` directly into the
production app without stripping mockup furniture.

---

## 8. What this prototype does not do

- No backend integration, no auth, no real Stripe/Paystack/Didit calls.
- No JavaScript beyond simple tab switches and modal demos. The
  state-machine views show one variant per file (e.g. `wallet.html` =
  default, `wallet-frozen.html` = cash-out-frozen variant) so engineers
  can see the structural diff.
- No marketing animation, no scroll-triggered effects. This is a
  product, not a campaign.

---

## 9. Provenance

- Colour palette extracted from the four reference screenshots in
  `/app-screenshots/` (April 2026).
- Wordmark "BOADMAN" reinterpreted in Space Grotesk 700 with tight
  tracking — close to but not a literal trace of the reference logo.
- All copy is original, drawn verbatim from `docs/features/glossary.md`.
- Every flow's UI maps to at least one screen, per
  `docs/features/flows.md` and `docs/features/screen-inventory.md`.
