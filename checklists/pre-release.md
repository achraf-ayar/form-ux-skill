# Pre-release form checklist

- [ ] Supported happy paths complete with mouse, touch where available, and keyboard only.
- [ ] Labels, required/optional state, group legends, focus indicators, tab order, help, and error associations are correct.
- [ ] Errors are localized, actionable, announced appropriately, and do not rely on color.
- [ ] Empty, min/max, just-outside bounds, length, zero/negative/precision, and cross-field rules are tested.
- [ ] Unicode, accents, Arabic/CJK, paste, autofill/password manager, undo, selection, and IME work.
- [ ] All supported locales format/parse numbers, percentages, currencies, dates, and messages correctly; RTL/mixed-direction UI is usable.
- [ ] Date-only values do not shift; instant/local appointment timezone and DST behavior match the domain strategy.
- [ ] Phones/identifiers/postal codes retain `+` and leading zeros; money retains currency and exact precision.
- [ ] Client bypass and stale state are rejected by server validation, authorization, and persistence constraints.
- [ ] Hidden/disabled/readonly fields are treated as untrusted; mass assignment, URL, upload, and logging risks are controlled.
- [ ] Loading, double submit, slow/network/server/authorization errors, retry, success, redirect, and back navigation behave safely.
- [ ] User-entered values survive recoverable failures; secrets are not logged or unnecessarily redisplayed.
- [ ] Existing focused tests, integration/e2e tests, typecheck, lint, and build pass as applicable.
- [ ] No unnecessary dependency, duplicated architecture, hardcoded locale/currency/date format, placeholder label, paste blocking, or unfinished TODO remains.

