# New form checklist

## Discover

- [ ] Identify framework/rendering model, reusable form components, design system, form library, and validation solution.
- [ ] Trace the trusted server handler, domain service, authorization, API contract, and persistence constraints.
- [ ] Identify active locales, translations, `lang`/`dir`, timezone strategy, and existing `Intl`/date utilities.
- [ ] Confirm the domain schema and existing tests; do not invent constraints or dependencies.

## Field contract

- [ ] For every field, record domain type, semantic control, requiredness, length/range/date/business limits, accepted temporary states, and server rule.
- [ ] Record raw, display, normalized, domain, and serialized values wherever they differ.
- [ ] Treat digit-only identifiers, phone, postal, account, document, OTP, and PIN values as strings unless arithmetic is meaningful.
- [ ] Define locale parsing/formatting, currency code/precision, date/time category, and timezone ownership.
- [ ] Choose intentional labels, help, autocomplete, `inputMode`, and localized error copy.

## Implement

- [ ] Prefer native semantics or the project's existing accessible component; avoid unnecessary abstraction/package changes.
- [ ] Preserve typing, selection, paste, autofill, IME composition, and temporary numeric states.
- [ ] Provide explicit labels, group semantics, help/error associations, visible focus, keyboard behavior, and correct DOM order.
- [ ] Enforce important rules at the server boundary and relevant database layer; authorize separately.
- [ ] Preserve values on failure and handle loading, duplicate submission, success, field/form/network errors, and retry.

## Verify

- [ ] Test empty and boundary values, Unicode, locale decimals/dates, timezone behavior, and leading zeros.
- [ ] Test keyboard, mobile semantics where possible, paste/autofill, server bypass/mismatch, double submit, and recovery.
- [ ] Run focused tests plus typecheck/lint/build as available and report unverified behavior.

