# Installing the Skills

This folder contains the two skills that power the Figma → Claude → Webflow workflow, packaged for reuse.

## ⚠️ Read this first

These skills are **adapted to the Timestate project** (see [../05-skills-and-adaptation.md](../05-skills-and-adaptation.md)). Installed as-is, they will make Claude follow *Timestate's* architecture — a specific 12-column grid, a specific token scale, and IDs that only exist on that site. **Use them as a working example and a template for your own project skill**, not as a universal method. The layer-by-layer portability breakdown and the 7-step adaptation workflow are in file 05.

> **Attribution:** the `client-first-editorial` skill is an independent adaptation built on **[Client-First](https://finsweet.com/client-first) by Finsweet**. It is not an official Finsweet skill or product.

## What's in this folder

| File | What it is |
|---|---|
| `client-first-editorial.skill` | The working skill — *how to build* (methodology, structure, patterns, tool playbook, class inventory) |
| `timestate-variables.skill` | The companion skill — *what values to use* (design tokens, IDs, theming system) |

A `.skill` file is simply a **zip archive** containing the skill's folder. Inside each:

```
client-first-editorial/
  SKILL.md                      ← the instructions (starts with a name + description header)
  references/
    mcp-workflow.md
    utility-classes.md

timestate-variables/
  SKILL.md
  references/
    token-values.md
```

You can rename one to `.zip` and open it with any unzip tool to read everything before installing — recommended.

## Installation (Claude Code)

Claude Code discovers skills placed in its skills directory. Each skill is one folder containing a `SKILL.md`.

**Step 1 — Unzip each `.skill` file** (rename to `.zip` if your unzip tool insists):

```bash
unzip client-first-editorial.skill
unzip timestate-variables.skill
```

This produces the two folders shown above.

**Step 2 — Move the folders into your Claude skills directory:**

- **Personal (all projects):** `~/.claude/skills/`
- **Per-project:** `.claude/skills/` inside the project folder

```bash
mkdir -p ~/.claude/skills
mv client-first-editorial timestate-variables ~/.claude/skills/
```

**Step 3 — Restart / start a Claude Code session** in your project.

## Verifying the installation

In a Claude Code session, ask:

> "What skills do you have available?"

Both skill names should appear. Then ask something the skill covers, e.g.:

> "According to the client-first-editorial skill, how should sections be structured?"

If Claude answers with the section nesting convention (`section_[page]-[section]` → padding wrapper → grid), the skill is loaded and working.

> If your Claude environment offers a skills listing command or settings panel, the two names should appear there too. If a skill doesn't load, the usual cause is the folder structure: the `SKILL.md` must sit directly inside a folder named after the skill, inside the skills directory.

## Using the skills correctly

The skills are **one third** of what makes the workflow work. The other two thirds:

1. **Project context** — a handoff document describing your project's current state, and (as it grows) an architecture record + debt register. The skills tell Claude how to build; the handoff tells it where the project stands. See [../02-project-preparation.md](../02-project-preparation.md).
2. **The live connections** — the Webflow MCP authorized for *your* site, and the Figma MCP running in your Figma desktop app. See [../01-prerequisites.md](../01-prerequisites.md).

Installing the skills without adapting them to your project will produce confident output aimed at the wrong architecture. The adaptation recipe is in [../05-skills-and-adaptation.md](../05-skills-and-adaptation.md) — it is the actual work.
