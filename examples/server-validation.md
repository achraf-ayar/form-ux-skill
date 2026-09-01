# Framework-neutral server validation

This example emphasizes the trusted boundary, explicit mapping, authorization, and structured errors. Replace helpers with the project's existing validator and HTTP framework.

```ts
type FieldErrors = Record<string, string>;

export async function handleCreateOrder(request: Request, actor: Actor): Promise<Response> {
  const payload: unknown = await request.json();
  if (!isRecord(payload)) return json({ formError: "Malformed request." }, 400);

  const errors: FieldErrors = {};
  const quantity = parseBoundedInteger(payload.quantity, 1, 100);
  if (quantity === null) errors.quantity = "Enter a quantity between 1 and 100.";

  const productId = typeof payload.productId === "string" ? payload.productId.trim() : "";
  if (!productId) errors.productId = "Choose a product.";
  if (Object.keys(errors).length) return json({ fieldErrors: errors }, 422);

  const product = await products.findById(productId);
  if (!product || !(await policies.canOrder(actor, product))) {
    return json({ formError: "This product is not available." }, 403);
  }

  // Recheck current inventory and price atomically; never accept them from hidden fields.
  const order = await orders.create({ actorId: actor.id, productId, quantity });
  return json({ id: order.id }, 201);
}

function parseBoundedInteger(value: unknown, min: number, max: number): number | null {
  if (typeof value !== "number" || !Number.isSafeInteger(value)) return null;
  return value >= min && value <= max ? value : null;
}
```

If the wire format sends editing strings, parse the complete string under an explicit locale-independent API contract or an authenticated locale contract. Never spread the payload into a database model; allowlist fields and keep authorization separate from validation.

