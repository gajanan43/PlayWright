# Trace Viewer, Screenshots & Videos in Playwright

These are important for **debugging failed tests** and understanding what happened during test execution.

---

# 1. Trace Viewer

**Trace Viewer** records detailed information about a Playwright test.

It can help you see:

* Actions performed
* Screenshots at each action
* DOM snapshots
* Network requests
* Console messages
* Errors
* Test timing
* Before/after state of actions

Think of it as a **recording of your test execution**.

---

## Enable Trace

In `playwright.config.ts`:

```ts
import { defineConfig } from "@playwright/test";

export default defineConfig({
    use: {
        trace: "on"
    }
});
```

Then run:

```bash
npx playwright test
```

After the test finishes, Playwright creates trace information.

You can open it with:

```bash
npx playwright show-trace path/to/trace.zip
```

---

## Recommended Trace Setting

Instead of recording every test, a common approach is:

```ts
export default defineConfig({
    use: {
        trace: "on-first-retry"
    }
});
```

Meaning:

```text
First attempt
     ↓
Test fails
     ↓
Retry test
     ↓
Capture trace
```

This is useful because traces can consume additional storage.

---

# 2. Trace Options

### `trace: "on"`

Always record traces.

```ts
use: {
    trace: "on"
}
```

### `trace: "off"`

Don't record traces.

```ts
use: {
    trace: "off"
}
```

### `trace: "on-first-retry"`

Record trace when the test is retried for the first time.

```ts
use: {
    trace: "on-first-retry"
}
```

This is commonly useful in CI.

### `trace: "retain-on-failure"`

Keep the trace when the test fails.

```ts
use: {
    trace: "retain-on-failure"
}
```

---

# 3. Capture Screenshots

Playwright can capture screenshots using:

```ts
await page.screenshot();
```

Example:

```ts
import { test } from "@playwright/test";

test("Screenshot test", async ({ page }) => {

    await page.goto("https://testautomationpractice.blogspot.com/");

    await page.screenshot({
        path: "screenshots/homepage.png"
    });

});
```

The screenshot will be saved as:

```text
screenshots/
    homepage.png
```

---

# 4. Full Page Screenshot

By default, Playwright captures the visible viewport.

To capture the **entire page**:

```ts
await page.screenshot({
    path: "screenshots/fullpage.png",
    fullPage: true
});
```

So:

```ts
fullPage: true
```

means:

> Capture the complete scrollable page.

---

# 5. Screenshot of an Element

You don't have to capture the entire page.

You can capture a particular element:

```ts
await page.locator("h1").screenshot({
    path: "screenshots/title.png"
});
```

For example:

```ts
const logo = page.locator("img[alt='logo']");

await logo.screenshot({
    path: "screenshots/logo.png"
});
```

---

# 6. Capture Screenshot Automatically on Failure

This is extremely useful.

In `playwright.config.ts`:

```ts
export default defineConfig({
    use: {
        screenshot: "only-on-failure"
    }
});
```

Now:

```text
Test passes
   ↓
No screenshot

Test fails
   ↓
Screenshot automatically captured
```

---

# 7. Screenshot Options

You can configure:

```ts
use: {
    screenshot: "only-on-failure"
}
```

Possible values include:

```text
"off"
"on"
"only-on-failure"
```

For most automation projects:

```ts
screenshot: "only-on-failure"
```

is a good choice.

---

# 8. Capture Videos

Playwright can record a video of the browser test.

Configure it in `playwright.config.ts`:

```ts
export default defineConfig({
    use: {
        video: "on"
    }
});
```

Then:

```bash
npx playwright test
```

Playwright records the browser session.

---

# 9. Video Options

### Always record

```ts
video: "on"
```

Every test gets a video.

### Record only on failure

```ts
video: "on-first-retry"
```

Useful when debugging retries.

### Record only when test fails

```ts
video: "retain-on-failure"
```

The video is recorded, but Playwright retains it when the test fails.

---

# 10. Recommended Configuration

For a real project, you could use:

```ts
import { defineConfig } from "@playwright/test";

export default defineConfig({

    retries: 1,

    use: {

        // Trace failed/retried tests
        trace: "on-first-retry",

        // Screenshot when test fails
        screenshot: "only-on-failure",

        // Keep video for failed tests
        video: "retain-on-failure"
    }
});
```

This gives you:

```text
                 Test
                  │
          ┌───────┼────────┐
          ↓       ↓        ↓
       Trace   Screenshot  Video
          │       │        │
          └───────┴────────┘
                  ↓
            Debug failure
```

---

# 11. Complete Example

Using your previous website:

```ts
import { test, expect } from "@playwright/test";

test("Trace Screenshot Video", async ({ page }) => {

    await page.goto(
        "https://testautomationpractice.blogspot.com/"
    );

    await expect(page).toHaveURL(
        "https://testautomationpractice.blogspot.com/"
    );

    await expect(
        page.locator("//h1[@class='title']")
    ).toHaveText("Automation Testing Practice");

    await page.locator("#name").fill("Gajanan");

    await page.locator("//label[@for='male']").click();

    // Manual screenshot
    await page.screenshot({
        path: "screenshots/test.png",
        fullPage: true
    });
});
```

And configuration:

```ts
import { defineConfig } from "@playwright/test";

export default defineConfig({

    retries: 1,

    use: {
        trace: "on-first-retry",
        screenshot: "only-on-failure",
        video: "retain-on-failure"
    }

});
```

---

# 12. How to Open Trace Viewer

After running your test:

```bash
npx playwright test
```

If a trace is available, open it:

```bash
npx playwright show-trace path/to/trace.zip
```

The Trace Viewer gives you a timeline like:

```text
Test
 │
 ├── goto()
 │
 ├── fill("#name")
 │
 ├── click("Male")
 │
 ├── expect()
 │
 └── FAILURE ❌
```

You can select an individual action and inspect what happened at that exact point.

---

## Easy way to remember

| Feature                         | Purpose                           |
| ------------------------------- | --------------------------------- |
| **Trace Viewer**                | Detailed debugging record         |
| **Screenshot**                  | Captures page/element as an image |
| **Video**                       | Records browser execution         |
| `page.screenshot()`             | Manually capture screenshot       |
| `fullPage: true`                | Capture entire page               |
| `screenshot: "only-on-failure"` | Automatic failure screenshot      |
| `video: "on"`                   | Record every test                 |
| `video: "retain-on-failure"`    | Keep videos for failures          |
| `trace: "on"`                   | Record every trace                |
| `trace: "on-first-retry"`       | Record trace on first retry       |

### ⭐ Interview answer

> **Trace Viewer** is a Playwright debugging tool that lets us inspect test execution through actions, screenshots, DOM snapshots, network activity, and other information. **Screenshots** capture the page or a specific element, while **videos** record the browser session. These artifacts are especially useful for debugging failed tests in CI/CD.
