# Skill behavior scenarios

These are decision tests for an AI using the skill, not string-matching unit tests. A reviewer should present the scenario without the expected answer, then compare the agent's reasoning and proposed implementation with the criteria below. Framework syntax may vary; the domain, browser, accessibility, locale, and trust-boundary decisions should not.

## 1. Moroccan phone number

**Scenario:** Add a required mobile field for Moroccan and international customers. A current handler calls `Number(value)`.

**Correct behavior:** Model the phone as a string; use `type="tel"`, `inputmode="tel"`, and `autocomplete="tel"`; preserve `+212` and significant leading zeros; remove numeric conversion; define country-aware normalization separately from display; validate at the server without assuming one global length.

**Regression signal:** Uses `type="number"`, strips `+`, or stores a JavaScript number.

## 2. French EUR amount

**Scenario:** A `fr-FR` invoice form accepts a price displayed as `1 234,50 €`.

**Correct behavior:** Separate editing/display/domain values; carry `EUR`; use locale-aware parsing under an explicit contract and `Intl.NumberFormat` for display; preserve exact money precision using the project's decimal/minor-unit model; repeat bounds server-side.

**Regression signal:** Calls `parseFloat`, deletes punctuation blindly, assumes dot decimal, or hardcodes `€`/`$` into the domain value.

## 3. Invoice number `000123`

**Scenario:** An invoice reference contains digits and leading zeros but is never used in arithmetic.

**Correct behavior:** Keep a string and preserve `000123`; use a text control with `inputmode="numeric"` only if the real grammar is digits-only; validate length/uniqueness server-side.

**Regression signal:** Converts to `123` or recommends `type="number"` solely because it contains digits.

## 4. Birth date

**Scenario:** Collect a birthday for a global user base.

**Correct behavior:** Model a date-only value, typically `YYYY-MM-DD` internally; use locale-appropriate UI/native date control where suitable; reject impossible/inappropriate future dates on the server; avoid converting through a timestamp/UTC instant; test multiple timezones.

**Regression signal:** Assumes `MM/DD/YYYY` or stores `new Date(input).toISOString()` as the birthday.

## 5. Username length 30

**Scenario:** Product policy says usernames are required, unique, and at most 30 characters.

**Correct behavior:** Apply appropriate `required`/`maxlength` client semantics and project validation, explain the rule, enforce normalization/length/uniqueness server-side, and rely on a persistence uniqueness constraint for races. Define Unicode/case policy rather than assuming ASCII.

**Regression signal:** Client-only length/uniqueness or `^[A-Za-z]+$` without a domain requirement.

## 6. Arabic Morocco application

**Scenario:** A profile form runs under `ar-MA` and contains names, phone, price, and date fields.

**Correct behavior:** Inspect active i18n, `lang`, `dir`, translations, and existing formatters; accept Arabic/Unicode text; use locale formatting; verify RTL layout and logical icon placement; keep mixed-direction phone/numeric content readable instead of forcing all values RTL; localize ARIA/error/date-picker text.

**Regression signal:** Treats RTL as `text-align:right` only or forces email/phone/code strings into unreadable RTL order.

## 7. Quantity range 1–100

**Scenario:** An order accepts whole quantities from 1 through 100.

**Correct behavior:** Choose numeric semantics, define integer/inclusive boundaries/zero policy; use `min=1`, `max=100`, `step=1` when `type=number` fits or an equivalent text/inputmode contract; enforce safe integer range server-side; test 0, 1, 50, 100, 101 and malformed values.

**Regression signal:** Relies on HTML attributes as security or forgets decimal/exponent parsing behavior.

## 8. Localized decimal while typing

**Scenario:** A controlled React input accepts negative decimals and reformats on every change.

**Correct behavior:** Keep an editing string separate from parsed numeric data; allow temporary `-`, locale decimal separator, and trailing separator states; defer/reduce formatting so cursor, selection, paste, undo, and IME work; parse and validate at a deliberate boundary.

**Regression signal:** Calls `Number()` and writes the formatted result back on every keystroke.

## 9. Current and new passwords

**Scenario:** Build sign-in and password-reset forms with reveal buttons.

**Correct behavior:** Use password inputs with `current-password` for sign-in and `new-password` for reset; allow paste/managers; enforce real policy server-side; make reveal a state-aware accessible button that preserves selection/value; do not trim passwords or log them.

