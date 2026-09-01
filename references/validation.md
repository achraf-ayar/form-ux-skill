# Validation architecture and interaction

Validation has layers with different responsibilities.

## Layers

1. **HTML constraint validation:** required, type, length, range, and pattern provide browser semantics and immediate feedback where suitable.
2. **Client schema/form validation:** improves feedback, handles cross-field UI state, and can share definitions with the server.
3. **Business validation:** enforces contextual rules such as inventory, uniqueness, eligibility, and permissions at a trusted boundary.
4. **Server validation:** parses untrusted input, applies invariants, and returns structured safe errors. It is mandatory for important rules.
5. **Database constraints:** protect durable integrity and resolve races for uniqueness/references/ranges where supported.

Client validation is not security. Even a shared schema is safe only if the server actually runs it on untrusted input. Authorization is separate.

Reuse the project's Zod, Valibot, Yup, Joi, framework validation, or custom approach. Do not add a validator just to mirror this guide. Prevent client/server drift by sharing safe schemas where the architecture supports it or by testing contract equivalence.

## Timing

- Do not show errors on untouched fields merely because the page rendered.
- On submit, show all relevant errors and move focus/navigation to the first useful target; large forms may need a linked error summary.
- After a failed submit, validate edited fields as the user corrects them when that reduces repeated failures.
- On blur suits fields whose complete value is needed. Real-time validation suits counters, password requirement progress, or immediately correctable syntax—not remote or noisy checks.
- Debounce remote checks, handle aborted/stale responses, and revalidate on submit because availability can change.

Avoid success indicators that announce every keystroke. Do not rely on color alone.

## Error design

An error says what to fix and, when useful, the allowed range:

```text
Bad:  Invalid value
Good: Enter a quantity between 1 and 100.
```

Associate field errors using `aria-describedby` and set `aria-invalid="true"` while invalid. Render an error region in a way compatible with the project's accessibility pattern; `role="alert"` can be appropriate for newly inserted submission errors but becomes disruptive if every keystroke announces. Keep stable IDs.

Map server errors by stable field keys/codes, include a form-level fallback, and never display raw exceptions. If the server rejects a value the client accepted, preserve input and show the server result rather than resetting the form.

## Cross-field and dynamic validation

Password confirmation, date ranges, dependent selects, and totals require form-level rules. Attach the error to the field the user should change or to a group, and make the dependency understandable. Dynamic limits can become stale: update client hints when possible but enforce the current rule atomically on the server.

## Controlled and uncontrolled inputs

Use the project's normal pattern. Controlled inputs are useful when UI depends on current state; uncontrolled/native form data can be simpler and reduce rerenders. Neither is inherently more valid.

For formatted or locale-sensitive controls:

- retain an editing string separate from parsed domain data;
- allow temporary invalid states (`-`, `1.`, empty) while typing;
- avoid rewriting values and moving the cursor after each keystroke;
- do not transform during IME composition;
- support paste, selection, undo, autofill, and browser restoration.

Normalize at a deliberate boundary. Validate the normalized/domain value while redisplaying a safe, understandable editing value.

## Submission and recovery

On submit, communicate loading, prevent accidental duplicate mutation, and keep the button label understandable (for example, visible label plus progress indication). Do not cause layout shift. Decide whether cancellation or navigation is safe.

Handle separately:

- field/business errors (`4xx`-style outcome);
- authentication/authorization failure;
- rate limiting;
- server fault;
- network/offline failure;
- success, redirect, or partial success.

Retry only when safe; use idempotency or server protections for consequential mutations. Optimistic UI is appropriate only when rollback and conflict behavior are clear. Preserve non-sensitive form state on failure and avoid resubmitting already uploaded large files without explanation.

