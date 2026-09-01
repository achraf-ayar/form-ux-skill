# Agent maintenance guide

## Purpose

This repository is an instruction skill for coding agents working on web forms. It is not an application or component package. Preserve its framework-neutral scope and make agents adapt to the target project's domain, stack, design system, dependencies, locale, and trust boundaries.

## Source of truth

- `SKILL.md` is the primary agent entrypoint. Keep it focused on activation, invariant principles, workflow, decision trees, reference routing, and completion criteria.
- `references/` owns detailed topic guidance. Put a fact in one canonical reference and link to it rather than duplicating it.
- `examples/` demonstrates compact reasoning patterns. Examples are illustrative, not copy-paste architectures.
- `checklists/` supports execution without replacing the reasoning in `SKILL.md`.
- `tests/skill-scenarios.md` is a behavioral regression suite: it records decisions a compliant agent should reach.

Instructions in deeper `AGENTS.md` files would override these rules for their subtree; none are currently needed.

## Quality expectations

- Be precise, practical, technically accurate, and opinionated only where semantics, safety, or accessibility justify it.
- Keep framework APIs current enough to be safe, but favor platform concepts over library-specific recipes.
- Never encode US-only, ASCII-only, LTR-only, or single-currency assumptions.
- Preserve the distinction between client feedback and authoritative server validation; validation, sanitization/encoding, and authorization are different controls.
- Do not recommend a new dependency when native HTML or the target project's existing stack is sufficient.
- Avoid enormous entrypoint sections, filler, duplicated rules, invented standards, and unfinished placeholders.

## Updating the skill

1. Identify the agent decision that needs to change and the realistic failure it prevents.
2. Edit the narrowest canonical reference. Change `SKILL.md` only if activation, routing, a cross-cutting invariant, or the core workflow changes.
3. Add or revise a compact example only when syntax materially clarifies the decision.
4. Add a scenario that would fail under the old guidance and pass under the new guidance.
5. Verify relative Markdown links, terminology, frontmatter, and absence of placeholder text.
6. Review the complete diff for accidental framework assumptions or duplicated guidance.

## Testing

There is no runtime package to install. Validation is documentation-oriented:

- run the available skill frontmatter/scaffold validator when present;
- check every local Markdown link resolves;
- inspect the repository tree for missing or empty required files;
- search for unfinished scaffold material and contradictory terminology;
- reason through affected scenarios in `tests/skill-scenarios.md`;
- validate code snippets for syntax and, more importantly, correct trust-boundary and value-model behavior.

Do not add a test framework solely to test Markdown. Small portable validation commands are preferable.

