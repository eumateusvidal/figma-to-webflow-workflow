# 02 — Project Preparation: Where the Quality Actually Comes From

**AI-assisted implementation does not compensate for a poorly structured design or project.** Claude amplifies whatever system exists: a clean token scale and consistent components produce consistent pages; ad-hoc values and one-off components produce ad-hoc output faster. Preparation is not overhead — it is the mechanism.

## Figma-side preparation

**Design system before pages:**
- Text styles for every type level — named, not detached. A style reading "Semibold/56/1.0" that maps 1:1 to a Webflow heading token is what lets Claude resolve type without guessing. Unmapped text = a question, or worse, an invented value.
- Color styles/variables for the full palette, including semantic roles (background/text/stroke per theme) rather than raw hex only.
- A spacing scale used deliberately. Gaps that land *on* the scale get recognized as tokens; arbitrary gaps (37px) force a snap-or-ask decision on every one.
- Components with variants for anything repeated — cards, nav, CTA, footer. One detached copy per page destroys the reuse signal.

**Layout discipline:**
- Design on the grid the implementation will use, with consistent margins. Column derivation is arithmetic: `(frame width − 2×padding) ÷ columns`. Blocks that sit on column lines resolve exactly; near-misses cause rounding judgment calls.
- Include the responsive frames that matter (at minimum: desktop + mobile). A desktop-only design forces the implementer to invent the recomposition.
- Delete or clearly separate scaffolding: hidden layers, abandoned drafts, alternate states. Claude builds the visible composition; ambiguous leftovers cost questions.

**Handoff content:**
- Real copy, or explicit "placeholder — re-author" markers. Template designs carrying another brand's text get flagged, not shipped.
- For CMS-driven sections: design for *N items*, not a hand-tuned collage of exactly 6. A layout that only works at one item count cannot be implemented as a collection list.
- Note intended interactions (what animates, what's a slider, what's a link) — a static frame is silent about all of it.

## Webflow-side preparation

**Variables (design tokens) are the backbone — set them up first:**
- Full scales for spacing, typography (size/line-height/weight per level), colors, radii, section padding.
- **Put responsive values on the tokens themselves** (per-breakpoint variable values). A heading token that is 8rem desktop / 3.5rem mobile makes every consuming class responsive with zero breakpoint overrides. This single decision eliminates most breakpoint work.
- Semantic layer over primitives: classes consume `text-primary`, never `brown-800` directly. Theme switching (light/dark sections) then becomes a variable *mode* flip instead of hand-mixed colors.

**Class system:**
- Pick a naming convention and hold it (this project: Client-First-derived — `pagename-section_element` for page-scoped, `component_element` for shared, `is-*` for modifiers).
- Build the structural primitives once: page wrapper, section pattern, padding wrappers, the grid, spacers, typography utility classes. Every page then assembles the same primitives.
- **No hardcoded values in classes** — every color, size, and spacing bound to a token. Enforce it from day one; retrofitting is miserable.

**Components:** navbar, footer, CTA and any repeated section become Webflow components *before* the second page exists. Pages override instance properties; nobody rebuilds.

**Alignment between the two systems:** the closer Figma's scales/names map to Webflow's tokens/classes, the more of the implementation becomes mechanical lookup instead of judgment. Perfect 1:1 isn't required — documented correspondence is.

## Project-context documents (what Claude reads before building)

Maintain three living documents (Markdown, in the project folder):

| Document | Job | Update cadence |
|---|---|---|
| **Handoff** | Current state: what's built/published/decided, verified MCP status, next action | Rewritten (not appended) at every session close |
| **Architecture record + debt register** | The system's *why*, plus every known issue as a numbered item with status | When architecture changes; debt items as found/fixed |
| **The skills** | Conventions, patterns, tool traps, ID registry | Every session's lessons — see [04](04-validation-and-learning.md) |

These are what make session N+1 start where session N ended instead of re-exploring.

**A ready-to-use handoff template is in [templates/PROJECT-HANDOFF-template.md](templates/PROJECT-HANDOFF-template.md)** — genericized from the real one this project maintains, section by section.
