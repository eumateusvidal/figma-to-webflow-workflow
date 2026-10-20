# 04 — Validation and Learning (Steps 10–11)

## Step 10 — Visual validation

The loop: **publish to staging → measure the real page → diff against Figma's numbers → fix root causes → re-measure → check the rest of the site.** Every fix is re-verified; nothing is assumed to have shipped.

**Measure, don't eyeball.** Both sides are numeric: Figma geometry from `get_metadata`; page geometry from the driven browser at explicit viewport sizes (`getBoundingClientRect`, computed styles). A useful finding looks like *"video renders at 0.70 aspect; design intends 1.27"* — diagnosable, fixable, re-checkable.

> ### ⚠ `scrollWidth === clientWidth` is a smoke test, NOT a responsive audit
> An earlier revision of this document called it "the single best health check." **That was wrong, and this project proved it three separate times.** A page can pass it perfectly while being structurally shredded, because **a CSS Grid that stays fully active at 390px divides the *available* width** — its children get absurdly narrow and nothing ever crosses the viewport edge.
>
> | Page | Reported | Actually rendering at 390px |
> |---|---|---|
> | Listing page | ✅ 390/390 | Grid never collapsed: cards **69–100px** wide, hero image **225×106** |
> | Team page | ✅ (after an overflow fix) | Portraits at **77×77px** inside a still-active 12-column grid |
> | Contact page | ✅ 390/390 | Hero `h1` **214px wide and still right-aligned**; three wrappers at **61px** |
>
> **Audit element geometry instead:** `getBoundingClientRect` on the real content blocks — cards, images, headings, text wrappers — and check they occupy the width you intended. Then, separately, assert that *no element* overflows:
> ```js
> [...document.querySelectorAll('body *')]
>   .filter(n => { const r = n.getBoundingClientRect(); return r.right > innerWidth + 1 || r.left < -1; })
> ```
> An empty result is meaningful; a matching `scrollWidth` on its own is not. (Filter out elements inside an `overflow:hidden` ancestor — intentional marquees and sliders will show up otherwise.)
>
> **Test the tablet band too, not just phone widths.** Two real overflows on this project lived at **768px** and were invisible at 390. Sweep 991 / 768 / 600 / 480 / 390 / 360.

**Verify writes from the compiled CSS.** After publishing, download the page's stylesheet and confirm the rules actually shipped — the MCP can report success on writes that were clobbered (see the batch-race rule in [03](03-implementation-workflow.md)). Note: Webflow's optimizer legally re-collapses longhands into shorthands in the compiled output; judge writes by reading the style back through the API, not by the compiled file's shape.

**Root causes, not symptoms.** Keep asking "why" until the answer is a mechanism. Real chains from this project:

| Symptom | Root cause actually fixed |
|---|---|
| Two cards narrower than siblings | Shrink-to-fit link wrapper + image lacking `width:100%` — the *wrapper chain*, not the image |
| Quote floating at a strange offset | Its grid computed twelve 0px columns — children placed for 12 columns inside a reflowed container |
| Headings overflowing at ~900px on every page | One `white-space: nowrap` on a shared component — one fix, seven pages |

**Two traps that produce FALSE bugs** (both did, on this project):
1. **Frozen animations.** Scroll-triggered text reveals (GSAP SplitText etc.) render mid-animation in automated browsers — headings look clipped or missing while the element itself measures perfectly. Check computed transform/opacity/height before reporting; final judgment on animated elements needs a real browser. Force-completing tweens perturbs layout — also not trustworthy.
2. **Lazy-loaded images** measure 0×0 or at natural size until scrolled into view. `scrollTo` the section, then measure.

Also: pass explicit width/height to the browser resize (presets can silently no-op) and assert `clientWidth` ≠ 0 before trusting anything.

**Report the evidence level of every claim.** From weakest to strongest:

| Level | Evidence | What it proves |
|---|---|---|
| 1 | Tool echo (`success`) | The request was accepted |
| 2 | API read-back | Something was stored. Lossy: some values are omitted or normalised (`0` → `0px`) even though they compile |
| 3 | **Local simulation**: a local build, or the live page with stored rules injected | A useful gate before staging. **It is not Webflow output** |
| 4 | Published compiled HTML/CSS on staging | What Webflow generated |
| 5 | Measured rendering of the published page at explicit widths | What users get |
| 6 | Designer canvas after a reload, or its emulation (labelled as such) | That the next editor sees the same thing ([06](06-designer-parity-and-troubleshooting.md)) |
| 7 | Real devices and assistive technology | The rest |

