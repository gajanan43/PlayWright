# Handle Dialogs, Frames & Inner Frames in Playwright

In Playwright, these are three important topics:

1. **Dialogs** → Alert, Confirm, Prompt
2. **Frames / iFrames** → Interact with elements inside an iframe
3. **Inner Frames / Nested Frames** → An iframe inside another iframe

---

# 1. Handling Dialogs

Browser dialogs are:

| Dialog      | Purpose       | Playwright                             |
| ----------- | ------------- | -------------------------------------- |
| `alert()`   | Shows message | `dialog.accept()`                      |
| `confirm()` | OK / Cancel   | `dialog.accept()` / `dialog.dismiss()` |
| `prompt()`  | Takes input   | `dialog.accept("text")`                |

Playwright automatically dismisses dialogs **if there is no dialog listener**, but when you need to verify or control the dialog, register a handler.

---

## A. Alert

Suppose the application executes:

```javascript
alert("Hello Playwright");
```

Handle it like this:

```ts
import { test, expect } from "@playwright/test";

test("Handle Alert", async ({ page }) => {

    await page.goto("https://example.com");

    page.on("dialog", async dialog => {

        console.log(dialog.type());
        console.log(dialog.message());

        await dialog.accept();
    });

    await page.locator("#alertButton").click();
});
```

### Important

Register the dialog handler **before clicking** the button that opens the alert.

```ts
page.on("dialog", async dialog => {
    await dialog.accept();
});

await page.locator("#alertButton").click();
```

---

# 2. Confirm Dialog

A confirm dialog has:

```javascript
confirm("Are you sure?");
```

You can click **OK**:

```ts
page.on("dialog", async dialog => {
    console.log(dialog.message());

    await dialog.accept();
});
```

Or click **Cancel**:

```ts
page.on("dialog", async dialog => {
    await dialog.dismiss();
});
```

### Example

```ts
test("Handle Confirm", async ({ page }) => {

    await page.goto("https://example.com");

    page.on("dialog", async dialog => {

        expect(dialog.type()).toBe("confirm");
        expect(dialog.message()).toBe("Are you sure?");

        await dialog.dismiss();
    });

    await page.locator("#confirmButton").click();
});
```

---

# 3. Prompt Dialog

A prompt asks the user for input.

JavaScript:

```javascript
prompt("Enter your name");
```

Playwright:

```ts
page.on("dialog", async dialog => {

    console.log(dialog.type());

    await dialog.accept("Gajanan");
});
```

Complete example:

```ts
test("Handle Prompt", async ({ page }) => {

    await page.goto("https://example.com");

    page.on("dialog", async dialog => {

        expect(dialog.type()).toBe("prompt");

        console.log(dialog.message());

        await dialog.accept("Gajanan");
    });

    await page.locator("#promptButton").click();
});
```

---

# 4. `dialog.accept()` vs `dialog.dismiss()`

Remember this:

```text
accept()  → OK
dismiss() → Cancel
```

For prompt:

```ts
await dialog.accept("Gajanan");
```

You can also accept without entering text:

```ts
await dialog.accept();
```

---

# 5. Dialog Methods

The most useful methods are:

```ts
dialog.type()
dialog.message()
dialog.defaultValue()
dialog.accept()
dialog.accept("text")
dialog.dismiss()
```

Example:

```ts
page.on("dialog", async dialog => {

    console.log("Type:", dialog.type());
    console.log("Message:", dialog.message());
    console.log("Default:", dialog.defaultValue());

    await dialog.accept();
});
```

---

# 6. Handling Frames / iFrames

An iframe is an HTML document embedded inside another HTML document.

Example:

```html
<iframe src="login.html">
```

The important point is:

> **You cannot directly use the main page locator to interact with elements inside an iframe.**

You need to switch your locator context to the frame.

---

# 7. Using `frameLocator()`

This is the recommended Playwright approach.

Suppose:

```html
<iframe id="myFrame">
    <input id="username">
    <input id="password">
    <button id="login">Login</button>
</iframe>
```

Use:

```ts
const frame = page.frameLocator("#myFrame");

await frame.locator("#username").fill("admin");
await frame.locator("#password").fill("12345");
await frame.locator("#login").click();
```

Notice:

```ts
page.locator()
```

becomes:

```ts
frame.locator()
```

---

# 8. Complete Frame Example

```ts
import { test, expect } from "@playwright/test";

test("Handle iframe", async ({ page }) => {

    await page.goto("https://example.com");

    const frame = page.frameLocator("#myFrame");

    await frame.locator("#username").fill("admin");

    await frame.locator("#password").fill("12345");

    await frame.locator("#login").click();
});
```

---

# 9. Frame by Name

If your iframe has:

```html
<iframe name="loginFrame">
```

You can use:

```ts
const frame = page.frameLocator('iframe[name="loginFrame"]');

await frame.locator("#username").fill("admin");
```

---

# 10. Frame by URL

Sometimes there is no useful `id` or `name`.

You can locate the iframe using its URL:

```ts
const frame = page.frameLocator(
    'iframe[src*="login"]'
);

await frame.locator("#username").fill("admin");
```

This is often better than using a long XPath.

---

# 11. Getting a `Frame` Object

Playwright also provides:

```ts
page.frames()
```

Example:

```ts
const frames = page.frames();

for (const frame of frames) {
    console.log(frame.url());
}
```

You can also find a specific frame:

```ts
const frame = page.frame({
    name: "loginFrame"
});
```

Then:

```ts
await frame.locator("#username").fill("admin");
```

