# Form accessibility

Accessibility is a correctness requirement for every form.

## Names and descriptions

Prefer an explicit association:

```html
<label for="email">Email address</label>
<input id="email" name="email" type="email" aria-describedby="email-help email-error">
<p id="email-help">We will send the receipt here.</p>
<p id="email-error">Enter an email address like name@example.com.</p>
```

Render/connect only applicable descriptions or keep IDs stable according to the framework pattern. A wrapping label is valid, but explicit `for`/`id` often makes component composition clearer. Placeholder text is not a label. Icon-only reveal, clear, upload, or calendar buttons need accessible names that describe the action and current state.

Use native semantics before ARIA. ARIA can expose state; it does not add missing keyboard behavior or validation.

## Groups

Use `<fieldset>` and `<legend>` for related radio buttons, checkbox groups, and groups whose shared question is necessary to understand each option. Keep individual labels. Do not put unrelated layout sections in fieldsets.

For custom comboboxes/date pickers, use a proven project component and test the complete interaction pattern; applying `role="combobox"` to generic markup is insufficient.

## Errors and required state

- Use native `required` where appropriate and also make required/optional status visually understandable.
- Set `aria-invalid="true"` after the field is known invalid, not preemptively.
- Connect help and errors with `aria-describedby` without discarding existing descriptions.
- Announce newly appearing submission errors or an error summary once, without creating repeated alert noise.
- In a summary, link each error to its field when possible.
- Do not rely only on red color, icons, or placeholder changes.

After submit, focus the summary or first invalid field according to form size/context, then ensure the user can navigate normally. On server errors, preserve entered data.

## Keyboard and focus

All controls must work with keyboard alone in a logical DOM/tab order. Do not add positive `tabindex` to repair visual order. Keep visible focus indicators with sufficient contrast; if native outlines are replaced, provide an equally clear `:focus-visible` treatment.

Touch/drag interfaces need keyboard and pointer alternatives. Hover-only hints/actions are insufficient. Modal pickers and popovers must manage opening, closing, Escape, return focus, and outside interaction according to their established pattern without trapping users unexpectedly.

## Disabled and readonly

- `disabled` controls are not focusable in common browser behavior and are normally omitted from form submission. Explain unavailable actions; do not disable the submit button as the only way to communicate errors.
- `readonly` preserves focus/submission for supported text-like inputs but is not an authorization mechanism.
- `aria-disabled` communicates state but does not prevent interaction; code must enforce behavior.

Never trust any of these client states on the server.

## Autocomplete and identity

Use `autocomplete` intentionally: `name`, `given-name`, `family-name`, `email`, `tel`, `organization`, `street-address`, `postal-code`, `country`, `current-password`, and `new-password` are common tokens. Use `name` attributes that are stable and meaningful for submission. Do not fight browser autofill or password managers through fake fields or paste blocking.

## Review methods

Check semantic structure and accessible names, then operate the entire form by keyboard. Verify zoom/reflow, screen-reader announcements when tooling is available, contrast/focus, error recovery, group context, dynamic content, and RTL. Automated checks help but do not prove a custom widget is usable.

