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

## Follow-ups

- **Bucket B (post-delivery):** moving the remaining granular header / landing /
  menu-row tokens out requires **expanding Semantic** with those roles first,
  then alias + rebind through them (same workflow). Start with `menu-row/*`.
- **Doc naming drift:** the architecture/reference v3.2 HTML decks still call
  this collection "Component Packs / CP"; the live file names it "Exceptions".
  Reconcile naming in those decks in a later pass.
