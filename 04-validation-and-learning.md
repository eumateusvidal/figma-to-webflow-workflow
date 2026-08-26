# 04 — Validation and Learning (Steps 10–11)

## Step 10 — Visual validation

The loop: **publish to staging → measure the real page → diff against Figma's numbers → fix root causes → re-measure → check the rest of the site.** Every fix is re-verified; nothing is assumed to have shipped.

**Measure, don't eyeball.** Both sides are numeric: Figma geometry from `get_metadata`; page geometry from the driven browser at explicit viewport sizes (`getBoundingClientRect`, computed styles). The single best health check: `documentElement.scrollWidth === clientWidth` (any difference = horizontal overflow). A useful finding looks like *"video renders at 0.70 aspect; design intends 1.27"* — diagnosable, fixable, re-checkable.

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

**Regression scope:** overflow-check every page of the site at mobile width; re-measure desktop on the changed page against known-good positions; call out any intentionally site-wide change explicitly. End every session by telling the human exactly what needs real eyes (typically: animations, and a scroll on an actual phone).

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

**The filter — what becomes a rule:**
- *Generalizable?* Will bite future sessions → skill. Page-specific → stays in the page.
- *Validated?* Patterns enter only after working end-to-end on a real page.
- *A decision, not a fact?* → handoff, as "decided, don't silently revisit" — not a rule.
- *Already covered?* Read the existing guidance first; extend or supersede, never duplicate or contradict.

**Supersede, don't overwrite.** When new evidence contradicts a documented claim, mark the old one superseded with provenance. The history tells future sessions whether a documented limit is real or stale — including Claude's own corrected false findings, named explicitly so nobody re-fixes a phantom.

Close: update the debt register, rewrite the handoff, re-package the skills (zip + source folder stay in sync).
