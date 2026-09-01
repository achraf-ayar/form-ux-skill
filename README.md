# Form UX Skill

Form UX Skill is a reusable instruction repository for filesystem-based coding agents. It helps an agent design, implement, audit, and repair web forms by reasoning about domain data, browser semantics, localization, accessibility, validation, security, mobile input, and recovery—not merely appearance.

It is not a UI component library and does not prescribe a framework. The instructions tell an agent to adapt to React, Next.js, Vue, Nuxt, Svelte, SvelteKit, plain HTML/JavaScript, TypeScript, Tailwind, shadcn/ui, component libraries, and custom design systems already present in a project.

## Why it exists

Plausible-looking forms often corrupt or reject real data. Common failures include:

- converting `000123` or a phone number into a number;
- shifting a date-only value through UTC conversion;
- parsing `1.234,50` with US assumptions;
- validating a security-sensitive rule only in the browser;
- clearing entered values after a server error;
- using placeholders as labels or inaccessible custom calendars;
- blocking paste, autofill, password managers, keyboards, or IME input;
- hardcoding `$`, English errors, LTR layout, or `MM/DD/YYYY`.

The skill makes these decisions explicit and reviewable.

## Supported agents

The repository uses ordinary Markdown and a `SKILL.md` entrypoint. It can be read by Claude Code, OpenAI Codex, Agy, Cursor-like tools, and other agents that load instructions from a filesystem. This describes format-level compatibility; it is not an endorsement or official vendor integration.

## Installation ideas

Choose the convention your agent supports:

1. Clone or copy this repository into the agent's skills directory.
2. Add a symlink from that directory to this checkout if you want to contribute changes here.
3. Keep it in a project and tell the agent to load `form-ux-skill/SKILL.md` for form work.

The root directory and frontmatter name are both `form-ux-skill`, matching common skill discovery conventions.

## Usage

Example prompts:

```text
Audit every form in this repository using the form UX skill.
```

```text
Create this signup form following the form UX skill.
```

```text
Check the date, number, phone, and currency fields using the form UX skill.
```

```text
Fix all high-severity form issues. Preserve the existing design system.
```

An agent should first inspect the project, then select only the relevant references. For a large form, it may construct the field matrix from `SKILL.md`. For an audit, it should report severity, evidence, precise locations, impact, and a targeted fix.

## Principles

- Domain meaning comes before input appearance.
- Raw, display, normalized, domain, and serialized values may differ.
- Native HTML and browser behavior are the default when they meet the requirement.
- Client validation improves feedback; it is not a security boundary.
- Locale, language, RTL, Unicode, accessibility, mobile use, and recovery are core requirements.
- Existing dependencies and conventions win unless a real gap justifies a change.
- A digit-only value is not necessarily numeric.
- Simple forms should stay simple.

## Repository structure

```text
form-ux-skill/
├── SKILL.md                 Agent entrypoint and routing
├── README.md                Human introduction and usage
├── AGENTS.md                Maintenance instructions
├── LICENSE                  MIT license
├── references/              Detailed, topic-specific guidance
├── examples/                Compact cross-stack implementation examples
├── checklists/              New form, audit, and release checks
└── tests/skill-scenarios.md Behavioral decision scenarios
```

`SKILL.md` is intentionally focused. Detailed knowledge belongs in `references/`; examples illustrate decisions without imposing one framework.

## Contributing

Keep guidance framework-neutral, actionable, and grounded in web/platform behavior. Add a rule only when it changes a meaningful agent decision. Prefer one canonical explanation with links over repeated prose. New examples must show reasoning or a failure mode, not simply another library syntax.

Before submitting a change:

- ensure every link resolves and every file has substantive content;
- keep `SKILL.md` concise enough for routine loading;
- update scenario tests when behavior changes;
- check examples for client/server agreement, accessibility, locale, and type correctness;
- avoid new runtime dependencies—this repository is documentation;
- do not claim vendor certification or compatibility beyond filesystem-based instruction loading.

See [AGENTS.md](AGENTS.md) for maintainer-facing rules.

