# Form audit checklist

## Scope and evidence

- [ ] Inventory forms, search/filter controls, dialogs, uploads, reusable fields, schemas, and custom widgets.
- [ ] Trace high-risk values from browser through client, server, domain, and persistence.
- [ ] Inspect i18n/RTL, design system, validation stack, server authorization, database constraints, and tests.
- [ ] Record exact file paths/lines; distinguish observed evidence from runtime inference.

## Review

- [ ] Domain types and raw/display/normalized/domain/serialized conversions preserve data.
- [ ] Controls use appropriate semantic type, keyboard, autocomplete, and native behavior.
- [ ] Numeric ranges/precision, dates/timezones, currency, phones, identifiers, Unicode, and locale parsing are correct.
- [ ] Client feedback is humane and important rules are enforced server-side; database/authorization gaps are separate findings.
- [ ] Labels, groups, keyboard, focus, errors, announcements, RTL, touch, paste, autofill, and IME are usable.
- [ ] Submission prevents accidental duplication, handles all failure classes, and retains values.
- [ ] Upload and URL handling treat metadata/input as untrusted.
- [ ] Tests cover boundaries, server bypass, locale/timezone, failures, and races.

## Report or fix

- [ ] Rank findings CRITICAL/HIGH/MEDIUM/LOW by likelihood and impact.
- [ ] For each finding include location, issue, impact, and targeted recommended fix.
- [ ] Summarize cross-cutting causes and prioritized work without overstating certainty.
- [ ] Edit only if requested; then fix critical/high risks coherently across client/server and add regression tests.
- [ ] Run relevant checks and state what remains unverified.

