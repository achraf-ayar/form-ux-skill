# Form security and trust boundaries

Treat every client-supplied value as untrusted, including hidden, disabled, readonly, prefilled, query-derived, and framework-generated fields.

## Separate controls

- **Validation** decides whether a value conforms to its type and business rules.
- **Normalization** produces a documented canonical representation where appropriate.
- **Sanitization/encoding** makes data safe for a specific sink or removes disallowed content under an explicit policy.
- **Authorization** decides whether this actor may perform this action on this resource.

One does not replace another. “Sanitize input” is not a complete security strategy; validate structured data and encode output for its context.

## Server boundary

At the trusted handler/service boundary:

1. Parse only expected fields and types.
2. Reject unknown or immutable fields where mass assignment is possible; map an explicit allowlist into domain commands.
3. Apply length/range/format and cross-field rules before expensive work.
4. Recompute prices, ownership, roles, limits, and derived values; never trust hidden fields for authority or secrets.
5. Authorize the actor and resource separately from shape validation.
6. Use safe database/API primitives, persistence constraints, and transaction semantics.
7. Return safe error codes/messages without leaking internals or sensitive existence information.

Client-side schemas remain useful UX, but an attacker can bypass them.

## Specific risks

- **URLs:** allow expected schemes and, for server-side fetches/redirects, defend against SSRF/open redirect and unsafe protocols using the project's security layer.
- **Identifiers:** validate type/shape, then authorize access. An opaque or valid ID is not proof of permission.
- **Passwords/secrets:** never log or redisplay them; use established password hashing/authentication services. Hidden inputs must not contain secrets.
- **Uploads:** enforce size/type/count on the server, generate safe storage names, isolate scanning/processing, restrict public access, and guard image/document parsers. See [file uploads](file-uploads.md).
- **Rich text/HTML:** use an established sanitizer configured for the output context; plain text should be rendered as text, not HTML.
- **CSRF/replay/rate limits:** use framework protections and appropriate idempotency/rate limiting for the operation.

## Error handling and telemetry

Do not expose stack traces, database messages, validation implementation details, tokens, or sensitive values. Logs should retain enough correlation for diagnosis while redacting credentials, payment-like data, personal documents, OTPs, and unnecessary personal data.

Security-sensitive responses may intentionally avoid confirming whether an email/account exists. Keep the message useful without weakening the policy.

