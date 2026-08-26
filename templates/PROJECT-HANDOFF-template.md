# [Project Name] — Project Handoff

> **Read this first.** It is the entry point for a new session on this project.
> **Every fact below was verified live at [date, time] — state how:** which API reads, which published pages, which viewport widths. Facts that could not be verified are marked as such.
> **Rewrite this document at every session close — never append.** Re-verify its facts live (MCP probes, publish timestamps, page inventory) instead of copying them forward. A stale handoff costs more than no handoff: the next session will trust it.

---

## 1. Project Identification

What this project is, one paragraph: purpose (client site / template / product), the framing that drives decisions (e.g. "template for sale → content must be real, structures must survive N CMS items").

> **Standing constraints** the user has stated go here, prominently — e.g. "no custom code", "never touch production", "X is out of scope". These outrank convenience on every future decision.

| Fact | Value |
|---|---|
| Site ID | |
| Workspace ID | |
| Staging / production domains | (state explicitly whether publishing can reach production) |
| Plan-level limits discovered | |
| Locales | |
| Pages (count + where the ID table lives) | |
| CMS collections (IDs + item counts) | |

## 2. Repository & Branch Context

- Git repo or not; branch; remote; PR status — or state explicitly that none apply.
- **Source of truth for the build** (usually: the live site) and **for conventions** (usually: the skills).
- The working tree: list every file/folder at the root with a one-line role. Flag anything disposable.
- If skills exist as both editable folders and packaged zips: state the sync rule and whether they were verified in sync at close.

## 3. MCP / Tooling Readiness — probed at close, not assumed

| Channel / tool | Probe run (and when) | Result |
|---|---|---|
| | | |

- What needs **no setup** next session (and how you know), vs. what must be re-verified.
- **The exact first call the next session should make** to confirm readiness, with the IDs it needs inline.
- Known hard limits of the tools *as verified on this project*, with pointers to where the workaround lives.

## 4. Exact Current State — what is live

- Last publish: timestamp, and **how you proved nothing is pending** (e.g. per-page lastUpdated vs lastPublished — never a site-level timestamp).
- Per-page table: state, known issues (referencing debt-register IDs).
- Any verification sweep results worth keeping (e.g. overflow audit per page at mobile width).

## 5. Work Completed This Session

Final state, not chronology. Per work item: what, where, root cause if it was a fix, and how it was verified. Include:
- **Corrections to earlier claims** (yours or previous sessions') — named explicitly so nobody re-fixes a phantom or re-trusts a false fact.
- **Things deliberately NOT done**, with the evidence that justified not doing them.
- New classes/components/tokens created (name, parent, purpose).

## 6. Validated Patterns That Must Guide Future Work

Numbered list of the conventions future sessions must follow instead of re-deriving. Add new ones at the bottom, labeled with the build that validated them. If one was superseded, strike it and say why.

## 7. Skills & Documentation — What Changed and Why

| Change | Where | Why |
|---|---|---|

## 8. Known Limitations, Pending Items & Open Questions

- Point to the canonical debt register (do not duplicate it) and extract only the highest-priority items.
- **Decisions made this session (do not silently revisit)** — each one sentence.
- Known gaps left deliberately, with the reason.
- Open questions carried forward — and which ones block what.
- State plainly: is anything half-finished? (Best answer: "No blocked or half-finished work.")

## 9. Testing & Validation Performed

What was verified, how, and at which breakpoints/pages. Then — just as important:

> ⚠ **Honest caveats:** what was NOT verified and why (environment limits, scope), and any measurement traps the next session must know before trusting its own tools.

## 10. Recommended Starting Point for the Next Session

- **The first five minutes**, as an exact copy-pasteable sequence: read this file → load skills → run the readiness probe → skip rediscovery (point to the ID registry).
- The suggested first work item, with why it's highest-value.
- Pre-publish and pre-work checks specific to this project.
- What deserves 60 seconds of human eyes.
