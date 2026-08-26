# 03 — Implementation Workflow (Steps 5–9)

## Step 5 — Analyze the existing project first

Never build into a project you haven't read. Concretely:

- Read the handoff; load both skills; probe the MCP ([01](01-prerequisites.md)).
- Read the pages/classes/components the new work will touch — via the MCP element tree and, for ground truth, the **published page and compiled CSS** (`curl` the page, follow the stylesheet link).
- **Before any responsive work: determine the page's grid-placement mechanism.** Grep the page's compiled CSS for `#w-node`. Many hits → children are element-placed (Designer-only; class overrides *cannot* beat those ID rules — the workaround is flipping the container to flex at the breakpoint, which makes child `grid-area` inert). Few/none → class-placed, freely re-flowable headlessly.
- Check the blast radius of every class you plan to touch: is it page-scoped or used site-wide? One `curl`-and-grep per class across the site's pages answers it.

## Step 6 — Analyze the Figma design

1. `get_metadata` on the frame → full node tree with exact x/y/w/h. Work from these numbers.
2. `get_screenshot` **per section node**, never the whole page frame (a full mobile frame renders as an unreadable thumbnail).
3. Derive layout arithmetically: `column = (frame width − 2×padding) ÷ 12`; round block edges to column lines. Map vertical gaps onto the spacing scale (112px → `section-padding--medium`, not `margin-top: 112px`). Resolve text styles to existing tokens; no match within rounding tolerance → **ask, don't invent**.
4. Ignore: hidden layers, placeholder/borrowed copy (flag it), fixed compositions that can't survive variable CMS counts, and literal media dimensions (a 369×291 video = "landscape ~4/3", not `height: 291px`).

## Step 7 — Map the design onto existing patterns

First question for every section: *which existing pattern is this?* — not *how do I build this?* Most sections are an existing pattern with new content. Check, in order:

1. **Existing component?** → instance + property overrides. Never rebuild nav/footer/CTA.
2. **Existing shared class set?** → reuse. A card on two pages is one class set.
3. **Variant of existing?** → `is-*` modifier combo on the base. Never fork.
4. **Genuinely new?** → project naming convention, all values token-bound, and audit for name collisions before creating.

## Step 8 — Build

Fixed sequence per section: **audit → create classes first → build elements → bind CMS/links → verify**. Classes exist before the elements that reference them.

**Prompt structure that works** — say what, constrain how, define done:

```
Implement <page/section> following the project's established patterns and skills.
Before changing anything: analyze the Figma reference, the current implementation,
and the existing responsive patterns.
Prioritize project-pattern consistency over pixel-perfect reproduction.
No isolated one-off solutions. If anything is ambiguous or needs a meaningful
assumption — stop and ask. Do not guess.
After: test the relevant breakpoints, verify no regressions, summarize.
```

**Decision boundary:** technical execution is Claude's. Anything touching scope, product behavior, shared/site-wide components, or working constraints is the human's — Claude presents options with trade-offs and waits. (On this project that boundary surfaced a standing "no custom code in the template" constraint that redirected an entire approach; assumed silently, the work would have been discarded.)

**Webflow MCP working rules** (the expensive ones — full playbook ships inside the skill):
- CSS longhand only — no `padding`/`margin`/`gap`/`grid-column` shorthands (`gap` specifically corrupts into a custom property; use `grid-column-gap`/`grid-row-gap`, flex included)
- One style per write call — two writes to the same class in one batch race and silently clobber each other; read the `breakpoints` map in every echo, not just `success`
- `set_style` replaces the element's full class list, resolves names globally, and can attach a foreign combo chain — always read the `styleNames` echo back
- Never send long comma-bearing values (gradients, data-URIs) through style writes — they truncate and can permanently corrupt that class's breakpoint store

## Step 9 — Responsive implementation

**Mobile is a recomposition, not a scale-down.** The system, in three layers:

1. **Tokens carry the scaling.** Per-breakpoint values on the variables (8rem→3.5rem etc.) make consuming classes responsive with zero overrides. Never write a class-level breakpoint override for a value a token can supply.
2. **Structural changes go on the tablet breakpoint (≤991px)** — Webflow cascades downward, so one override covers everything below. Add a phone-level refinement only when a layout genuinely needs a third step (3→2→1 card grids). Grid recomposition on this project: 12 → 6 (tablet) → 4 (mobile), expressed as proportional child widths (50%, 33.333%, 25%) with zero column-gap so the arithmetic stays exact.
3. **Media is fluid:** `width: 100%` (not just `max-width` — small source assets will otherwise render small and collapse the composition), height auto or a project-standard `aspect-ratio` (3/4, 4/3, 3/2, 16/9 — not bespoke ratios from frame pixels), wrapper crops (`overflow:hidden`) + media fills (`object-fit:cover`). **Check the ancestor chain**: a shrink-to-fit link wrapper or flex parent collapses the box before the media ever sees it — fix the wrapper, not the image.

Reordering for the mobile composition uses CSS `order` on modifier combos — DOM moves inside animated components are a last resort (they risk the interaction code).
