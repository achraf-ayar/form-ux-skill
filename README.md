

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
- avoid new runtime dependencies this repository is documentation;
- do not claim vendor certification or compatibility beyond filesystem based instruction loading.


See [AGENTS.md](AGENTS.md) for maintainer-facing rules.



