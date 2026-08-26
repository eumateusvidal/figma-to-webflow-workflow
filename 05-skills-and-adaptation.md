# 05 — The Skills, and How to Adapt Them to Your Project

## What ships in [skills-export/](skills-export/)

A skill = a folder with a `SKILL.md` (YAML `name` + `description` frontmatter, then instructions) plus optional `references/`. Claude loads it when the description matches the task; from then on, every decision routes through it.

| Skill | Role | Contents |
|---|---|---|
| **`client-first-editorial`** | *How to build* | Naming, section structure, the 12-col grid system, typography layering, spacing methods, responsive rules, ~30 validated build patterns; references: the full **MCP playbook** (every verified tool trap + recovery) and the **class inventory** |
| **`timestate-variables`** | *What values* | Three-layer token architecture, color-scheme (light/dark section) modes, all scales, decision trees, and the **ID registry** (every page/class/token/component/CMS-field ID, pre-harvested) |

Always loaded together. The interaction with the rest of the system: **skills define conventions → handoff defines current state → architecture record defines the why → the live site is final truth.**

## ★ `client-first-editorial` is a contextual adaptation — not a universal method

The skill was adapted to one specific working context: implementing the **Timestate** Webflow template. Its foundation is **[Client-First](https://finsweet.com/client-first), the Webflow methodology created by Finsweet** — all credit for that foundation is theirs, and their official docs remain the authoritative source for it. On top of that foundation sit deliberate project decisions and hard-won specifics. **This adaptation is not an official Finsweet product and is not endorsed by Finsweet.** Installed unchanged on another project, it will confidently apply the *wrong* architecture. Know the layers:

| Layer | Examples | Portable? |
|---|---|---|
| Client-First foundation (Finsweet) | class naming discipline, utility philosophy, semantic structure, accessibility | Yes |
| Timestate architecture | **full-width 12-col grid instead of Client-First's centered containers** (a deliberate contradiction), variable-mode section theming, the specific token scale, the editorial-row patterns | No — template design decisions |
| Project specifics | ID registry, class inventory, documented tool traps *as encountered here*, page patterns | No — but the *categories* are exactly what your adaptation should populate |

Adapting a skill to the real project beats applying generic rules because a build session doesn't need general answers — it needs *this project's* answers: one correct way per decision, no plausible-but-wrong defaults, and every documented trap is an hour the next session doesn't lose.

## The adaptation workflow (recommended, step by step)

Use Claude itself to do the adaptation — against a real project that already represents the patterns you want:

**1. Start from `client-first-editorial` as the baseline.** Install it, keep it intact as the reference copy.

**2. Ask Claude to analyze the skill alongside your existing project.** Give it both: the skill, and access to the project (the live Webflow site via MCP, plus the published pages/CSS). The project should be one whose patterns you actually want to follow.

**3. Have Claude compare skill guidance against the project's real implementation patterns** — not against intentions or memory. Published CSS and element trees are the evidence.

**4. Ask for meaningful differences across, at minimum:**
- design patterns and section structures
- layout and grid systems
- typography and sizing
- colors and variables
- spacing and visual rhythm
- component structures
- naming conventions
- responsive behavior
- interaction patterns
- reusable implementation conventions

**5. Require Claude to classify every difference into one of four buckets:**

| Bucket | Meaning | Action |
|---|---|---|
| **Established, reusable project pattern** | Used consistently in 2+ places, clearly deliberate | Formalize into the skill |
| **Intentional project-specific difference** | A real design decision that diverges from the baseline | Document it *as* a divergence, replacing the baseline rule |
| **One-off implementation** | Appears once, or inconsistently, or looks accidental | Do **not** encode; optionally list as an open question |
| **Baseline guidance still correct** | The generic rule matches the project | Keep unchanged |

**6. Ask Claude to propose the concrete edits** — additions, removals, adjustments — as a reviewable list, not as an already-rewritten skill.

**7. Review before applying.** You approve each change; Claude applies the approved set and re-packages.

**Critical caveat for step 5:** the purpose is not to customize the skill to match every existing implementation. **The project itself is under evaluation too.** An inconsistency Claude finds may mean the *project* has a bug or an accidental pattern — formalizing it would canonize a mistake. The test for every candidate rule: is it *intentional, consistent, reusable, and worth enforcing on future pages?* If unclear — that's a question for you, not a rule.

**The result** should be a project-aware layer combining: established best practices + the Client-First foundation where still applicable + validated project-specific patterns + lessons from real implementations. Then populate the companion variables skill with *your* tokens and IDs (its Timestate content is ~100% project-specific; its structure — token layers, decision trees, ID registry, token-debt section — is the reusable part).

## Continuous improvement (the skill is never finished)

The skill you install is the starting point; the one you have after five builds is the asset. Per session, per [04](04-validation-and-learning.md):

- evaluate each new learning for **general usefulness** before writing it down
- reusable patterns → the skill, labeled with the build that validated them
- one-off decisions → the handoff as decisions, never as rules
- **read existing guidance before adding** — extend or supersede with provenance; never duplicate or contradict
- capture across all fronts: Figma analysis technique, design-system usage, responsive patterns, Webflow/tool behavior, validation traps

The compounding is the point: on the source project, the fourth page needed almost none of the discovery the first three paid for.