A simulation can miss what Webflow adds on publish. For example, `w-variant-…` classes appear only on elements whose styles carry component-variant data, and some sites strip script-only classes from per-page CSS. So the status line reads "simulated locally; staging pending", never "verified".

**Regression scope:** overflow-check every page of the site at mobile width; re-measure desktop on the changed page against known-good positions; call out any intentionally site-wide change explicitly. End every session by telling the human exactly what needs real eyes (typically: animations, and a scroll on an actual phone).

## Publishing safely

On many plans a publish is **site-wide**. It ships every staged change by anyone, and a non-draft page goes live at anyone's next publish. On a shared company site, other people's publishes took unpublished work live several times without notice. Treat every publish as its own decision:

1. **Fresh preflight.** List the pages and CMS items changed since the last publish, and by whom. If anyone else has changes pending, stop and report.
2. **Staging first.** Publish to the Webflow subdomain only. Then diff every route's HTML (visible text and structure) and the full CSS rule set against production, and classify each difference. Only the intended, namespaced changes may differ. If something unexplained appears after a staging publish, republish staging and diff again.
3. **Get explicit approval for this publish.** A standing approval may cover staging; a production approval covers that one publish only.
4. **After publishing:** confirm production matches the staging build you diffed, re-run the page checks, and confirm the intended indexing state (noindex, sitemap, canonical).
5. **Keep rollback structural.** Hide replaced blocks rather than deleting them. Keep before/after copies of every embed in the repo. Keep before-captures and style snapshots, and record the rollback path before you publish.

Never publish a client's production site just to test a hypothesis. Use a sandbox site.

## Step 11 — Capture learnings into the skills

> A lesson left in a chat transcript gets paid for twice.

Before the session closes, route every finding to its one home:

| Finding | Destination |
|---|---|
| Tool behavior/limit/trap | The MCP playbook reference |
| Reusable build pattern | The skill's patterns section, labeled with where it was proven |
| Naming/architecture rule | The skill's rules sections |
| Class that now exists | The class inventory |
| Token/ID harvested | The variables skill's registry |
| Broken/deferred/needs-decision | The debt register (numbered, with status) |
| Project state + next action | The handoff — **rewritten**, facts re-verified live, never copied forward |

**Classify each finding before filing it.** The class decides both where a finding goes and how long it stays true:

| Class | Example | Shelf life |
|---|---|---|
| Universal principle | "The published page is the proof; read-backs are lossy" | Long |
| Webflow platform constraint | "The canvas draws a template-less grid as two columns" | Until Webflow changes it. Record the evidence |
| Tool-specific behaviour | "The HTML builder turns `<button>` into a link" (MCP 2.1, 2026-10) | **Date and version it.** Re-probe on upgrades |
| Conditional practice | "Namespace everything when building inside a host site" | State the condition |
| Project decision | "This site's phone layout starts at 767" | Handoff or decision log, never the skill |
| Unverified hypothesis | "Embed CSS doesn't apply on the canvas" (two builds disagreed) | Open-questions list until tested |

Prefer principles to numbers ("small sequential batches; verify per container" outlives "batches of 12"). When a rule can be checked by a script, such as a CSS lint or a scan of the published output, add the check: scripts can't be skimmed past.

**The filter — what becomes a rule:**
- *Generalizable?* Will bite future sessions → skill. Page-specific → stays in the page.
- *Validated?* Patterns enter only after working end-to-end on a real page.
- *A decision, not a fact?* → handoff, as "decided, don't silently revisit" — not a rule.
- *Already covered?* Read the existing guidance first; extend or supersede, never duplicate or contradict.

**Supersede, don't overwrite.** When new evidence contradicts a documented claim, mark the old one superseded with provenance. The history tells future sessions whether a documented limit is real or stale — including Claude's own corrected false findings, named explicitly so nobody re-fixes a phantom.

Close: update the debt register, rewrite the handoff, re-package the skills (zip + source folder stay in sync).
