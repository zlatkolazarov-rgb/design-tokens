# Changelog — 2026-05-27 (session 2) — Header badge/tag token rename + value rationalization

Token system: **v3.2** · Figma file: `fysBbW10LRJUNgisaBKXJo`

## Summary

Renamed the misnamed `header/surface-secondary` token family and rationalized its
messy per-brand colour mapping into a single **header-aware brand-accent** rule
that is WCAG-AA legible across all 22 brands. **Rename + value change only — no
tokens added or deleted; collection counts unchanged.**

## 1. Rename (in place — IDs/aliases/bindings preserved)

| Layer | Old name | New name | Var ID |
|---|---|---|---|
| Exceptions | `header/surface-secondary` | `header/notification-badge` | `51:26` |
| Exceptions | `header/on-surface-secondary` | `header/on-notification-badge` | `51:32` |
| Modes | `color/header/surface-secondary` | `color/chrome/header-badge-bg` | `77:106` |
| Modes | `color/header/on-surface-secondary` | `color/chrome/header-badge-fg` | `77:112` |

Alias chain is two links (Exceptions → Modes → primitive); **no Theme
intermediary**, no other consumers. Rename was `variable.name = …` (ID-based),
so all node bindings survive.

## 2. Legacy check (`DiM6F6cVhVzV5ato0ZQE44`)

The legacy file has **no local variables**. Its `Number Badge` component
(`13003:38997`) uses a raw fill: **bg `#98989c` (neutral mid-grey), number text
`#f8f9fd` (near-white)** — same for Large/Small. That pairing is only **~2.68:1**
(fails AA). Legacy's grey was a single-brand component default, not a per-brand
rule.

## 3. Value rule applied — header-aware brand accent

Old mapping was an inconsistent grab-bag: `primary/600` (13), `secondary/600` (5),
`system/error/400`, `system/info/400`, `tertiary/500`, `white`. 7 brands had the
badge **invisible on their own header** (badge == header colour) and 6 failed
text-vs-fill AA.

New rule, per brand mode:
- **Badge fill = the brand accent that is NOT the header colour.**
  - White / neutral / secondary-header brands → `primary/600`
  - Primary-header brands → `secondary/600`
  - **CherryCasino exception** → `secondary/700` (its `secondary/600` mid-teal
    narrowly failed text AA at 4.37)
- **Badge text = `white`, or `neutral/950`** where the fill is light/bright
  (luminance-driven AA pairing).

Result: **all 22 brands pass text-vs-fill AA, range 4.57–15.12** (0 failures).

Note on `vsHdr`: WCAG contrast is luminance-only, so hue-distinct pairs (e.g. a
teal badge on a maroon header) score low (~1–2.7) yet read fine visually. The
only *genuine* invisibility risk was same-token primary-on-primary, which the
rule eliminates.

## 4. Verification

- Re-resolved all 22 modes after writing: text-vs-fill AA 0 fails.
- Scanned CherryCasino (`6449:41843`) + Lyllo (`37:6826`) frames with exact
  `boundVariables` id matching: bindings resolve to the new values
  (`Number Badge` → `#216650`/`#ffffff` for CherryCasino; primary path for Lyllo).
  Bindings intact post-rename.

## 5. IMPORTANT finding — token is broader than "the count badge"

Per brand frame the token's consumers are **6× `Info Tag` + 3× `Notification`
ellipse + 1× `Number Badge`** (count badge). So this is really a shared **header
tag / accent** colour, *not* a dedicated notification-count-badge token. The
prior inventory's "73 bindings = count badge" conflated Info Tags + notification
dots + the count badge.

- The header-aware AA rule suits all consumers (Info Tags in a header *should*
  avoid the header colour), so the value change is sound for all of them.
- BUT the applied name `notification-badge` is arguably too narrow.
- The 3 `Notification` ellipses per brand are overridden via layered instance
  bindings (last-entry-wins) and resolve to `#af2b40` regardless — effectively
  detached from the token.

## OPEN DECISION (deferred to next session)

Naming/scope — three options on the table, user to decide:
1. **Rename broader** (recommended): e.g. Exceptions `header/tag-accent` +
   `on-tag-accent`, Modes `color/chrome/header-accent-bg` + `header-accent-fg`.
   Keep one shared token; values stay.
2. **Keep `notification-badge`** names as applied.
3. **Split** the count badge onto its own token (dedicated, ~22 instance
   rebinds) and keep the shared token for Info Tags / notifications.

Until decided, the live names are the `notification-badge` / `header-badge-bg`
set from step 1.

## Collection counts — unchanged

Global 135 · Modes 125 · Theme 40 · Semantic 98 · Regulatory 4 · Exceptions 27 = **429**.
