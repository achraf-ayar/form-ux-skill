# Dates and time

Model the temporal concept before choosing a control.

## Domain categories

- **Date only:** birthday, holiday, billing due date. Store a calendar date such as `2026-09-01` without converting it into an instant.
- **Instant:** an event that happened on the global timeline. Store a timestamp/instant and choose a timezone for display.
- **Local date and time:** a future appointment whose wall-clock time matters. Record the applicable IANA timezone or an explicit business timezone strategy.
- **Time only:** store hours/minutes/seconds only when the date and timezone are genuinely irrelevant.
- **Month:** `YYYY-MM` is often an appropriate machine representation.
- **Year:** there is no native `type="year"`; use a bounded control matching the range and task.

Never silently convert between these categories.

## Native controls and pickers

Use `type="date"`, `datetime-local`, `time`, or `month` when browser support, required granularity, locale presentation, and accessibility fit. The browser normally localizes the visible UI even though submitted date/month values have standardized forms. Do not write a placeholder such as `MM/DD/YYYY` and assume it matches the user.

Before adding a custom picker:

1. Inspect the project's component/design library and date dependencies.
2. Prefer its established accessible picker when it meets the requirement.
3. Verify keyboard navigation, focus management, labels/instructions, locale, disabled dates, and min/max behavior.
4. Allow manual entry when it improves efficiency and can be parsed unambiguously.
5. Do not create a calendar from generic divs or add a second date library for convenience.

Native `min`/`max` and picker disabled dates are feedback; repeat date boundaries and dynamic business limits on the server.

## The JavaScript date-only trap

This is unsafe when the domain is date-only:

```ts
// Bad: parsing/formatting through an instant can display the previous/next day by timezone.
const birthday = new Date("2026-09-01");
```

Keep an ISO calendar-date string or the project's date-only type and validate its calendar components without converting through a timestamp. When a `Date` is unavoidable, document whether construction is local or UTC and test zones on both sides of UTC.

## Locale and storage

Separate display from storage:

```text
Display (fr-FR): 01/09/2026
Domain date:      2026-09-01
```

Use `Intl.DateTimeFormat(activeLocale, options)` for display when the project's i18n layer does not already wrap it. Never assume `MM/DD/YYYY`; `en-US`, `en-GB`, `fr-FR`, and `ar-MA` have different conventions and numerals/calendar preferences may vary.

Do not parse an ambiguous localized string by splitting on `/`. Prefer native standardized values, a locale-aware existing parser, or an explicit component-based control. ISO-like machine representations are useful at boundaries, but an ISO timestamp is not a substitute for a date-only value.

## Boundaries and ranges

- Birthday: reject impossible dates and inappropriate future values on the server; choose a plausible minimum only when the product/domain defines one.
- Historical/future-only: compare in the business timezone and state whether today is allowed.
- Date range: define inclusive endpoints, ordering, maximum duration, disabled gaps, and what happens when one endpoint changes.
- Datetime: define seconds precision, daylight-saving gaps/overlaps, and timezone ownership.
- Dynamic limit: recalculate server-side at submission; client limits can become stale.

Use calendar-aware arithmetic for days/months/years. Adding milliseconds is not reliably equivalent to adding a calendar day across daylight-saving transitions.

## Error and testing concerns

Give errors in the user's language and in terms of the task: “Choose a checkout date on or after 1 September 2026.” Retain entered values. Test month/year boundaries, leap days, invalid dates, DST transitions where relevant, UTC− and UTC+ zones, localized display, and client/server timezone disagreement.

