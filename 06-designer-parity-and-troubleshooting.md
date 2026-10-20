# 06 — Designer Parity and Troubleshooting

A Webflow build lives in the Designer. A page that is right when published but broken on the canvas
will be "fixed" by the next person who opens it — and then be broken for real. This chapter makes
**Designer parity an acceptance criterion** and gives a repeatable procedure for the cases where the
environments disagree.

It generalizes lessons from three later builds (2026-09/10): a multi-page site built from a coded
prototype, and an isolated landing page built inside a large existing company site, plus the template
build this playbook started from. Project names, IDs and URLs are deliberately omitted.

## 1. Four environments, not one

| Environment | What renders it | Runs page scripts? | What it proves |
|---|---|---|---|
| **Figma** (the reference) | Figma | — | Intent: structure, hierarchy, geometry |
| **Designer canvas** | Webflow's editor, from its stored style/element model | **no** | That the build is maintainable by the next editor |
| **Preview** | Designer preview mode | interactions, typically | Interaction behaviour before publishing |
| **Published** (staging / production) | Browser, Webflow-compiled HTML/CSS | yes | What ships |

They differ for structural reasons:
- The canvas lays out from Webflow's **stored representation**, which for a few CSS forms is drawn
  differently from what a browser does with the compiled CSS (below).
- The canvas runs **no page scripts**, so anything a script builds, measures or classes at runtime is
  absent there.
