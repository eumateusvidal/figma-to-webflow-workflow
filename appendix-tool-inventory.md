# Appendix — Complete Tool Inventory (Used and Deliberately Not Used)

This page exists to answer a question that comes up naturally: *"which integrations actually produced these results?"* — and to prevent a specific confusion: several Figma-related integrations exist in the Claude ecosystem, and **only one of them was used here.**

## ✅ Actually used

| Resource | Kind | Role in the results | How you get it |
|---|---|---|---|
| **Webflow MCP** (Webflow's official server, Data channel) | MCP connection | All building: elements, classes, breakpoint styles, CMS bindings, assets, publishing | Connect Webflow's official MCP server to Claude; one-time browser authorization for your site. **Not a downloadable skill** |
| **Figma MCP — the Figma *desktop app* Dev Mode server** | MCP connection | All design inspection: exact node geometry (`get_metadata`), per-section screenshots, design variables, asset export (`get_design_context`) | Enable in the Figma desktop app → Dev Mode → MCP panel, with the design file open. **Not a downloadable skill** |
| **Claude Code's built-in browser** | Built into Claude Code | All verification: measuring the published page at explicit viewport sizes, overflow checks, screenshots | Ships with Claude Code — nothing to install |
| **`client-first-editorial` + `timestate-variables`** | Claude skills | The project's conventions, patterns, tokens, and accumulated lessons | [skills-export/](skills-export/) — the only two downloadable pieces |
| **Published-CSS reading** | Plain technique | Verifying that style writes actually shipped, by downloading and reading the site's compiled stylesheet | No tool — documented in the skill's MCP playbook |

## ❌ Available in the environment, but NOT used

Listed so nobody wastes time hunting for them as "the secret ingredient" — they contributed nothing to these results:

| Resource | Why it was not used |
|---|---|
| **Figma plugin skills** (`figma-use`, `figma-design-to-code`, `figma-generate-design`, and siblings) | They belong to a *different* Figma MCP server that requires an OAuth authorization never completed on this machine. The desktop-app Figma MCP covered every need. Zero calls across all sessions |
| **`figma-console` MCP** | Never called. Its write-into-Figma capabilities are irrelevant to a Figma → Webflow direction of work |
| **`design-systems-assistant` MCP** | Never called. The project's own variables skill *is* its design-system knowledge |
| **Generic `client-first-webflow` skill** | Deliberately **superseded** by the project-adapted skill (see [05-skills-and-adaptation.md](05-skills-and-adaptation.md)) — every project document instructs not to follow it |
| **"Figma Bridge"** | No resource by this name exists in this setup. If you've heard the term, the thing actually used is the **Figma desktop app's Dev Mode MCP server** (row 2 above) |

## The practical takeaway

The implementation quality came from **two MCP connections + two adapted skills + a measurement discipline** — not from a large stack of integrations. If you are reproducing this workflow, the complete shopping list is in [01-prerequisites.md](01-prerequisites.md), and the only downloadable artifacts are the two skills in [skills-export/](skills-export/). Everything else is either an integration you connect (Webflow MCP, Figma desktop MCP) or already built into Claude Code.