**Regression signal:** Disables paste, uses `autocomplete="off"`, or imposes arbitrary composition rules not in policy.

## 10. Email confirmation

**Scenario:** A signup form asks users to enter email twice and blocks paste into confirmation.

**Correct behavior:** Remove paste blocking; consider whether confirmation is needed; use `type=email`, trim according to account policy, avoid exhaustive email regex, compare under the documented canonicalization, validate server-side, and use verification for reachability.

**Regression signal:** Preserves anti-paste code or lowercases/deduplicates without an identity policy.

## 11. Image upload

**Scenario:** Users upload one JPG/PNG avatar up to 5 MB through a drag area.

**Correct behavior:** Keep a labeled real file input and click/keyboard fallback; set helpful `accept`; show constraints and selected/error/progress states; validate count/streamed size/signature/decoded content server-side; generate safe storage names, authorize association/access, and handle metadata/privacy.

**Regression signal:** Trusts `file.type`, extension, or drag-and-drop alone.

## 12. Large customer selector

**Scenario:** Choose one of 80,000 customers from an API.

**Correct behavior:** Reuse the project's accessible async combobox; support keyboard/touch, label, loading/empty/error, debounce and stale-response handling, stable selected identity, clear behavior, and pagination; revalidate ID and authorization server-side.

**Regression signal:** Renders a giant custom dropdown or trusts a submitted ID because it appeared in search.

## 13. Small shipping-method list

**Scenario:** Choose among three shipping methods with descriptions.

**Correct behavior:** Prefer a radio fieldset/legend so options can be compared; keep individual labels/descriptions; submit stable values; server rechecks availability and price.

**Regression signal:** Builds an inaccessible custom dropdown solely for styling.

## 14. Date range with dynamic availability

**Scenario:** Hotel check-in/out dates have disabled sold-out days and a maximum stay.

**Correct behavior:** Inspect/reuse the existing accessible picker; preserve locale/manual entry where appropriate; define inclusive/exclusive endpoints and ordering; expose disabled dates; recalculate availability/max stay atomically on submit because client data becomes stale; retain dates after rejection.

**Regression signal:** Treats disabled calendar cells as authoritative or loses the whole form when availability changes.

## 15. User-local appointment

**Scenario:** A clinic schedules 09:00 in the patient's selected timezone months ahead.

**Correct behavior:** Explicitly model local date, local time, and IANA timezone (or a documented clinic-zone policy); handle DST gap/overlap; convert to an instant only with that context; show the timezone and test transitions.

**Regression signal:** Sends `datetime-local` as UTC without timezone context.

## 16. OTP paste and autofill

**Scenario:** A six-digit login code uses six visual boxes.

**Correct behavior:** Keep code as a string; allow full-code paste and `autocomplete="one-time-code"`; consider one input as the simpler accessible control; if segmented, expose coherent naming/focus/deletion and mobile autofill; validate server-side with rate/attempt protections.

**Regression signal:** Converts to a number, blocks paste, or traps focus between boxes.

## 17. Address and postal code

**Scenario:** International checkout currently restricts address to ASCII and postal code to numbers.

**Correct behavior:** Accept Unicode address text; remove naive name/address regex; use autocomplete tokens; keep postal code as a string because formats can include letters/spaces and leading zeros; use country-specific rules only under an explicit country contract and server validation.

**Regression signal:** Applies `^[A-Za-z0-9 ]+$` globally or parses postal code numerically.

## 18. Hidden price and account owner

**Scenario:** Checkout submits hidden `price` and `ownerId`; the server writes the request object directly.

**Correct behavior:** Treat hidden fields as untrusted; allowlist request fields; derive current price and actor/owner on the server; validate product ID then authorize; protect against mass assignment and use transaction/idempotency controls where appropriate.

**Regression signal:** Merely adds client validation to hidden fields.

## 19. URL destination field

**Scenario:** Users configure a post-login redirect URL.

**Correct behavior:** Use URL semantics for feedback, then server-validate the allowed scheme/host/path under an explicit policy; prevent open redirects/unsafe protocols; preserve useful errors; encode safely at use.

**Regression signal:** Considers `type=url` sufficient or accepts `javascript:`/arbitrary external targets.

## 20. Async username availability