### `frameLocator()` vs `page.frame()`

For normal UI automation, prefer:

```ts
page.frameLocator("iframe")
```

because it provides locator-based interaction and Playwright's normal auto-waiting behavior.

---

# 12. Inner Frames / Nested Frames

This is an important interview concept.

Suppose the structure is:

```text
Main Page
   |
   └── iframe 1
          |
          └── iframe 2
                 |
                 └── input
```

This is called a **nested iframe**.

Example HTML:

```html
<iframe id="outerFrame">

    <iframe id="innerFrame">

        <input id="username">

    </iframe>

</iframe>
```

You need to locate the outer frame first and then the inner frame.

---

# 13. Handling Nested Frames

Using `frameLocator()`:

```ts
const outerFrame = page.frameLocator("#outerFrame");

const innerFrame = outerFrame.frameLocator("#innerFrame");

await innerFrame.locator("#username").fill("Gajanan");
```

This is the cleanest approach.

### Think of it like:

```text
page
 ↓
outerFrame
 ↓
innerFrame
 ↓
element
```

Code:

```ts
page
    .frameLocator("#outerFrame")
    .frameLocator("#innerFrame")
    .locator("#username")
    .fill("Gajanan");
```

---

# 14. Nested Frame Example

```ts
test("Handle Nested iframe", async ({ page }) => {

    await page.goto("https://example.com");

    const outerFrame = page.frameLocator("#outerFrame");

    const innerFrame = outerFrame.frameLocator("#innerFrame");

    await innerFrame
        .locator("#username")
        .fill("Gajanan");

});
```

---

# 15. Important: iframe Inside iframe

If you have:

```html
<iframe id="frame1">

    <iframe id="frame2">

        <button id="submit">Submit</button>

    </iframe>

</iframe>
```

Don't do this:

```ts
page.locator("#submit").click();
```

❌ Wrong because `#submit` belongs to the inner iframe.

Instead:

```ts
await page
    .frameLocator("#frame1")
    .frameLocator("#frame2")
    .locator("#submit")
    .click();
```

✅ Correct.

---

# 16. Frames Inside Frames — Visual Understanding

Think of an iframe as a **separate webpage**.

```text
┌──────────────────────────────┐
│ Main Page                    │
│                              │
│  ┌────────────────────────┐  │
│  │ Outer iframe            │  │
│  │                         │  │
│  │  ┌──────────────────┐   │  │
│  │  │ Inner iframe     │   │  │
│  │  │                  │   │  │
│  │  │ Username         │   │  │
│  │  └──────────────────┘   │  │
│  └────────────────────────┘  │
└──────────────────────────────┘
```

Playwright navigation:

```text
Page
 ↓
Outer iframe
 ↓
Inner iframe
 ↓
Element
```

---

# 17. Combining Dialog + Frame

Sometimes the button is inside an iframe and clicking it opens an alert.

Example:

```ts
test("Frame + Alert", async ({ page }) => {

    await page.goto("https://example.com");

    page.on("dialog", async dialog => {

        console.log(dialog.message());

        await dialog.accept();
    });

    const frame = page.frameLocator("#myFrame");

    await frame.locator("#alertButton").click();
});
```

The dialog belongs to the **page**, so the dialog listener is:

```ts
page.on("dialog", ...)
```

not:

```ts
frame.on("dialog", ...)
```

---

# 18. Common Mistakes

### ❌ Mistake 1 — Handler after click

```ts
await page.locator("#alert").click();

page.on("dialog", async dialog => {
    await dialog.accept();
});
```

The handler is registered too late.

### ✅ Correct

```ts
page.on("dialog", async dialog => {
    await dialog.accept();
});

await page.locator("#alert").click();
```

---

### ❌ Mistake 2 — Accessing iframe element directly

```ts
await page.locator("#username").fill("admin");
```

If `#username` is inside an iframe, this won't work.

### ✅ Correct

```ts
await page
    .frameLocator("#loginFrame")
    .locator("#username")
    .fill("admin");
```

---

### ❌ Mistake 3 — Using unnecessary XPath

Instead of:

```ts
//iframe[@id='outer']//iframe[@id='inner']//input[@id='username']
```

Prefer:

```ts
await page
    .frameLocator("#outer")
    .frameLocator("#inner")
    .locator("#username")
    .fill("admin");
```

---

# Quick Cheat Sheet

```ts
// ALERT
page.on("dialog", async dialog => {
    await dialog.accept();
});
```

```ts
// CONFIRM → OK
page.on("dialog", async dialog => {
    await dialog.accept();
});
```

```ts
// CONFIRM → CANCEL
page.on("dialog", async dialog => {
    await dialog.dismiss();
});
```

```ts
// PROMPT
page.on("dialog", async dialog => {
    await dialog.accept("Gajanan");
});
```

```ts
// SINGLE IFRAME
const frame = page.frameLocator("#frame");

await frame.locator("#username").fill("admin");
```

```ts
// NESTED IFRAME
const outer = page.frameLocator("#outerFrame");
const inner = outer.frameLocator("#innerFrame");

await inner.locator("#username").fill("admin");
```

### ⭐ Interview rule to remember

**Dialogs:**

> `page.on("dialog") → accept/dismiss`

**Iframe:**

> `page.frameLocator("iframe") → locator()`

**Nested iframe:**

> `page.frameLocator("outer").frameLocator("inner").locator("element")`

This pattern covers most **Alert, Confirm, Prompt, iframe, and nested iframe** automation scenarios in Playwright.
