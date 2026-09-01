# Password fields

Password UX must support users and password managers while enforcing the product's actual authentication policy.

## Markup and autocomplete

Use `type="password"` and stable names/labels:

- sign-in password: `autocomplete="current-password"`;
- account creation/reset: `autocomplete="new-password"`;
- username/email alongside sign-in: the appropriate identity autocomplete token.

Do not disable paste, autofill, or password-manager behavior. Do not split passwords into segments or impose arbitrary composition rules. Avoid low maximum lengths that reject generated passphrases; enforce a server limit high enough for legitimate use and bounded enough for resource protection.

## Policy and feedback

State real minimum/maximum and compromised-password or business rules. Prefer length and breached-password defenses over forcing predictable uppercase/lowercase/symbol recipes unless policy requires them. Server enforcement is authoritative. Do not trim or silently normalize passwords; exact code points usually matter.

A strength meter is optional. Add it only if it gives actionable, accurate feedback and does not imply a weak password is acceptable. Requirement progress can update live without announcing every keystroke. Localize all guidance.

## Show/hide

A reveal control should be a button with an accessible, state-aware label such as “Show password” / “Hide password.” Preserve focus, selection, value, autocomplete, and submission behavior when toggling type. Do not reveal by default or leave the state ambiguous.

## Confirmation

Confirmation can catch typing mistakes for a new password, but password managers and reveal functionality may reduce the need. If used, allow paste and compare exactly. Report mismatch on the confirmation field after useful interaction or submit; do not erase either value. Never ask users to confirm a current password merely as a generic UI pattern unless reauthentication is a security requirement.

Do not redisplay password values returned by a server, store them in logs/analytics, or serialize them into URLs/local storage. Clear them after successful completion or when security context requires reentry, while preserving non-secret fields on other failures.

