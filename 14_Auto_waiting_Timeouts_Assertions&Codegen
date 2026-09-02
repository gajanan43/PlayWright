# Auto Waiting, Timeouts, Assertions & Codegen in Playwright

These are **four important Playwright concepts** you should learn together because they directly affect how reliable your tests are.

---

## 1. Auto Waiting in Playwright

One of Playwright's biggest advantages is **automatic waiting**.

When you perform an action like:

```ts
await page.locator("#login").click();
```

Playwright does **not immediately click**.

It automatically waits for the element to become actionable, such as:

* Element exists in DOM
* Element is visible
* Element is enabled
* Element is not blocked by another element
* Element is stable enough to interact with

### Example

```ts
await page.goto("https://example.com");

await page.locator("#login").click();
```

You generally **do not need**:

```ts
await page.waitForTimeout(5000);
```

❌ Avoid unnecessary hard waits.

Instead:

```ts
await page.locator("#login").click();
```

Playwright automatically waits.

### Auto-waiting example

```ts
await page.getByRole("button", { name: "Login" }).click();

await page.getByLabel("Username").fill("admin");

await page.getByLabel("Password").fill("12345");
```

This is one reason Playwright tests are usually more stable than tests that rely heavily on `sleep()`.

---

# 2. Timeouts in Playwright

A **timeout** defines how long Playwright should wait for an operation before failing.

There are several important timeout types.

### A. Action timeout

Controls actions such as:

```ts
click()
fill()
check()
selectOption()
```

Example:

```ts
await page.locator("#login").click({
    timeout: 10000
});
```

Playwright waits up to **10 seconds** for the action to succeed.

---

### B. Assertion timeout

Controls how long Playwright waits for an assertion.

```ts
await expect(page.locator("h1")).toHaveText("Welcome", {
    timeout: 10000
});
```

It will retry the assertion for up to 10 seconds.

---

### C. Navigation timeout

Controls navigation operations.

```ts
await page.goto("https://example.com", {
    timeout: 30000
});
```

Maximum wait = **30 seconds**.

---

### D. Test timeout

Controls the maximum duration of the entire test.

```ts
test("Login test", async ({ page }) => {
    // test code
});
```

You can configure it in `playwright.config.ts`.

```ts
import { defineConfig } from "@playwright/test";

export default defineConfig({
    timeout: 30000
});
```

---

## Timeout hierarchy

A simple way to remember:

```text
Test
 ├── Navigation
 ├── Actions
 └── Assertions
```

Each operation can have its own timeout, while the test itself also has an overall timeout.

---

# 3. Assertions in Playwright

Assertions are used to **verify that the application behaves as expected**.

Playwright provides the `expect()` API.

```ts
import { test, expect } from "@playwright/test";
```

### Example

```ts
test("Check title", async ({ page }) => {

    await page.goto("https://www.selenium.dev/");

    await expect(page).toHaveTitle("Selenium");

});
```

Here:

```ts
expect(page)
```

means:

> I expect something about this page.

And:

```ts
.toHaveTitle("Selenium")
```

means:

> The page should have this title.

---

## Common assertions

### URL

```ts
await expect(page).toHaveURL("https://example.com/");
```

### Title

```ts
await expect(page).toHaveTitle("Example");
```

### Element visible

```ts
await expect(page.locator("#login")).toBeVisible();
```

### Element hidden

```ts
await expect(page.locator("#error")).toBeHidden();
```

### Text

```ts
await expect(page.locator("h1")).toHaveText("Welcome");
```

### Contains text

```ts
await expect(page.locator("body")).toContainText("Welcome");
```

### Input value

```ts
await expect(page.locator("#username"))
    .toHaveValue("admin");
```

### Checkbox

```ts
await expect(page.locator("#remember"))
    .toBeChecked();
```

---

# 4. Soft Assertions

Normally, when an assertion fails, the test continues no further from that point.

You can use a **soft assertion** when you want the test to continue.

```ts
await expect.soft(page.locator("#username"))
    .toBeVisible();

await expect.soft(page.locator("#password"))
    .toBeVisible();

await expect.soft(page.locator("#login"))
    .toBeVisible();
```

This allows you to collect multiple assertion failures before the test is reported as failed.

---

# 5. Assertions Automatically Wait

This is very important.

Consider:

```ts
await expect(page.locator("#message"))
    .toBeVisible();
```

Playwright doesn't simply check once.

It **retries the assertion until it passes or the assertion timeout expires**.

