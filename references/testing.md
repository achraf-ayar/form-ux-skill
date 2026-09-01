# Form testing

Test decisions and boundaries across browser, client, trusted server, and persistence paths. Use the project's existing unit, component, integration, and end-to-end tools; do not add a framework merely to follow this guide.

## Boundary tables

For `maxlength=100`, cover `0`, `1`, `99`, `100`, and `101` characters, plus Unicode grapheme/code-unit cases if the distinction matters.

For inclusive range `1–100`, cover `0`, `1`, a representative middle (`50`), `100`, and `101`. Add empty, whitespace, malformed, decimal, negative, exponent, and very large inputs according to the parser/control.

For dates, test exact min/max, just outside each boundary, leap day, month/year changes, and timezone zones on both sides of UTC. For appointments, include daylight-saving gaps/overlaps when supported regions observe them.

## Coverage dimensions

- required/optional empty state and whitespace-only input;
- minimum/maximum length and numeric/date bounds;
- zero, negative, decimal precision, rounding, and overflow;
- leading zeros in identifiers, postal codes, PINs, and phones;
- Unicode names/addresses, accents, Arabic, CJK, emoji where allowed, and IME composition;
- paste, undo, selection, autofill, password manager, and browser restoration;
- keyboard-only operation, focus visibility/order, error association/announcement, and group context;
- locale decimal/group separators, currency code/symbol placement, percent convention, date order, translated messages, and RTL layout;
- file count/size/type mismatch, spoofed MIME/extension, progress, cancellation, and partial retry;
- empty/loading/error/stale selection for async comboboxes;
- valid client + invalid server, bypassed client, stale business limit, database uniqueness race, and authorization failure;
- loading, accidental double click, slow response, network loss, server error, retry, success, redirect, and back navigation;
- state preservation after every failure class.

## Assertions that matter

Assert the value that reaches the trusted handler and persistence layer, not only the visible string. A phone `+212 6…` must not become a number; invoice `000123` must retain zeros; localized `1.234,50 €` must map to the intended decimal and currency; a date-only birthday must remain the same calendar day in different timezones.

Verify server rejection by calling the server boundary without browser validation. Verify authorization with valid-shaped IDs belonging to another actor. Check client/server messages and constraints remain aligned while accepting that the server can reject stale business state.

## Manual checks

Some qualities require interaction: mobile keyboards, picker usability, screen-reader output, password-manager behavior, zoom/reflow, touch targets, RTL mixed-direction text, and custom widget keyboard patterns. Document environments used and unresolved coverage rather than claiming automated certainty.

