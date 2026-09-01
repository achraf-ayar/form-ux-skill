# React form: preserve editing state

This example shows why a locale-sensitive decimal should remain a string while the user edits. It assumes the application already provides `parseLocalizedDecimal`; see [localized number](localized-number.md).

```tsx
import { FormEvent, useState } from "react";
import { parseLocalizedDecimal } from "@/lib/numbers"; // existing project utility

type Props = {
  locale: string;
  saveQuantity: (quantity: number) => Promise<void>;
};

export function QuantityForm({ locale, saveQuantity }: Props) {
  const [editingValue, setEditingValue] = useState("");
  const [error, setError] = useState<string>();
  const [pending, setPending] = useState(false);

  async function submit(event: FormEvent<HTMLFormElement>) {
    event.preventDefault();
    const quantity = parseLocalizedDecimal(editingValue, locale);

    if (quantity === null || !Number.isInteger(quantity) || quantity < 1 || quantity > 100) {
      setError("Enter a whole-number quantity between 1 and 100."); // use i18n in production
      return;
    }

    setPending(true);
    setError(undefined);
    try {
      await saveQuantity(quantity); // the trusted server validates 1–100 again
    } catch {
      setError("We could not save the quantity. Your entry is still here; try again.");
    } finally {
      setPending(false);
    }
  }

  return (
    <form onSubmit={submit} noValidate>
      <label htmlFor="quantity">Quantity</label>
      <input
        id="quantity"
        name="quantity"
        type="text"
        inputMode="numeric"
        value={editingValue}
        onChange={(event) => setEditingValue(event.target.value)}
        aria-invalid={error ? true : undefined}
        aria-describedby={error ? "quantity-help quantity-error" : "quantity-help"}
      />
      <p id="quantity-help">Choose 1 to 100.</p>
      {error && <p id="quantity-error">{error}</p>}
      <button type="submit" disabled={pending}>
        {pending ? "Saving…" : "Save quantity"}
      </button>
    </form>
  );
}
```

In a real project, use its translation layer, error component, pending pattern, and server API. `noValidate` is optional: if used for custom messaging, retain semantic attributes and do not lose server enforcement. Do not clear `editingValue` on failure.

