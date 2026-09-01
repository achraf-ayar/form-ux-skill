# Localized number: format and parse are separate

Formatting is standardized:

```ts
export function formatMoney(amount: number, locale: string, currency: string) {
  return new Intl.NumberFormat(locale, { style: "currency", currency }).format(amount);
}

formatMoney(1234.5, "en-US", "USD");
formatMoney(1234.5, "fr-FR", "EUR");
formatMoney(1234.5, "ar-MA", "MAD");
```

There is no standard `Intl` inverse parser. Prefer the project's existing locale parser. If its supported locales use decimal digits, an explicit parser can derive separators and digit glyphs rather than guessing:

```ts
export function parseLocalizedDecimal(input: string, locale: string): number | null {
  const formatter = new Intl.NumberFormat(locale, { useGrouping: true });
  const parts = formatter.formatToParts(-12345.6);
  const group = parts.find((part) => part.type === "group")?.value;
  const decimal = parts.find((part) => part.type === "decimal")?.value;
  const minus = parts.find((part) => part.type === "minusSign")?.value ?? "-";
  const digits = new Map(
    Array.from({ length: 10 }, (_, digit) => [
      new Intl.NumberFormat(locale, { useGrouping: false }).format(digit),
      String(digit),
    ]),
  );

  let normalized = input.trim();
  for (const [localized, ascii] of digits) normalized = normalized.split(localized).join(ascii);
  normalized = normalized.replace(/[\s\u00a0\u202f]/gu, "");
  if (group) normalized = normalized.split(group).join("");
  if (decimal) normalized = normalized.split(decimal).join(".");
  if (minus !== "-") normalized = normalized.split(minus).join("-");

  if (!/^[+-]?(?:\d+(?:\.\d*)?|\.\d+)$/u.test(normalized)) return null;
  const value = Number(normalized);
  return Number.isFinite(value) ? value : null;
}
```

This remains a scoped example, not a universal parser: it deliberately rejects currency symbols, percentages, accounting signs, exponents, and ambiguous mixed separators. Money should use the project's decimal/minor-unit model rather than binary floating point. Test every supported locale and domain policy.

