# Numeric inputs, money, phone, and identifiers

The first decision is domain type, not keyboard layout.

## Number or string?

Use a numeric domain type when arithmetic, numeric comparison, or numeric measurement is meaningful: quantity, percentage, age, year within a defined range, measurement, or amount. Use a string when exact characters and leading zeros matter and arithmetic does not: phone, postal code, employee ID, invoice number, account reference, credit-like identifier, document number, OTP, or PIN.

`type="number"` is not a generic “digits only” control. It may accept exponent syntax, applies browser-specific stepping, exposes spinner behavior, and cannot preserve leading zeros. Its suitability depends on the field and locale/input experience.

## Control selection

| Meaning | Typical control | Notes |
|---|---|---|
| Integer quantity | `type="number"` or text + `inputmode="numeric"` | Choose based on stepping, browser behavior, and parsing needs. |
| Locale-entered decimal | often text + `inputmode="decimal"` | Native number inputs do not provide a universal localized parsing contract. |
| Phone | `type="tel"`, `inputmode="tel"` | String; permit `+`, spaces, parentheses, and country-aware formats as appropriate. |
| Postal/identifier/PIN/OTP | text + appropriate `inputmode` | String; `pattern` may hint a truly digit-only grammar. Do not parse numerically. |
| Currency | text/decimal control or established money component | Carry a currency code; parse locale-aware input separately from formatting. |

`inputMode` changes the virtual keyboard, not validity. `pattern` can participate in constraint validation for text-like inputs but must express a real grammar and must be repeated server-side.

## Ranges, sign, zero, and steps

Define each independently:

- integer versus decimal;
- whether zero is allowed;
- whether negative values are allowed;
- inclusive minimum and maximum values;
- precision/scale and rounding policy;
- increment behavior (`step`) versus valid values;
- dynamic/business limits, such as remaining inventory or account balance.

HTML `min`, `max`, and `step` improve native feedback but do not replace server checks. Do not use `step="any"` as a substitute for a precision policy. A price requiring two decimal places is a domain/storage decision, and some currencies use zero or three minor digits. Binary floating point is unsuitable for exact financial arithmetic; follow the project's integer-minor-unit, decimal, or money type.

## Parsing and temporary input

Parsing is distinct from formatting. `parseFloat("1.234,50")` does not parse a German/French-style amount correctly, and deleting punctuation with regex can change magnitude. If the project accepts localized numeric input, use its established locale parser or implement an explicit parser informed by locale and validate the complete string. `Intl.NumberFormat` formats; it does not provide a standard inverse parser.

Controlled inputs must allow temporary states such as:

```text
empty   -   .   ,   1.   1,
```

where appropriate for locale and sign. Keep an editing string separate from the parsed domain number. Parse on blur or submit, or maintain both states without rewriting the value/cursor on every keystroke. Respect selection, paste, autofill, and composition events.

## Currency and percentage

A money value comprises amount plus currency, not a symbol-prefixed number. Never hardcode `$`.

```ts
new Intl.NumberFormat(locale, {
  style: "currency",
  currency: "MAD", // or USD/EUR from the domain value
}).format(amount);
```

Displayed `€1.234,50` may map to domain decimal `1234.50`. Define whether a percentage input `12.5` means domain `12.5` or fraction `0.125`; label it and serialize consistently. Format using `Intl.NumberFormat` with `style: "percent"` only after confirming the domain convention.

## Phone numbers

Phones are strings. Preserve international `+`, country code, extension if supported, and significant leading zeros. Formatting may improve display, but keep a clear raw/normalized/domain policy and never use `Number(value)`.

Do not assume every country's number has one fixed length. If country selection changes validation, make the relationship accessible and validate with a maintained existing solution or appropriately broad server rules. Normalization such as `+212 6 12 34 56 78` → `+212612345678` is appropriate only when the service contract expects that canonical form and extensions/local dialing rules are handled.

## Ages, years, and IDs

- Age is a number only if the form truly asks for age; for long-lived records, birth date often models the fact better because age changes.
- A year can use a bounded numeric field or a purpose-built control. HTML has no `type="year"`; avoid pretending `type="date"` is year-only.
- Credit-card-like values and account references are strings and sensitive data. Preserve leading zeros, minimize collection, and follow applicable security/compliance requirements.

