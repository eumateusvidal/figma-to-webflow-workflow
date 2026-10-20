# Pre-Publish Checklist — [page / change] — [date, time, timezone]

> One checklist per publish. An approval covers this publish only.

## 1. Scope
- Intended change: 
- Publish target: ☐ staging only ☐ production (needs explicit OK below)
- Publish scope on this plan: ☐ single page ☐ whole site (ships everyone's staged changes)

## 2. Pending-changes preflight (fresh, not copied)
| Page / CMS item | Changed by | When | Intended? |
|---|---|---|---|

☐ Nothing unrelated is pending, or the owner was told about it and decided.

## 3. Staging diff vs production
- Routes diffed: __ / __ · CSS rules changed: __ (all intended? ☐)
- Unexpected differences: 

## 4. Checks on staging (evidence level: published compiled output / measured)
☐ Widths 1440 · 1280 · 991 · 768 · 767 · 480 · 390 · 360: no overflow; content blocks at intended widths
☐ Designer parity: no template-less grids or auto-fill in compiled CSS; resting states native
☐ Keyboard path and visible focus · accessibility scan 0 violations
☐ Forms: submissions intercepted; field names and hidden fields correct; no real submissions
☐ Head code, canonical, OG, robots/noindex, sitemap state as intended
☐ Embeds byte-identical to the repo source

## 5. Rollback path
- How to undo (hidden blocks, embed source files, style snapshot): 

## 6. Approval and result
- Owner OK for this production publish: [quote, time]
- Published at: · Production matches the diffed staging build: ☐ · Post-publish checks: ☐
- Owner steps still pending in the Designer: 
