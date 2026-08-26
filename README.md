# Figma → Claude → Webflow — Implementation Playbook

A practical workflow reference for implementing Webflow pages from Figma designs with high visual fidelity, using **Claude** as the builder — connected live to Figma and Webflow through their MCP integrations.

**What this repository is:** the complete documentation, prompt library, downloadable Claude skills, and templates for a workflow used in production on a real Webflow template project. It documents the actual process — including its constraints, hard tool limits, and failure modes — not an idealized version.

**Who it's for:** experienced Figma/Webflow users who want to reproduce or adapt AI-assisted page implementation without sacrificing design-system consistency, responsiveness, or maintainability. It assumes fluency with Webflow, Figma, and general development workflow; it does not assume prior experience with Claude, skills, or MCPs (setup is covered in [01](01-prerequisites.md)).

**How the pieces fit:** *project documentation* (a handoff + architecture record) gives Claude the project's current state; *skills* give it the project's conventions and accumulated lessons; the *Figma MCP* provides exact design geometry instead of screenshot guesswork; the *Webflow MCP* is where building happens; *visual validation* closes the loop by measuring the published result against the design's numbers. Each has a dedicated section below.

## The workflow

```
PREPARE (once per project)
 1. Prepare the design system and handoff in Figma          → 02
 2. Prepare the Webflow project: variables, naming, base classes → 02
 3. Install the skills; write your project-context docs      → 01, 04
 4. Connect and validate the Figma + Webflow MCPs            → 01

IMPLEMENT (every page)
 5. Analyze the existing project before touching anything    → 03
 6. Analyze the Figma design — numbers first                 → 03
 7. Map the design onto existing project patterns            → 03
 8. Build with validated patterns; ask on real decisions     → 03
 9. Test responsiveness and functionality                    → 03

CLOSE (every session)
10. Visual validation against Figma; fix root causes         → 04
11. Capture reusable learnings into the skills               → 04
```

## Files

| File | Contents |
|---|---|
| [01-prerequisites.md](01-prerequisites.md) | Required tools, MCP setup, validation checks |
| [02-project-preparation.md](02-project-preparation.md) | Design-system and project prep — **the results depend on this** |
| [03-implementation-workflow.md](03-implementation-workflow.md) | Steps 5–9: analysis, Figma inspection, build rules, responsive system |
| [04-validation-and-learning.md](04-validation-and-learning.md) | Steps 10–11: measurement-based validation, skill capture |
| [05-skills-and-adaptation.md](05-skills-and-adaptation.md) | **The skills, and how to adapt `client-first-editorial` to your project** — the most detailed section |
| [appendix-tool-inventory.md](appendix-tool-inventory.md) | Exactly what was used and what wasn't |
| [prompts.md](prompts.md) | The reusable prompt library (desktop page, mobile page, session close) |
| [templates/](templates/) | Ready-to-use project-handoff template |
| [skills-export/](skills-export/) | Downloadable skills + install guide |

## Attribution: Client-First by Finsweet

The naming and structure methodology underlying this workflow's main skill is **[Client-First](https://finsweet.com/client-first), a methodology created by Finsweet**. Full credit for Client-First belongs to Finsweet; their official documentation is the authoritative source for the methodology itself.

The **`client-first-editorial`** skill in this repository is an **independent adaptation** of that methodology, created for one specific working context (implementing a Webflow template with Claude). It deliberately diverges from standard Client-First in documented places — most notably a full-width 12-column grid instead of centered containers. It is **not an official Finsweet product, skill, or endorsement**, and should never be presented as one. The layer-by-layer breakdown of what is Finsweet's methodology, what is project adaptation, and how to build your own adaptation is in [05-skills-and-adaptation.md](05-skills-and-adaptation.md).

## The one principle everything else follows from

**The project is the authority; the design is the reference.** Figma defines what the page should communicate — structure, hierarchy, composition. The project's documented system (tokens, classes, patterns, breakpoints) defines how it gets built. Where they conflict, the project wins and the design is adapted to the system. This is what keeps ten pages looking like one site, and what makes the output maintainable rather than screenshot-shaped.
