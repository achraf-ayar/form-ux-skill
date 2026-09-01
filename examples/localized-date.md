# Localized date: keep calendar dates out of timestamps

For a date-only domain fact, retain the machine value and format components without `new Date("YYYY-MM-DD")`:

```ts
type CalendarDate = `${number}-${number}-${number}`;

export function parseCalendarDate(value: string): CalendarDate | null {
  const match = /^(\d{4})-(\d{2})-(\d{2})$/.exec(value);
  if (!match) return null;

  const [, y, m, d] = match;
  const year = Number(y);
  const month = Number(m);
  const day = Number(d);
  const check = new Date(Date.UTC(year, month - 1, day));

  if (
    check.getUTCFullYear() !== year ||
    check.getUTCMonth() !== month - 1 ||
    check.getUTCDate() !== day
  ) return null;

  return value as CalendarDate;
}

export function formatCalendarDate(value: CalendarDate, locale: string) {
  const [year, month, day] = value.split("-").map(Number);
  // Components are intentionally constructed at UTC noon and formatted in UTC
  // solely to use Intl for a calendar-date display without zone rollover.
  const carrier = new Date(Date.UTC(year, month - 1, day, 12));
  return new Intl.DateTimeFormat(locale, {
    year: "numeric",
    month: "2-digit",
    day: "2-digit",
    timeZone: "UTC",
  }).format(carrier);
}
```

Prefer a project/library date-only type when available. The carrier `Date` above is not stored as the domain value. For instants, instead parse/store a timestamp and format it with the user's explicit display timezone.