**Scenario:** Username availability is checked on every keystroke over the network.

**Correct behavior:** Wait until useful interaction/length, debounce or check on blur, cancel/ignore stale responses, expose checking/error states without noisy alerts, and recheck at submit/persistence because availability races.

**Regression signal:** Treats a green client check as a uniqueness guarantee.

## 21. Server error after long form

**Scenario:** A 20-field application receives a validation error after submission and currently resets.

**Correct behavior:** Preserve non-sensitive entries; map structured server errors to fields; show/focus a linked error summary and first relevant field; keep localized actionable messages; handle form-level and network errors separately.

**Regression signal:** Clears the form, reports only “Invalid,” or focuses unpredictably.

## 22. Percentage semantics

**Scenario:** A discount UI shows `12.5%`, while the API expects `0.125`.

**Correct behavior:** Document editing/display/domain/serialized mapping; label percent units; parse under locale; validate range/precision on client and server; transform exactly once; format from the domain convention with `Intl`.

**Regression signal:** Sometimes stores `12.5` and sometimes `0.125` without an explicit boundary.

## 23. Disabled role selector

**Scenario:** Non-admin users see a disabled role select; the server accepts the submitted `role` field.

**Correct behavior:** Treat disabled state as presentation only and ignore/reject role changes server-side based on actor authorization; use explicit field mapping; explain why unavailable if useful. Remember disabled controls may not submit.

**Regression signal:** Assumes users cannot modify a disabled value outside the UI.

## 24. Search form

**Scenario:** A site search rejects punctuation and requires three characters.

**Correct behavior:** Use `type="search"`, appropriate mobile semantics, a persistent label, and intentionally decide whether empty/short/punctuation queries are meaningful. Do not invent restrictions; encode the query safely and apply server resource limits.

**Regression signal:** Applies a generic alphanumeric regex without a search-domain reason.

## 25. Month/year card expiry

**Scenario:** A form needs an expiry month and year, not a day.

**Correct behavior:** Model month/year explicitly, use `type="month"` when supported and suitable or accessible separate controls, serialize as an unambiguous month value, compare in the correct business timezone/month granularity, and never invent a day then risk timezone shifts.

**Regression signal:** Uses a full date picker and stores an arbitrary first/last day timestamp.

## 26. Multiple files with partial failure

**Scenario:** Three documents upload; one fails virus scanning after the other two succeed.

**Correct behavior:** Show per-file processing status and safe error; retain successful associations; let the user replace/retry the failed file without silently duplicating successes or clearing form data; keep final submission state consistent; authorize worker and download paths.

**Regression signal:** Reports generic total failure and reuploads everything on retry without idempotency.

## 27. Currency across USD, EUR, and MAD

**Scenario:** One account can invoice in USD, EUR, or MAD regardless of UI locale.

**Correct behavior:** Carry selected/authorized ISO currency code independently of locale; determine precision/domain rules from currency/business policy; use locale plus code for display; validate allowed currencies server-side and avoid binary floating point for exact settlement.

**Regression signal:** Infers currency from locale or always prefixes `$`.

## 28. Audit-only request

**Scenario:** The user says, “Review this form and rank the issues,” but does not ask for edits.

**Correct behavior:** Inspect form components, schemas, backend, i18n/design system, and tests; trace values; report evidence with exact paths/lines, severity, impact, and targeted fixes; do not modify files or install dependencies.

**Regression signal:** Rewrites the form architecture during an audit-only request.

## 29. Basic contact form in plain HTML

**Scenario:** A small site needs name, email, message, and submit fields with an existing server endpoint.

**Correct behavior:** Use semantic native labels/inputs/textarea, appropriate types/autocomplete/required/length rules based on actual constraints, server validation, useful errors, loading/duplicate handling, and state preservation. Keep it simple.

**Regression signal:** Introduces a framework, form library, schema package, date library, or elaborate field abstraction.

## 30. Client/server constraint mismatch

**Scenario:** The client accepts quantity 100, but the server silently caps it to 99.

**Correct behavior:** Identify silent mutation as corruption; establish one documented limit, align client/server feedback, reject invalid/stale input rather than silently changing it unless the business explicitly defines clamping and communicates it; add boundary tests.

**Regression signal:** Leaves silent coercion in place because the persisted value is technically valid.

