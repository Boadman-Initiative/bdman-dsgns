# Backend Gaps · Design-driven engineering roadmap

> **Read order:** open the design, find the screens, then come here to see
> what the design implies for backend that doesn't exist yet. Each entry
> names the surface, the gap, and a minimum-viable scope.
>
> **Last updated:** 2026-05-21 · v2 design freeze for stakeholder share.
>
> **Process going forward.** Each feature gets a subagent investigation
> against `boadman-api/` to compare the design against what's actually
> wired. The outputs of that work refine this register from "design
> implies" to "diff vs. current code."

Gaps are categorised:

- **NEW** — endpoint / table / job / field doesn't exist; needs to be added.
- **REFACTOR** — exists but the contract doesn't match what the design needs.
- **RESTRUCTURE** — current modelling is wrong (not just incomplete) and needs reworking.
- **CONFIG** — exists but values need to change or be made configurable.

Severity:

- 🔴 **Blocker** — design can't ship until this lands.
- 🟠 **Major** — design ships degraded without this.
- 🟡 **Minor** — nice-to-have; can defer.

---

## A. Tournament — single-match free-for-all

The tournament design has moved away from brackets entirely. Every tournament
is now one match — host configures the winner count (top 1 / top 2 / top 3),
the prize splits per slot, and after the match the host records winners
from the roster.

### A.1 🔴 NEW · Tournament cover image upload

- **Surface.** Tournament create form, tournament detail hero.
- **Gap.** No field exists on `tournaments` for a cover image; no upload
  pipeline. Currently the design uses a real game banner image, but that's
  game-level art, not tournament-level art.
- **Minimum-viable scope.** Add `cover_image_url` (text, nullable) to
  `tournaments`. Add `POST /tournaments/:id/cover` (host-only) that
  uploads a multipart image to object storage (≤ 5 MB, JPEG/PNG/WebP),
  validates dimensions ≥ 1200 × 600, stores the URL on the row.
  Default for hosts who don't upload: the game's stock banner.

### A.2 🔴 NEW · Configurable winner count + per-slot distribution

- **Surface.** Tournament create form (host sets `winner_count` 1–N), tournament
  detail (prize block in Open / Live states), record-winners UI in Awaiting.
- **Gap.** Current backend assumes fixed top-3. The design lets the host
  pick 1, 2, 3 (and conceptually more) winners and set the % per placement.
- **Minimum-viable scope.** Add to `tournaments`: `winner_count` (int, 1–8),
  `prize_distribution` (jsonb `[{position:1, pct:60}, ...]`). Validate
  in create that `len(distribution) == winner_count` and `sum(pct) == 100`.

### A.3 🔴 NEW · `awaiting_result` tournament status

- **Surface.** Tournament detail — between Live and Completed.
- **Gap.** Current `tournaments.status` enum has no `awaiting_result`
  value. Host abandonment job and the 7-day timer hang off this state.
- **Minimum-viable scope.** Extend enum to include `awaiting_result`.
  Transition `live → awaiting_result` happens when the host clicks
  "Match concluded" OR when a derived end-time passes. From there the
  host has 7 days to record winners (existing host-abandonment rule).

### A.4 🔴 NEW · Host records winners endpoint

- **Surface.** Awaiting-result state, host's "Record winners" panel.
- **Gap.** No endpoint to submit a winner list with placements. Current
  prize-release code assumes a bracket with winners produced by match
  resolution.
- **Minimum-viable scope.** `POST /tournaments/:id/record-winners`
  (host-only) takes
  `[{placement:1, profile_id, evidence_url?}, {placement:2, ...}]`,
  validates winners are confirmed participants, length matches
  `winner_count`, sets tournament to `prize_releasing`, queues
  prize-distribution worker. Existing 48-h hold + complaint rules apply.

### A.5 🔴 NEW · External-game join codes

- **Surface.** Tournament detail Live state — "Join the external game" panel
  (lobby code, host EA/Activision/PSN ID, Discord link, instructions).
- **Gap.** Boadman doesn't host games — players play the actual game (Apex,
  CoD, Cyberpunk) and Boadman just records results. There's no field on
  tournaments for the host's external lobby details, and no UI to surface
  them to confirmed players.
- **Minimum-viable scope.** Add to `tournaments`:
  `external_lobby_code` (text), `external_host_id` (text — host's gamer-tag
  on the platform), `external_voice_link` (URL — Discord etc.),
  `external_host_instructions` (text). Add `POST /tournaments/:id/external-details`
  (host-only). Surface to roster participants whose status is `confirmed`.

### A.6 🟠 NEW · Roster participant status enum

- **Surface.** Tournament Live state — roster shows
  `Confirmed / Invited / No-show / Did not show`.
- **Gap.** Current `tournament_participants` rows have a boolean
  `checked_in_at` only. The design needs richer states because the host is
  the source of truth about who actually joined the external game.
- **Minimum-viable scope.** Add `status` enum to `tournament_participants`:
  `registered → checked_in → confirmed_by_host → did_not_show / did_not_finish`.
  Add `POST /tournaments/:id/participants/:profile_id/confirm` (host-only)
  and `…/mark-no-show`.

### A.7 🟠 NEW · Host stream-link field

- **Surface.** Tournament detail Live state — "Watch on Twitch" CTA only
  appears when the host attached a stream link.
- **Gap.** No field for it. Currently the "submit host evidence" path is
  meant for post-match VODs.
- **Minimum-viable scope.** `tournaments.stream_url` (text, nullable). Add
  to existing `PATCH /tournaments/:id` host-only route. UI shows the CTA
  only when populated.

---

## B. Match / 1v1 challenges

