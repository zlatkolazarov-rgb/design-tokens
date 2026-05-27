# Changelog — 2026-05-27 — Exceptions layer refactor

Token system: **v3.2** · Figma file: `fysBbW10LRJUNgisaBKXJo`

## Summary

Reworked the **Exceptions** collection (`46:6`, the override layer the
architecture decks call "Component Packs / CP") to function as intended and
trimmed it from **48 → 27 tokens**, with **zero visual change**.

Intended architecture confirmed and enforced:

```
Exceptions → Semantic → Theme → Modes
```

Figma has no automatic cross-collection cascade, so "Exceptions overrides
Semantic" means: a component binds to an Exceptions token only when it
genuinely needs to diverge from Semantic; otherwise the token (or the
component) should resolve through Semantic directly. Exceptions is now kept
**lean** — pass-through tokens were moved down into Semantic.

## 1. Audit

Exceptions held 48 pure-alias tokens (no divergent values — the "override" was
illusory). Target-layer split: 8 → Semantic, 19 → Theme (same-name identity
aliases), 21 → Modes. Reference load ≈ 11,552 component bindings
(Brands 8,954 · Test 1,320 · Components 1,278; all other pages 0).

## 2. Re-pointed 13 tokens to alias their Semantic counterpart

Value-safe (terminal-equal in Theme Light + Dark, which propagates to all 22
brand modes). Alias-target change only, no component touched:

`header/{surface-primary, on-surface-primary, active-selected}`,
`nav/{default-icon, default-text, selected-icon, selected-text, background, radius}`,
`cta/{form, card, wallet} radius`, `surface/raised-light`.

## 3. Deleted 9 zero-reference tokens

Confirmed zero usage across all binding types (fills, strokes, effects,
text-range fills, node-level) on every page, then removed:
`cta/primary/surface-hover`, `cta/{form,card,wallet}/radius`,
`nav/default-text`, `view-all/{label, label-hover, icon, icon-hover}`.

## 4. Moved 12 inherited tokens into Semantic

Rebound their component consumers (~1,400 bindings: Components 100, Test 159,
Brands 1,304) from the Exceptions token directly to the Semantic token, then
deleted the Exceptions token. Value-identical by construction; verified **zero
dangling references** before each deletion.

Moved: `cta/primary/{surface, on-surface, surface-disabled}`,
`header/{surface-primary, on-surface-primary, active-selected}`,
`nav/{default-icon, selected-icon, selected-text, background, radius}`,
`surface/raised-light`.

## 5. Verification

- Terminal equality checked in Theme Light **and** Dark for every re-point.
- Full all-binding-type re-scan confirmed 0 remaining references before each
  delete.
- Rendered ComeOn and Hajper brand frames — headers, nav, CTAs, active states,
  landing blocks all correct. No visual shift.

## Live collection state (post-refactor)

| Collection | Vars | Modes |
|---|---|---|
| Global | 135 | 1 |
| Modes | 124 | 22 |
| Theme | 39 | 2 |
| Semantic | 97 | 1 |
| Regulatory | 4 | 7 |
| **Exceptions** | **27** | **1** |

## Exceptions now holds 27 — all genuine (no Semantic equivalent exists)

- Granular header (11): `background`, `cta`, `ios-bar`, `on-cta`,
  `on-surface-1`, `on-surface-2`, `on-surface-secondary`, `surface-1`,
  `surface-2`, `surface-secondary`, `text`
- landing (9): `icon-cta-bg`, `icon-cta-fg`, `primary-cta-bg`,
  `primary-cta-text`, `secondary-cta-bg`, `secondary-cta-text`, `tag-bg`,
  `tag-text`, `text`
- menu-row (3): `on-selected`, `surface`, `surface-selected`
- Other (4): `nav/shadow`, `generic/ios-bar`, `text/on-raised-light`,
  `cta/landing/radius` (brand-DNA)

## 6. Action-link color fix + new `color/text/link` token

Carry-over from the prior session: during the component rewire, legacy
`Main/Text-Primary` (a blue link colour) was collapsed onto v3.2
`color/text/primary` (body-text near-black), so action-links rendered black.

**Root-cause nuance found via reference cross-check** (`DiM6F6cVhVzV5ato0ZQE44`):
link-named slots are heterogeneous — `link`/`CTA`/`Read More` were blue
`#0066ff`; `View all` was neutral gray `#98989c`; lowercase `cta` was white
button labels `#f8f9fd`; `Text Link Right` was mixed blue/black by context. A
blanket rebind would have broken the gray/white/black cases.

**AA problem with the obvious fix:** `color/text/brand` is a per-brand accent
and fails WCAG AA (<4.5:1) as link text on 16/22 brands. So a dedicated token
was created instead.

**New token — `color/text/link`:**
- Modes `color/text/link` (`6607:61813`) — 22 brand modes, each aliasing the
  *lightest* `color/primitives/primary/{500..900}` stop that clears 4.5:1
  against that brand's page **and** bottom-sheet surface.
- Semantic `color/text/link` (`6607:61814`) — aliases the Modes token.
- Result: **all 22 brands pass AA** (lowest 4.62; most 5–13:1).

**Rebound 36 action-links** to `color/text/link` (reference-confirmed blue):
`link` (4), `CTA` (6), `Read More` (2), all `Text Link Right` (23, headers +
search-bar Cancel, all blue contexts), `View all` Originals (1). Per-instance
only — the shared Text Link Right master was left alone so black "Login"
variants (In-game/Lobby/Germany) stay black. Verified 0 dangling refs;
screenshots confirm blue links on light headers, untouched white labels on
colored headers.

**Held 3 — RESOLVED 2026-05-27 (follow-up):**
- `View all` Desktop/Casino Grid 6 (`I4050:55612;27172:190617`) — was the lone
  outlier on `color/text/primary` (#1c1d20 black); all 26 sibling "View all"
  buttons + the reference use gray. Rebound `color/text/primary` →
  `color/text/secondary` (`50:8`). Consistency repair, not a link.
- 2× `cta` "Change Method" (`4050:63287` Active, `4050:63306` Open) — their
  disclosure chevron was already `color/icon/action` (#0066ff blue) while the
  label was black `color/text/primary`. Rebound label → `color/text/link`
  (`6607:61814`) so the control reads as one tappable affordance (icon/action ↔
  text/link parallel). AA-safe per brand on bottomsheet. Verified visually
  (blue label + blue chevron on white QD sheet).

Collection counts after this: Semantic 97→98, Modes 124→125 (the two new link
tokens).

## Follow-ups

- **Bucket B (post-delivery):** moving the remaining granular header / landing /
  menu-row tokens out requires **expanding Semantic** with those roles first,
  then alias + rebind through them (same workflow). Start with `menu-row/*`.
- **Doc naming drift — RESOLVED 2026-05-27:** the v3.2 architecture deck,
  reference deck, and `index.html` were renamed from "Component Packs / CP" to
  "Exceptions" to match the live file. Naming-only pass — the `38 vars` figures
  in those decks were left at their 2026-04-29 snapshot value (the decks are
  dated point-in-time artifacts; live count is now 27).
