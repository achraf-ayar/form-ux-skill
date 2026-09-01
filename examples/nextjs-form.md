# Next.js form: server action as trust boundary

This compact pattern keeps validation in a server action and returns structured errors. Adapt it to the project's Next.js version, validator, authentication, and design system.

```ts
// app/profile/actions.ts
"use server";

type State = { fieldErrors?: Record<string, string>; formError?: string; ok?: boolean };

export async function updateProfile(_previous: State, data: FormData): Promise<State> {
  const phoneValue = data.get("phone");
  if (typeof phoneValue !== "string") {
    return { fieldErrors: { phone: "Enter a phone number." } };
  }

  const phone = phoneValue.trim(); // replace with the project's country-aware normalization
  if (!phone || phone.length > 40) {
    return { fieldErrors: { phone: "Enter a valid phone number." } };
  }

  const user = await requireAuthenticatedUser();
  await profiles.updatePhone(user.id, phone); // explicit authorized field, not `{...data}`
  return { ok: true };
}
```

```tsx
// app/profile/phone-form.tsx
"use client";

import { useActionState } from "react";
import { updateProfile } from "./actions";

export function PhoneForm() {
  const [state, action, pending] = useActionState(updateProfile, {});
  const error = state.fieldErrors?.phone;

  return (
    <form action={action}>
      <label htmlFor="phone">Phone number</label>
      <input
        id="phone"
        name="phone"
        type="tel"
        inputMode="tel"
        autoComplete="tel"
        aria-invalid={error ? true : undefined}
        aria-describedby={error ? "phone-error" : "phone-help"}
      />
      <p id="phone-help">Include the country code, for example +212…</p>
      {error && <p id="phone-error">{error}</p>}
      {state.formError && <p role="alert">{state.formError}</p>}
      <button disabled={pending}>{pending ? "Saving…" : "Save phone number"}</button>
    </form>
  );
}
```

Important reasoning: phone remains a string, the server repeats validation and authorization, and only an explicit field reaches the domain service. Add localization and a stronger existing phone validator when the product defines supported countries/formats.