Carried over from existing screen-inventory.md — no significant new gaps
beyond what's already in `boadman-api/docs/followups.md`.

---

## C. Wallet, deposits, redemptions

### C.1 🟡 NEW · Wallet "Vault ID" + sync state

- **Surface.** Wallet hero — `VAULT · A-247-J` serial number, "last sync"
  pulse, live spendable preview.
- **Gap.** The vault ID is purely decorative in the design — it could be
  derived from `profiles.id` slug or `wallets.id`. But the design implies
  a stable, user-visible reference for support conversations.
- **Minimum-viable scope.** Either derive client-side from a short hash of
  `wallet.id` (zero backend change) or add `wallets.public_ref` (short
  hash, generated on create). User decision; suggest the former.

### C.2 🟠 NEW · Local-currency conversion display

- **Surface.** Wallet shows "£1,240.00 GBP · ₦2,448,000 NGN" based on
  player country.
- **Gap.** The platform-config FX-rate refresh worker exists; the wallet
  read endpoint doesn't surface the NGN equivalent.
- **Minimum-viable scope.** Wallet GET response includes
  `gbp_equivalent`, `local_equivalent` (where applicable, derived from
  player country + FX rate cache).

---

## D. Notifications

### D.1 🟠 NEW · In-app notification storage

- **Surface.** Bell icon dropdown + dedicated notifications inbox.
- **Gap.** No `notifications` table; today the platform sends emails for
  the regulatory subset but doesn't persist in-app messages.
- **Minimum-viable scope.** Add `notifications` table
  (`profile_id`, `kind`, `body_md`, `deep_link`, `created_at`, `read_at`).
  Every audit/state event that the channel matrix marks in-app inserts a
  row. `GET /notifications`, `PATCH /notifications/:id/read`,
  `POST /notifications/mark-all-read`.

---

## E. Identity & KYC

### E.1 🟡 NEW · KYC re-submission window UI

- **Surface.** KYC outcome screen — "Resubmit" CTA when rejected.
- **Gap.** Endpoint exists (`POST /kyc/submit` is idempotent on a per-user
  basis). Just need the UI surface; no backend change.
- **Status.** UI gap only — no backend work.

### E.2 🟠 NEW · Rectification queue link UI

- **Surface.** Profile settings — "Request rectification" CTA on locked fields.
- **Gap.** Endpoint exists. UI needs to route from profile-edit attempts on
  locked fields into the rectification form.

---

## F. Brand actor

### F.1 🟠 NEW · Brand cover & avatar upload

- **Surface.** Brand dashboard hero — brand has a logo and cover banner.
- **Gap.** No fields. Upload pipeline needs to mirror tournament cover (A.1).
- **Minimum-viable scope.** `brands.logo_url`, `brands.cover_url`,
  `POST /brands/:id/logo`, `POST /brands/:id/cover`.

### F.2 🟢 (covered in future-implementation.md) — additional Brand Managers
### F.3 🟢 (covered in future-implementation.md) — Admin-created games

---

## G. Public discovery

### G.1 🟠 NEW · Tournaments hub list endpoint

- **Surface.** Tournaments hub (`/tournaments`) — filterable list of open,
  in-progress, and recently completed tournaments.
- **Gap.** Likely partially exists for player-hosted list, but the design
  expects richer filtering: by game, host type (player/brand), entry-fee
  range, "joinable now", sort order.
- **Minimum-viable scope.** `GET /tournaments` query params
  `?game=&host_type=&entry_max=&joinable=true&sort=`. Pagination via
  cursor. Each item returns hero card data (cover, title, host, entry,
  prize-pool, players-of-cap, status).

---

## H. Sponsorships

### H.1 🟡 RESTRUCTURE · Sponsorship attribution payload

- **Surface.** Public sponsorships block — funder avatar, public message,
  amount, brand-vs-player marker.
- **Gap.** Schema likely tracks the amount and funder ID. Design expects
  `public_message` (text, ≤ 140 chars) and `is_brand` derived.

---

## I. Cross-cutting

### I.1 🟠 RESTRUCTURE · Status-banner contract

- **Surface.** Cash-out freeze banner, KYC step-up banner, AML hold banner,
  policy re-consent.
- **Gap.** Each surface independently composes a banner from various
  flags. The design wants a single banner-state aggregator the client can
  read once.
- **Minimum-viable scope.** `GET /me/banners` returns
  `[{kind, severity, title, body, action_link}]`, computed server-side from
  all relevant flags (cash_out_frozen, kyc_required, aml_review,
  policy_outdated, edd_paused). Client renders banners in order.

---

## J. Decisions still open

These need a product-side call before backend can quote:

- **J.1.** Should tournaments support more than 3 winners? Design allows
  `winner_count` 1–8; if business says "max 3 always" we save effort.
- **J.2.** Should the Vault ID be user-visible (support reference) or
  purely decorative (UI flair)?
- **J.3.** Stream link — Twitch only? Or any URL the host pastes?
  Validation matters because the CTA reads "Watch on Twitch" if Twitch URL.

---

## K. Subagent investigation backlog

For each surface area below, dispatch a `general-purpose` subagent into
`boadman-api/` to diff what's wired against this register. Update entries
to `WIRED`, `PARTIAL`, or `MATCHES_DESIGN` once findings come back.

- [ ] A. Tournament single-match model
- [ ] B. Match / 1v1 challenges
- [ ] C. Wallet + redemptions
- [ ] D. Notifications
- [ ] E. KYC
- [ ] F. Brand actor
- [ ] G. Public discovery
- [ ] H. Sponsorships
- [ ] I. Cross-cutting banner contract
