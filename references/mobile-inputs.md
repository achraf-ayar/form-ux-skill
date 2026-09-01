# Mobile input, paste, and autofill

Mobile keyboard choice materially affects completion speed. Choose semantics and keyboard hints independently:

| Task | HTML type | `inputmode` |
|---|---|---|
| Email | `email` | usually `email` (often implied) |
| Telephone | `tel` | `tel` |
| URL | `url` | `url` |
| Search | `search` | `search` |
| Digit-only string/quantity | text or number by semantics | `numeric` |
| Locale decimal editing | often text | `decimal` |

`inputmode` is a hint, not validation. Some keyboards omit characters such as minus or decimal separators; test supported devices and retain a way to enter every valid value. Do not set `numeric` for a phone that needs `+` or extension characters; prefer `tel`.

## Interaction layout

- Keep controls and actions large enough to target reliably and spaced to avoid accidental activation.
- Do not require hover. Make help, password reveal, date selection, file removal, and clear actions available to touch and keyboard.
- Avoid fixed overlays that hide the focused field or error when the on-screen keyboard opens.
- Use responsive layouts that preserve label/error proximity and logical DOM order.
- Set reasonable `enterkeyhint` only when it improves the flow and does not override expected multiline behavior.

## Paste, autofill, and codes

Do not disable paste for passwords, email/confirmation, OTP/code, PIN, or ordinary fields. Paste reduces errors and supports password managers and assistive workflows. If pasted input contains surrounding whitespace or grouped separators, apply the documented normalization at the correct boundary rather than blocking it.

Use autocomplete tokens deliberately. For one-time codes, `autocomplete="one-time-code"` can help where supported. A single input is often easier to paste and edit than multiple one-character boxes; if the existing design uses segmented boxes, make them behave as one accessible value, support full-code paste, deletion, mobile autofill, and focus changes without traps.

Do not use `autocomplete="off"` reflexively. It is only a hint and often harms users. Password fields should use `current-password` or `new-password` as appropriate.

## Masks on mobile

Masks must survive typing, deletion, selection, paste, autofill, undo, composition, international formats, and screen-reader use. Keep the domain value separate from decoration. Avoid a mask added only for aesthetics; a visible example and normalization on blur/submission may be more robust.

