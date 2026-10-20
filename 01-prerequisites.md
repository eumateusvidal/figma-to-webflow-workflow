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

## Webflow MCP — detect capabilities, don't assume them

The Webflow MCP server changes between versions, and what a given session can call changes between clients and sessions. Notes written in July 2026 said there was no element query/move/delete and no page creation; version 2.0.1 had all of them. A later session found the Webflow connector authorised on the account but its tools **not loaded** in the IDE session at all. So: plan from what this session has proven.

**Capability check (every session, a few minutes):**

1. **Loaded, not connected.** List the Webflow tools actually callable in this session. If they're deferred, load them; if they're absent, say so and don't plan writes.
2. **Read the server's guide once** and note its version string (2.2.0 at the time of writing).
3. **Resolve the site explicitly** (never assume a site ID) and read its details: plan signals, locales, domains, last publish.
4. **One cheap read per tool layer:** data/REST (list pages), element/style (`get_all_elements { depth: 0 }` on a known page; query one known style), Designer (current page or one element snapshot — needs the Designer open on that site; a failure there says nothing about the other layers).
5. **Probe plan-gated features instead of inferring them.** For example, page branching returns 403 `not_enterprise_plan_site` on plans without it — treat that as final.
6. **Record a capability matrix** in the handoff — capability · tool/action · proven/failed/untested · evidence · date · version — and prove risky write behaviours on a sandbox site or a disposable draft page, never on existing pages.

**Tool layers, as observed (2026-08 → 2026-10, MCP 2.0.1–2.2.0):**

| Layer | Tools | Needs the Designer open? |
|---|---|---|
| Data / REST | pages, sites, CMS, assets, forms, scripts, publishing | No |
| Element / style / component (`data_*`, addressed by site + page) | element tree, settings, styles, variables, components, props, variants, HTML builder, interactions | Worked without the Designer companion app on 2.0.1+ — confirm with the probe |
| Designer UI | `designer_tool` (selection, canvas navigation, page switching), `element_snapshot_tool` | Yes — and it disconnects when the Designer reloads |

> **Superseded wording (kept for provenance).** Earlier versions of this page said the "Designer channel … cannot style anything — never plan work around it." That is still true of `designer_tool` itself, but styling never went through it: it goes through the `data_*` element/style tools, which later sessions used without the Designer open. Probe; don't rely on either sentence.

**Observed to need a person in the Designer** (re-check on your version; Claude specifies exactly what to click, then verifies from the published page):

1. Per-element grid placement (`#w-node-*` rules — element-level, not class-level)
2. CMS "Current item" links and multi-reference list sources — **the API read is also lossy for these**: a correct setting reads back identically to a broken one. Verify only from published output.
3. Form name and some form settings (read back correctly through the API, but didn't publish), and lightbox media lists.
4. On plans without API access to them: 301 redirects, and the "redirect the old URL" option when changing a slug.

The rendering differences between the Designer canvas and the published page — and what to do about them — are in [06-designer-parity-and-troubleshooting.md](06-designer-parity-and-troubleshooting.md).

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
3. Run the capability check (above) and update the handoff's capability matrix if anything changed
4. Skip discovery — the variables skill's ID registry has every page/class/token/component/CMS-field ID already harvested