- Style writes made through the API are not always re-laid out on an open canvas until a **reload**
  ([03 · MCP working rules](03-implementation-workflow.md#step-8--build)).
- Published CSS may be **optimised per page** on some sites: classes not applied to any canvas element
  have been stripped from the page stylesheet, so script-only classes shipped unstyled. Other sites ship
  one shared stylesheet and don't strip. Check the `<link rel="stylesheet">` tags of a published page.
- The "Desktop" canvas on a laptop is narrower than a 1440px browser; `vw`-based values rewrap. Compare
  at equal widths before calling it a defect.

## 2. Designer-safe authoring rules

1. **Every grid declares its columns.** Wherever `display: grid` is in effect — base, or the breakpoint
   where a class becomes a grid — set `grid-template-columns`. For a single-column stack, `auto`
   reproduces the browser's implicit column exactly (while `grid-auto-columns` / `grid-auto-flow` are
   unset); `minmax(0, 1fr)` additionally prevents min-content blowout but re-check the render, and avoid
   it on absolutely positioned single-track grids (seen collapsing to the padding width). On combos,
   check whether the parent already sets a template.
2. **No `repeat(auto-fill | auto-fit, …)`.** Use explicit column counts per breakpoint.
3. **The resting state is the finished composition.** With scripts off and embed CSS ignored, every
   component shows its default readable state: first tab and panel active, accordion in its default
   state, counters at their final value. Hidden pre-animation states are applied only after a script
   marks the page ready, with a fail-safe timeout.
4. **Script-set states are attributes or ARIA** (`aria-expanded`, `data-state`), styled by attribute
   selectors in the owning component's embed. If a state must be a Webflow class, author it on a canvas
   element so it exists in compiled CSS.
5. **No layout that needs JavaScript to be correct.** Scripts enhance; they don't compose.
6. **Class placement over element placement** for grid children — Designer per-element placement emits
   ID-level rules that class overrides cannot beat ([03 · Step 9](03-implementation-workflow.md#step-9--responsive-implementation)).
7. **Repeating script UI is cloned from a canvas template node**, never fabricated with
   `createElement` + class names (see the per-page CSS point above).

## 3. Detection

**Before writing:** lint the CSS you're about to send for grid rules without templates and for
auto-repeat tracks (a short script over your source or a `get_styles` export is enough — count
`display:grid` vs `grid-template-columns` per class and breakpoint).

**After publishing:** scan the compiled CSS for the same patterns — builders and later edits can change
stored values even when the source was clean.

**Canvas emulation** (when the Designer isn't open, and as a deterministic regression check): load the
published page with JavaScript disabled and every `.w-embed style` element removed; capture at the
canvas-equivalent width; compare to the normal capture. It is a conservative proxy — report it as
"emulated", not "checked in the Designer".

**On the canvas:** reload first; snapshot each section (element snapshots need the Designer open on the
site and may fail on very large pages — snapshot a section instead); compare with a known-good older
section as a control; check tablet and mobile by hand if the snapshot tool can't switch breakpoints,
and list that manual step.

## 4. Decision tree — the environments disagree

Always lay out **three states side by side: reference, Designer, published.** Two-way comparisons
mislead.

```
Published ≠ Figma (beyond intended adaptation)?
├─ yes → build/compile problem (see §6 playbooks): value truncated or collapsed, class dropped by a builder,
│        wrong breakpoint, ID placement, conflicting smaller-breakpoint rule, per-page CSS stripped a class,
│        cache (wait ~1 min after publish and cache-bust), lazy images / frozen animations in captures.
└─ no → Designer ≠ Published?
        ├─ no  → done (check Preview too if interactions matter).
        └─ yes → reload the Designer.
             ├─ fixed → stale canvas. Not a defect.
             └─ still wrong →
                  doubled columns / side-by-side items / nested blocks halving → template-less grid or auto-fill (rules 1–2)
                  all tab panels stacked / piled positioned items / empty stage → state lives in script or embed CSS (rules 3–4)
                  narrower page, different wraps → canvas width vs vw values; compare at equal width
                  placeholder boxes, extra gaps → empty elements get placeholder size on canvas only
                  variant looks like base → variant override scope/specificity (§6)
                  anything else → probe element on a draft page: create → reload → snapshot → bisect properties
```

## 5. Case study — template-less grids (two independent builds, 2026-10)

- **Symptom.** In the Designer: overlapping text, labels beside their values, swatches beside captions,
  nested blocks squeezed. Preview and the published site were correct.
- **Root cause.** A class with `display: grid` and no `grid-template-columns`: the browser creates one
  implicit column (standard CSS), but the canvas drew Webflow's default two-column grid; nested
  template-less grids halved the width at each level. `repeat(auto-fill, minmax(…))` wasn't represented
  either. One build had about 60 such rules, the other several hundred across 21 pages.
- **What failed first.** Live template edits appeared to do nothing — the canvas didn't re-layout until
  reload — and probe elements looked right until the reload. Ruled out with evidence: embed CSS,
  script-built layout, script-added classes, variable modes, viewport detection, element placement.
- **Validated fix.** An explicit template on every grid and explicit per-breakpoint counts instead of
  auto-fill. Published layout unchanged (no height change; pixel differences at noise level); canvas
  correct after reload.
- **Prevention.** A build-time lint ("every grid declares columns; no auto-fill/auto-fit") and a compiled
  CSS scan after each push. Later batches reported zero implicit grids.
- **Not verified.** Webflow doesn't document its canvas grid model — the rule is derived from observed
  behaviour. Tablet/mobile canvas states were checked by hand or inferred from the same mechanism.

## 6. Troubleshooting playbooks

Each: symptom → diagnosis → safe recovery → prevention.

**Tools missing or failing**
- Connector authorised but its tools absent in this session → the client didn't load them; load/enable
  them or work without tooling. Never plan writes on assumed tools.
- Designer tools fail, data tools work → no Designer tab open on the site, or the companion app
  disconnected after a Designer reload. Reconnect; data-layer work can continue.
- 403 on a plan feature (page branching, redirects via API) → final for that site; plan an owner step.
- Prevention: the capability check in [01](01-prerequisites.md#webflow-mcp--detect-capabilities-dont-assume-them).

**A write "succeeded" but nothing changed**
- Same style twice in one batch (race), long or comma-bearing value truncated, `calc(… * n)` inside a
  gradient truncated, compound value mixing `var()` with other tokens collapsed to the bare variable,
  shorthand stored wrongly.
- Recovery: one style per call, longhands, precomputed values; confirm with a full-breakpoint read and
  then the compiled CSS.

**A batch timed out**
- Several timed-out batches had applied in full; blind retries created duplicate classes and instances.
- Recovery: query every style/container the batch touched, then send only what's missing.

**"Already exists" inside a batch**
- Later actions in the same batch still ran — on a shared site they can modify someone else's style.
- Recovery: inspect that style at once; restore from a snapshot if it wasn't yours. Prevention: only touch
  names you own; check existence first.

**Class missing from an element**
- Builders drop classes that don't exist yet, and stacked classes need their combo chain (`a.b`) to exist
  first. Recovery: create the chain, re-apply, read the class echo.

**Breakpoint override does nothing**
- ID-level placement outranks it; a smaller-breakpoint rule conflicts; written on `small` instead of
  `medium`; or a more specific combo beats a variant override (variant styles have compiled as
  `.class:where(.w-variant-…)`, i.e. base-class specificity).

**Component variant issues**
- Properties seem missing in API reads → they live on a variant. Newer tool versions can read variant
  styles; otherwise read computed styles on the published page and spot variants by `w-variant-…`
  classes in HTML.
- A local simulation differs from published → Webflow adds `w-variant-…` only to elements whose styles
  carry variant data, on publish. Add it in the simulation, or validate on staging.
- Obsolete styling still applies → a combo name kept under a longer chain keeps the old chain alive;
  empty obsolete chains (deletion may be refused).

**Markup transfer defects** (verify in published HTML)
- Literal `##INLINE<n>##` text → an inline element containing only inline elements went through the
  HTML builder. Rebuild that node with the element builder.
- Missing spaces → leading/trailing whitespace in inline text trimmed.
- Buttons became links; `hidden`, empty boolean attributes, `target` dropped → set them after insertion
  or change the approach.
- Forms: let the element builder create the `<form>`; field names `name` / `title` published as
  `field`; label `for` must be set at creation; form name is a Designer step.

**Publishing surprises**
- Publishing is often site-wide: it ships every staged change by anyone, and others may publish at any
  time. Recovery: report the diff, don't "fix" others' content. Prevention: §7.

## 7. Publish safety (summary)

- Fresh pending-changes check before **every** publish; unrelated changes by others → stop and report.
- Publish to staging first; diff every route's HTML and the CSS rule set against production; only
  intended changes may differ.
- Explicit approval per production publish — approvals don't carry over.
- After publish: production equals the diffed staging build; re-run the page checks.
- Rollback without disturbing others: hide replaced blocks instead of deleting, keep embed versions in
  the repo, keep before-captures.
