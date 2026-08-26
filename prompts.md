# Prompt Library

Reusable prompts for driving the workflow. Replace “X” with the page name and attach/reference the Figma frame. These are the actual prompt patterns used on the source project — their structure (*before / during / after*, plus the explicit ask-don't-assume clause) is deliberate; see [03-implementation-workflow.md](03-implementation-workflow.md#step-8--build) for why each part matters.

## 1 — Page Implementation (Desktop)

```text
Implement the attached “X” page, using the provided reference as the source for understanding the intended page, while following the project's established best practices, existing implementation patterns, and the guidance defined in the relevant skill.
Before making any changes:
Analyze the attached reference to understand the intended content, structure, hierarchy, layout, visual direction, and relevant interactions.
Review the current project state and identify the existing patterns, conventions, components, and implementation approaches used across similar pages.
Review the relevant skill and apply its guidance together with the validated patterns already established in the project.
Reuse existing components, utilities, styles, naming conventions, and implementation patterns whenever appropriate.
Do not assume the reference should be reproduced as a pixel-perfect implementation if that would conflict with established project patterns.
During the implementation:
Keep the new page consistent with the rest of the project.
Follow the established standards for structure, styling, responsiveness, components, interactions, and other relevant implementation details.
Avoid unnecessary duplication, isolated solutions, or unrelated changes.
Only introduce new patterns or reusable components when the existing project structure does not adequately support the intended page and there is a clear reason to do so.
Treat the attached reference as guidance for the intended outcome, while allowing the existing project patterns and skill to determine how that outcome should be implemented.
If you encounter any ambiguity, missing information, conflicting patterns, or decision that would require a meaningful assumption, stop and ask me before proceeding. Do not guess or silently make important implementation decisions.
After the implementation:
Test and validate the page and all relevant functionality.
Verify responsiveness and consistency with the existing project.
Compare the final result against the attached reference to confirm that the intended content, hierarchy, and visual direction were properly translated into the project's existing design and implementation patterns.
Check for regressions or unintended effects on existing pages and functionality.
When finished, provide a clear and concise summary covering:
What was implemented.
Which existing project patterns, components, and skill guidance were applied.
Any new reusable pattern or component introduced, including why it was necessary.
Testing and validation performed.
Any remaining limitations, decisions, or considerations that may require future attention.
```

## 2 — Page Implementation (Mobile)

```text
Implement the mobile version of the attached “X” page, following the project's established best practices, existing implementation patterns, and the guidance defined in the relevant skill.
Before making any changes:
Analyze the attached reference to understand the intended mobile layout, content hierarchy, visual direction, responsive behavior, and relevant interactions.
Review the current project and the existing implementation of page “X” to understand how the desktop version is structured.
Identify the established responsive patterns, components, styling conventions, and mobile behaviors already used throughout the project.
Review the relevant skill and apply its guidance together with the validated patterns established in the project.
Reuse existing responsive patterns, components, utilities, and conventions whenever appropriate.
During the implementation:
Adapt the existing page for mobile while preserving the intended content, hierarchy, and overall design direction from the attached reference.
Prioritize consistency with the project's established responsive patterns rather than treating the reference as a pixel-perfect specification.
Ensure the mobile experience feels like a natural extension of the existing project and desktop implementation.
Avoid creating isolated mobile-specific solutions or unnecessary duplication when the existing structure can be adapted appropriately.
Only introduce new responsive patterns or reusable components when the current project does not adequately support the required behavior.
If anything is unclear, ambiguous, missing, or requires a meaningful assumption that could affect the implementation, pause and ask me before proceeding. Do not guess or silently make important decisions.
After implementation:
Test and validate the mobile layout and all relevant functionality.
Verify that the page behaves correctly across the relevant responsive breakpoints.
Check that content, interactions, spacing, hierarchy, and visual consistency are maintained.
Verify that the mobile implementation does not introduce regressions or unintended effects on the existing desktop experience or other pages.
When finished, provide a clear and concise summary covering:
What was implemented or adapted for mobile.
Which existing responsive patterns and skill guidance were applied.
Any new responsive pattern or component introduced, including why it was necessary.
Testing and validation performed.
Any remaining limitations, decisions, or considerations that may require future attention.
```

## 3 — Finish Session (handoff + close)

```text
Now, prepare a complete handoff for closing the current context window and enabling the next session to continue the project with minimal friction.
Before creating the handoff, review the current state of the project and verify everything that was completed, changed, learned, or decided during this session.
1. Consolidate the Current Session
Create or update the appropriate Markdown handoff document with a clear and accurate summary of the current session.
The document should include:
Project identification and purpose.
Current repository and branch context.
Current Pull Request status, if applicable.
Work completed during this session.
Pages, components, features, files, documentation, and skills that were created or updated.
Important patterns, decisions, and lessons learned.
Relevant skill updates and the reasoning behind them.
Testing and validation performed.
Known limitations, open questions, pending items, or dependencies.
The exact current state of the project.
A clear recommended starting point for the next session.
Focus on the final and actionable state, not on a chronological transcript of everything that happened.
2. Update Everything That Is Necessary
Review all relevant project artifacts and update anything necessary to ensure the repository accurately reflects the current state of the work.
This may include:
Skills and skill references.
Project documentation.
Context or handoff files.
Setup and onboarding instructions.
Configuration or supporting files.
Create additional files only when they provide clear value for continuity, maintainability, or immediate onboarding in the next context window.
Avoid unnecessary documentation, duplicated information, or files created only for the sake of completeness.
3. Prepare the Next Session for Immediate Continuation
The next context window should be able to identify and continue this project immediately.
Ensure the handoff clearly provides or references:
Project identification: what the project is and how to recognize it.
Repository context: the relevant repository, current branch, and working state.
Current implementation status: what is complete and what remains.
Relevant skills: which skills must be reviewed and applied before continuing.
Important project patterns: the validated conventions that should guide future work.
Starting point: the first recommended action for the next session.
4. Webflow MCP Readiness
Also verify the Webflow MCP context and document what is necessary for the next session to begin working with it immediately.
Do not assume that an MCP connection, workspace, site, credentials, permissions, or configuration is available without verification.
If the current environment allows the Webflow MCP connection to be verified, validate:
Whether the MCP connection is available and working.
The relevant Webflow workspace and site or project context.
Any required identifiers, permissions, or setup dependencies needed to begin work.
If the connection or its context cannot be verified or persisted across sessions, clearly document:
What is currently known.
What still needs to be confirmed.
The exact first step required in the next session to restore or verify access.
The goal is to avoid wasting the next context window rediscovering project identity, repository context, skills, established patterns, or Webflow MCP requirements.
5. Final Validation
Before closing this context:
Review the final working tree and relevant repository state.
Verify that all necessary documentation and skill updates are complete.
Ensure the handoff reflects the actual current state, rather than assumptions or outdated information.
Confirm that no important pending work, decision, dependency, or limitation has been omitted.
Ensure the next session can use the handoff as its primary starting point.
After completing all necessary updates, provide a concise final summary explaining:
What was updated or created to close this session.
The current state of the project.
Any remaining pending items or dependencies.
Whether the Webflow MCP context was successfully verified and is ready for immediate use, or exactly what must be done to restore or confirm it in the next session.
Do not begin new implementation work after this preparation is complete. The purpose of this step is to close the current context cleanly and leave the project in a reliable state for the next session.
```
