# Locale, language, and direction

Locale is application state, not a guess from one field.

## Discover the active context

Inspect, in order of project authority:

- locale route/parameter and user preference;
- application i18n provider/configuration and translation catalogs;
- existing locale/context utilities and `Intl` wrappers;
- root/document `lang` and `dir`;
- server locale propagation and API contracts.

Do not hardcode a locale in a reusable field. Ensure `html lang` reflects content language and direction is set at the correct container/document boundary. Content can require local direction overrides, but avoid blanket `dir` changes on every input.

## Formatting versus parsing

Prefer standards for display:

```ts
const amountText = new Intl.NumberFormat(locale, {
  style: "currency",
  currency,
}).format(amount);

const dateText = new Intl.DateTimeFormat(locale, {
  dateStyle: "medium",
  timeZone: displayTimeZone,
}).format(instant);
```

`Intl` formatting is not an inverse parser. Do not remove `,` or `.` blindly: separators have different meanings by locale, and spaces may be grouping characters. Use the existing locale-aware parsing solution or accept an unformatted editing representation with clear locale guidance. Define the domain value independently from its presentation.

Carry ISO 4217 currency codes such as `USD`, `EUR`, and `MAD`. Let formatting determine symbol, placement, spacing, and minor-unit display. Never infer currency solely from locale when accounts or transactions can use multiple currencies.

## Translation

Localize labels, hints, validation messages, error summaries, status text, file constraints, date-picker text, and accessible names. Avoid building sentences from fragments whose grammar or word order will not translate. Prefer messages with parameters:

```text
quantity.range(min: 1, max: 100)
```

Format inserted numbers/dates in the active locale. Server validation should return stable error codes plus safe parameters when the client owns translation, or already-localized messages under an explicit server locale strategy. Do not expose raw validator/database messages.

## RTL and Arabic

For Arabic and other RTL interfaces:

- verify label/control/error alignment, logical CSS properties, icon order, clear/reveal buttons, and popover placement;
- use start/end rather than hardcoded left/right where direction should mirror;
- test keyboard focus and selection visually;
- do not blindly force numeric, phone, URL, email, or code content to RTL. These often benefit from an explicit LTR or auto direction inside an RTL layout while labels remain RTL;
- keep plus signs, country codes, decimal/group separators, and mixed-script values readable;
- ensure date-picker language and navigation behavior are usable, not merely mirrored.

`ar-MA` does not justify assumptions about one numeral system, calendar, or date pattern. Let the active application locale/configuration and `Intl` determine presentation, then test the actual supported environment.

## Locale checklist

- Display and parser agree on separators and digits.
- Date-only values never travel through unintended timezone conversion.
- Currency code is domain data, not a hardcoded symbol.
- Messages and ARIA text are translated.
- `lang`/`dir` and component layout match the active context.
- Unicode text, paste, autofill, and IME composition survive.
- Client and server receive the same locale/timezone context where needed.

