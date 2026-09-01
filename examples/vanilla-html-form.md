# Plain HTML form: use the platform

Native controls can provide semantics, mobile keyboards, autocomplete, and constraint feedback without a form framework.

```html
<form method="post" action="/account" id="account-form">
  <div>
    <label for="email">Email address</label>
    <input
      id="email"
      name="email"
      type="email"
      autocomplete="email"
      maxlength="254"
      aria-describedby="email-help"
      required
    >
    <p id="email-help">We use this for account notices.</p>
  </div>

  <fieldset>
    <legend>Contact preference</legend>
    <label><input type="radio" name="contact" value="email" required> Email</label>
    <label><input type="radio" name="contact" value="phone"> Phone</label>
  </fieldset>

  <div>
    <label for="phone">Phone number <span>(optional)</span></label>
    <input id="phone" name="phone" type="tel" inputmode="tel" autocomplete="tel">
  </div>

  <button type="submit">Save account</button>
</form>
```

```js
const form = document.querySelector("#account-form");

form.addEventListener("submit", () => {
  const button = form.querySelector('button[type="submit"]');
  button.disabled = true;
  button.textContent = "Saving…";
  // Normal navigation restores the page. If using fetch, re-enable on every failure
  // and preserve inputs; the server must still validate and authorize all fields.
});
```

Do not assume this client handler prevents all duplicate requests; consequential server operations need idempotency/transaction protection. `maxlength=254` is illustrative only if it matches the application's email contract.

