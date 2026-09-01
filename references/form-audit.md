# Form audit mode

Use this procedure when asked to audit, review, improve, or check forms. An audit request alone authorizes inspection and reporting, not edits; implement fixes only when requested.

## Discovery

Search beyond `<form>` tags:

1. routes/pages/components containing forms, inputs, contenteditable fields, dialogs, filters, search, onboarding, settings, and upload surfaces;
2. reusable field/form/date/combobox/upload components and wrappers;
3. client schemas, form libraries, normalization/formatting helpers, and domain types;
4. server actions, API handlers, services, authorization, database constraints, and upload workers;
5. i18n configuration, `lang`/`dir`, translations, locale/timezone utilities, and existing `Intl` use;
6. design-system documentation and current form tests.

Trace representative high-risk fields end to end: browser value → client parsing → request → server parsing/validation → domain/persistence → response/redisplay.

## Review categories

- Domain type and value lifecycle
- Semantic control and browser behavior
- Length/range/date/business constraints
- Locale, formatting/parsing, language, and RTL
- Accessibility, keyboard, focus, and announcements
- Client/server/database validation alignment
- Security, authorization, mass assignment, URLs, secrets, and uploads
- Mobile keyboard/touch, paste, autofill, and IME
- Async submission, duplicate prevention, error recovery, and state preservation
- Tests at boundaries and trust boundaries

## Severity

- **CRITICAL:** exploitable or dangerous backend acceptance; security-sensitive validation only client-side; silent corruption of amounts/dates/identifiers; inaccessible critical flow that prevents completion; severe upload/authorization exposure.
- **HIGH:** important server validation missing; numeric conversion of string identifiers; likely locale parsing error; form data erased on server failure; timezone corruption; major keyboard/screen-reader barrier.
- **MEDIUM:** poor validation timing; generic/unassociated errors; wrong mobile keyboard; missing autocomplete; weak async states; incomplete boundary coverage.
- **LOW:** helper copy, minor layout consistency, non-blocking affordance, or maintainability improvement with limited user impact.

Rank by actual impact and likelihood, not rule count. Do not label every missing enhancement as a defect. Separate observed evidence from inferred risk and note when runtime verification was unavailable.

## Recommended output

```markdown
# Form Audit

## Summary

Forms inspected: 8
Critical: 0
High: 2
Medium: 4
Low: 1

## Findings

### HIGH — Phone field parsed as a number

Location: `src/routes/profile.tsx:84`
Issue: The submit handler calls `Number(phone)`.
Impact: Leading zeros and `+` country prefixes are lost.
Recommended fix: Keep the editing/domain value as a string and normalize under the phone contract on the server.

## Cross-cutting issues

...

## Recommended priorities

1. ...
```

Use exact paths and line references where available. Include a concise remediation, not a speculative rewrite.

## Fix workflow

If fixes were requested, address critical/high issues first unless dependencies require another order. Preserve architecture, make targeted coherent changes on both client and server, add boundary/regression tests, and run available focused tests plus typecheck/lint as proportionate. Report what was not verified.

