---
name: form-ux-skill
description: Design, implement, audit, or repair web forms with correct domain modeling, browser semantics, validation, localization, accessibility, mobile behavior, security, and error recovery. Use for form creation and reviews across framework-based or plain web projects; adapt to the existing stack instead of introducing a component library.
---

# Form UX

Build forms that preserve meaning, remain usable, and enforce trusted constraints. A field is not correct merely because it looks correct.

## Operating principles

- Inspect before editing. Follow the project's framework, form patterns, design system, i18n, and validation stack.
- Model meaning before markup: distinguish raw, display, normalized, domain, and serialized values.
- Prefer semantic HTML and native browser behavior. Add JavaScript or custom controls only for requirements native controls cannot meet.
- Treat all client values as untrusted. Enforce business and security invariants at the trusted server boundary; database constraints remain the final integrity layer where applicable.
- Never infer numeric domain type from digits alone. Phone numbers, postal codes, account numbers, document numbers, and many identifiers are strings.
- Follow the active locale and language. Never assume US dates, dot decimals, ASCII names, left-to-right layout, or `$` currency.
- Make accessibility, keyboard use, mobile input, failure recovery, and boundary testing part of the implementation—not a later polish pass.
- Preserve existing architecture and dependencies. Do not add a form, schema, date, mask, or i18n package without a demonstrated need.

## Choose a mode

- **Implement or repair:** inspect the project, make a field contract, implement the narrowest coherent change, and verify both client and server paths.
- **Audit:** discover all relevant form surfaces and report evidence-ranked findings. Change files only when the user requested fixes.
- **Explain or review:** inspect enough code to ground the answer, then explain specific risks and remedies without assuming authorization to edit.

## Repository inspection

Before coding, find:

1. Framework, rendering model, form library, reusable form/field components, and design-system controls.
2. Existing schema validators and conventions; do not add Zod, Valibot, Yup, Joi, or another validator merely because it is familiar.
3. Server actions, route handlers, APIs, services, database constraints, authorization, and upload handling.
4. `html lang`, direction, locale routing/preferences, translation catalogs, and existing `Intl` usage.
5. Domain types, API contracts, persistence representations, and tests.
6. Existing date pickers, comboboxes, file uploaders, error summaries, and form-state patterns before building replacements.

For complex forms, write this working matrix before implementation:

| Field | Domain Type | HTML Type / Control | Required | Constraints | inputMode | Autocomplete | Locale-sensitive | Server Validation |
|---|---|---|---|---|---|---|---|---|
| Example | string | `tel` | yes | country-aware business rule | `tel` | `tel` | formatting | yes |

Also record accepted temporary input states, normalization timing, display formatting, and serialization when they differ.

## Implementation procedure

1. State each field's domain meaning and whether arithmetic, ordering, or calendar/time semantics apply.
2. Define requiredness, length/range/boundary rules, accepted characters, business rules, and who is authoritative for each rule.
3. Select the simplest semantic control. Reuse an accessible project control when native HTML is insufficient.
4. Define raw → normalized → domain → serialized conversion explicitly. Keep presentation formatting reversible and locale-aware.
5. Add intentional `autocomplete`, `inputMode`, labels, help/error associations, keyboard behavior, and localized copy.
6. Use client validation for timely feedback and server validation for trust. Align shared constraints without treating shared code as proof that the server path runs.
7. Validate after interaction, blur, or submit as appropriate. Reserve live validation for genuinely useful feedback; allow temporary typing states and IME composition.
8. On failure, retain user input, expose actionable field errors and an error summary when useful, and focus/navigate to the first relevant error without creating a focus trap.
9. During async submission, communicate progress, prevent accidental duplicate actions, preserve layout, handle network/server errors, and make retry safe.
10. Test boundaries, locale behavior, keyboard/paste/autofill, server rejection, and recovery using the project's existing tools.

## High-impact decisions

### Is the value numeric?

```text
Will arithmetic be performed on it?
├─ Yes → a numeric/decimal/money domain type may be appropriate.
│        Define precision, range, sign, zero, locale parsing, and serialization.
└─ No  → prefer a string when leading zeros or exact characters matter.
         phone/postal/invoice/account/document IDs are typical strings.
```

Do not blindly use `type="number"`. Read [numeric inputs](references/numeric-inputs.md) for keyboard, `inputMode`, decimal, currency, percentage, and precision decisions.

