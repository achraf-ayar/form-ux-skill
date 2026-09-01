# Text inputs

Use this guide for text, textarea, email, username, URL, and search fields.

## Establish the contract

Decide whether empty, absent, and whitespace-only values mean the same thing. Requiredness is a domain rule, not a visual convention. Reflect it in HTML for feedback and accessibility, then enforce it on the server.

Define what is counted for length. JavaScript string length counts UTF-16 code units, not user-perceived characters; database and validator limits may count differently. For limits involving emoji or combining characters, align client, server, and storage deliberately. Do not invent a maximum merely because a field looks short.

Use `minlength`/`maxlength` when the constraint is real and the browser's counting behavior matches it. A character counter helps only when the limit matters; connect it as help text, update it without noisy announcements, and communicate the unit. Avoid hard blocking when a soft editorial target is intended.

## Labels, help, and placeholders

- Give every control a persistent accessible name, normally an explicit `<label for>`.
- Use a placeholder for an example or format hint only when useful. It disappears during entry and must not carry required instructions.
- Put enduring constraints in helper text and connect it with `aria-describedby`.
- Mark optional/required state consistently with the application. Native `required` conveys semantics; visible text avoids ambiguity.
- Use `<textarea>` for multiline content. Do not simulate it with `contenteditable` unless rich text is a real requirement and the project has an accessible editor.

## Whitespace and normalization

Preserve what the user is typing. Normalize at a defined boundary—usually on blur for display-only cleanup or immediately before validation/submission—not on every keystroke.

Possible policies differ by field:

- email/search: leading and trailing whitespace is usually accidental;
- username: trim only if the product defines surrounding whitespace as insignificant;
- legal name/address/free text: internal and sometimes surrounding whitespace may be meaningful;
- multiline text: preserve line breaks; normalize newline encoding only if the domain/storage contract requires it.

Do not silently collapse all internal whitespace, strip diacritics, transliterate, or change case without a documented domain reason. Record normalization so the server applies the same rule.

## International text

Names, addresses, organizations, and free text must accept Unicode unless the domain explicitly forbids particular characters. This rejects real users:

```js
// Bad: excludes accents, Arabic, CJK, apostrophes, and many valid names.
/^[A-Za-z ]+$/
```

If a field has a restricted machine grammar, validate that grammar and explain it: a URL slug, invitation code, or system username may legitimately be narrower. Prefer an allowlist tied to the protocol/business rule over a vague “special characters” ban. Handle IME composition before triggering transformations or validation.

## Email

Use `type="email"` and usually `autocomplete="email"`. Trim surrounding whitespace according to the product contract. Do not attempt exhaustive RFC validation in the client; browser syntax checking plus a modest project validator is enough for feedback. The server must validate, and verification by sending a message is the authoritative proof of reachability.

Treat case deliberately. Domain parts are case-insensitive; local-part behavior is more nuanced. Do not lowercase or deduplicate entire addresses unless the identity/account policy explicitly defines that canonicalization. Return a useful message such as “Enter an email address like name@example.com,” not “Invalid value.”

## Username, URL, and search

- Username rules are product-specific. Explain allowed characters and length; enforce uniqueness server-side and handle races at persistence.
- Use `type="url"` when a URL is required, but validate permitted schemes/hosts server-side. Never concatenate or render a submitted URL into a dangerous sink without contextual handling.
- Use `type="search"` for a search action when its browser semantics fit. A search query is often valid when short or punctuation-heavy; avoid overvalidation.

## Value lifecycle example

```ts
const raw = formData.get("email");
if (typeof raw !== "string") return fieldError("email", "Enter an email address.");

const normalized = raw.trim();
// Validate `normalized` on the trusted server using the project's existing validator.
```

Keep the user's original form state available for correction; do not redisplay secrets or unsafe unescaped content.

