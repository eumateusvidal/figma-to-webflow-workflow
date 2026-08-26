# 01 — Prerequisites and Setup

## Required stack

| Requirement | Notes |
|---|---|
| **Claude Code** | The working environment: reads project files, drives both MCPs, holds long sessions |
| **Webflow MCP** (official server, Data channel) | One-time browser authorization for your site. All building goes through this |
| **Figma MCP** (the **desktop app's** Dev Mode server) | Figma desktop app → Dev Mode → MCP panel, with the design file open. Not the plugin-based server — see [appendix-tool-inventory.md](appendix-tool-inventory.md) |
| **The two skills** | From [skills-export/](skills-export/) — install, then **adapt** per [05](05-skills-and-adaptation.md) |
| **Project-context docs** | A handoff file + (as the project grows) an architecture/debt record. See [02](02-project-preparation.md) |

Built into Claude Code, nothing to install: a driveable browser for measuring the published site.

## Figma MCP setup

- Asset export (`get_design_context`) requires whitelisting a writable directory: **Figma desktop → Dev Mode → MCP panel → Allowed directories**. Everything else (`get_metadata`, `get_screenshot`, `get_variable_defs`) works without it.
- Geometry comes from `get_metadata` (node x/y/w/h) — that's what layout derivation consumes. Screenshots are secondary.

## Webflow MCP — capabilities and hard limits

**Data channel** (the workhorse): elements, classes with per-breakpoint values, CMS collections/bindings, assets, publishing. No per-session setup once authorized.

**Designer channel**: canvas selection/navigation only. **It cannot style anything** — never plan work around it. It disconnects whenever the companion app tab loses focus; treat it as down.

**Cannot be done via MCP at all** (require ~30s of manual Designer clicks each; Claude specifies exactly what to click, then verifies from the published page):

1. Per-element grid placement (`#w-node-*` rules — element-level, not class-level)
2. CMS "Current item" links and multi-reference list sources — **the API read is also lossy for these**: a correct setting reads back identically to a broken one. Verify only from published output.

## Validation checks (run these before starting work)

```
Webflow:  one cheap read — get_all_elements { depth: 1 } on any page.
          Returns the Body root → channel is live. Never assume up or down; probe.

Figma:    get_metadata on the target frame. Returns the node tree → live.

Browser:  resize to an EXPLICIT width/height (presets can silently fail),
          then assert document.documentElement.clientWidth is non-zero
          before trusting any measurement.
```

## Session startup ritual (~1 minute, every session)

1. Read the project handoff doc — current state, recent decisions, what not to redo
2. Load both skills
3. Probe the Webflow MCP (above)
4. Skip discovery — the variables skill's ID registry has every page/class/token/component/CMS-field ID already harvested