### What kind of date/time is it?

```text
Date-only fact (birthday, due date)? → date-only domain value; avoid timestamp conversion.
Instant on a global timeline?        → timestamp plus an explicit display timezone strategy.
User-local appointment?             → model local date/time and timezone requirements explicitly.
```

Never silently mix these concepts. Read [dates and time](references/dates-and-time.md).

### When should validation run?

```text
Security or business invariant? → always at the trusted server boundary.
Immediate typo worth catching?  → client feedback after interaction may help.
Expensive remote check?         → blur/debounce where appropriate; handle races and recheck on submit.
Submission-wide dependency?     → validate on submit and return structured errors.
```

Read [validation architecture](references/validation.md) and [security](references/security.md).

### Which choice control?

```text
Small fixed list       → native select or radio group.
Large searchable list → accessible combobox/autocomplete, preferably the existing one.
Independent boolean   → checkbox; switch only when it represents an immediate on/off setting.
Multiple choices      → checkbox group or accessible multi-select based on scale and workflow.
```

Read [select and combobox](references/select-combobox.md).

## Non-negotiable rules

- Use explicit labels. A placeholder is never the only label.
- Do not remove focus indicators, disable paste, block password managers, or invent hostile confirmation behavior.
- Use native semantics before ARIA; use `aria-invalid` and `aria-describedby` to connect errors/help when needed.
- Do not clear a form after an error. Disabled and readonly values are not trustworthy authority.
- Use `type=email`, `tel`, `url`, `password`, `search`, and appropriate date controls when their semantics fit, while still applying server validation and domain rules.
- Do not use strict RFC-style email regexes, ASCII-only name regexes, fixed global phone regexes, or locale-blind regex parsing.
- Do not normalize controlled input so aggressively that `-`, `.`, `1.`, selection, paste, autofill, or IME composition becomes unusable.
- Do not hardcode a date order, decimal separator, currency symbol, or language. Prefer `Intl.NumberFormat` and `Intl.DateTimeFormat` for display.
- Treat uploaded type, filename, size, and metadata as untrusted; validate and protect files server-side.
- Distinguish validation (is the value acceptable?) from sanitization/encoding (is it safe for a particular sink?). Authorization is separate from both.

## Reference routing

Read only what the current task needs:

- Text, textarea, email, username, search, URL, Unicode, trimming, length, counters: [text inputs](references/text-inputs.md)
- Integers, decimals, money, percentages, quantities, IDs, phone, parsing: [numeric inputs](references/numeric-inputs.md)
- Date, datetime, time, month, year, ranges, pickers, timezone: [dates and time](references/dates-and-time.md)
- Active locale, formatting/parsing, translation, Arabic/RTL: [locale and i18n](references/locale-and-i18n.md)
- Validation layers, timing, errors, controlled inputs: [validation](references/validation.md)
- Labels, groups, keyboard, focus, announcements, disabled/readonly: [accessibility](references/accessibility.md)
- Mobile keyboards, touch, paste, autofill: [mobile inputs](references/mobile-inputs.md)
- Trust boundaries, authorization, mass assignment, URLs, hidden values: [security](references/security.md)
- File/image inputs and upload states: [file uploads](references/file-uploads.md)
- Select, radio, checkbox, multi-select, autocomplete, combobox: [select and combobox](references/select-combobox.md)
- Passwords, confirmation, managers, show/hide: [passwords](references/passwords.md)
- Boundary, integration, locale, timezone, and failure tests: [testing](references/testing.md)
- Repository-wide discovery, severity, and report format: [form audit](references/form-audit.md)

Compact implementation examples live under [`examples/`](examples/). Treat them as reasoning examples, not framework mandates.

## Completion checklist

- The domain meaning and all value transformations are explicit; leading zeros, precision, date-only values, and Unicode survive correctly.
- Semantic type/control, label, help, error association, autocomplete, keyboard, focus, mobile input, and RTL behavior are appropriate.
- Client feedback is humane, localized, and matched by authoritative server enforcement and relevant database constraints.
- Submission loading, duplicate prevention, success, server/network failure, retry, and state preservation work.
- Boundary, paste/autofill, locale/decimal, timezone, keyboard, and client/server mismatch cases were tested in proportion to risk.
- No unnecessary dependency, abstraction, global rewrite, arbitrary rule, placeholder TODO, or unrelated change was introduced.