So this:

```ts
await expect(locator).toBeVisible();
```

is generally better than:

```ts
await page.waitForTimeout(3000);
expect(await locator.isVisible()).toBe(true);
```

### Prefer

```ts
await expect(locator).toBeVisible();
```

instead of manually waiting.

---

# 6. `waitForTimeout()` — Don't Overuse It

You may see:

```ts
await page.waitForTimeout(5000);
```

This means:

> Wait exactly 5 seconds.

But this is usually **not a good synchronization strategy**.

For example:

```ts
await page.waitForTimeout(5000);
await page.locator("#login").click();
```

If the element becomes ready after 1 second, you unnecessarily wait 4 seconds.

If it becomes ready after 7 seconds, the test fails.

### Better

```ts
await page.locator("#login").click();
```

Playwright handles the waiting.

---

# 7. Codegen in Playwright

**Codegen** is Playwright's test/code generator.

It allows you to interact with a website while Playwright generates code for your actions.

Run:

```bash
npx playwright codegen
```

A browser opens.

For example, if you:

1. Open a website
2. Click Login
3. Enter username
4. Enter password
5. Click Submit

Playwright can generate code similar to:

```ts
await page.goto("https://example.com/login");

await page.getByLabel("Username").fill("admin");

await page.getByLabel("Password").fill("12345");

await page.getByRole("button", { name: "Login" }).click();
```

---

# 8. Codegen with a URL

You can directly provide a website:

```bash
npx playwright codegen https://www.saucedemo.com/
```

This is very useful when you're learning Playwright.

---

# 9. Codegen Generates Locators

One of the most useful features is that Codegen helps generate locators.

For example, it may generate:

```ts
page.getByRole("button", { name: "Login" })
```

or:

```ts
page.getByPlaceholder("Username")
```

or:

```ts
page.getByText("Products")
```

or:

```ts
page.locator("#username")
```

This helps you understand how Playwright identifies elements.

---

# 10. Codegen Is a Starting Point, Not Final Code

Don't blindly copy everything generated by Codegen.

For example, Codegen might generate:

```ts
await page.locator("div:nth-child(3) > button").click();
```

You may be able to replace it with a more readable locator:

```ts
await page.getByRole("button", { name: "Login" }).click();
```

### Locator preference

Generally prefer:

```text
1. getByRole()
2. getByLabel()
3. getByPlaceholder()
4. getByText()
5. getByTestId()
6. CSS/XPath when necessary
```

---

# 11. Complete Example

Here's a small example combining **auto-waiting, timeout, assertions, and good locators**:

```ts
import { test, expect } from "@playwright/test";

test("Login test", async ({ page }) => {

    await page.goto("https://www.saucedemo.com/");

    // Assertion
    await expect(page).toHaveTitle(/Swag Labs/);

    // Auto-waiting + action
    await page.getByPlaceholder("Username").fill("standard_user");

    await page.getByPlaceholder("Password").fill("secret_sauce");

    await page.getByRole("button", { name: "Login" }).click();

    // Assertion + auto-retry
    await expect(page).toHaveURL(/inventory/);

    await expect(
        page.getByText("Products")
    ).toBeVisible();

});
```

Notice that we didn't use:

```ts
waitForTimeout()
```

because Playwright automatically waits for actions and assertions.

---

# 12. Quick Interview Notes

| Concept                | Meaning                                                          |
| ---------------------- | ---------------------------------------------------------------- |
| **Auto waiting**       | Playwright automatically waits for elements to become actionable |
| **Timeout**            | Maximum time Playwright waits                                    |
| **Assertion**          | Verifies expected application behavior                           |
| **`expect()`**         | Playwright assertion API                                         |
| **`waitForTimeout()`** | Fixed wait; generally avoid for synchronization                  |
| **Codegen**            | Generates Playwright code from browser interactions              |
| **Action timeout**     | Timeout for actions like click/fill                              |
| **Assertion timeout**  | Timeout for `expect()`                                           |
| **Navigation timeout** | Timeout for navigation                                           |
| **Test timeout**       | Maximum duration of a test                                       |

### The most important rule

```text
Don't manually wait unless you actually need to.

Use Playwright's:
    Auto-waiting
        +
    Locator actions
        +
    Web-first assertions
```

For example:

```ts
await page.getByRole("button", { name: "Submit" }).click();

await expect(page.getByText("Success")).toBeVisible();
```

This is the **Playwright way**.
