Absolutely. This is the next important Playwright topic after dialogs and frames. The easiest way to understand it is to separate **Browser → Context → Page → Popup**.

## 1. Understand the hierarchy first

Think of Playwright like this:

```text
Browser
│
├── Browser Context 1
│   │
│   ├── Page 1 (Tab)
│   ├── Page 2 (Tab)
│   └── Popup
│
└── Browser Context 2
    │
    └── Page 1
```

### Meaning

* **Browser** → Chrome/Firefox/Edge process
* **BrowserContext** → isolated browser session
* **Page** → a browser tab
* **Popup** → a new page/tab opened by another page

---

# 2. Browser Context

Normally, Playwright Test creates a browser context for each test automatically.

You usually don't need to manually create one.

But it's important to understand:

```ts
const context = await browser.newContext();
```

Example:

```ts
import { test, expect } from "@playwright/test";

test("Browser Context", async ({ browser }) => {

    const context = await browser.newContext();

    const page = await context.newPage();

    await page.goto("https://testautomationpractice.blogspot.com/");

    console.log(await page.title());

    await context.close();
});
```

Here:

```text
browser
   ↓
context
   ↓
page
```

---

# 3. Why BrowserContext is useful

A context gives you an **isolated session**.

For example:

```text
Context 1
    User A
    Cookies A
    Local Storage A

Context 2
    User B
    Cookies B
    Local Storage B
```

They don't share the same browser session.

This is useful for testing multiple users.

For example:

```ts
test("Multiple users", async ({ browser }) => {

    const user1Context = await browser.newContext();
    const user2Context = await browser.newContext();

    const user1Page = await user1Context.newPage();
    const user2Page = await user2Context.newPage();

    await user1Page.goto("https://example.com");
    await user2Page.goto("https://example.com");

    // User 1 and User 2 have separate sessions

    await user1Context.close();
    await user2Context.close();
});
```

---

# 4. Pages / Tabs

A **Page** represents a browser tab.

You can create multiple pages inside the same context:

```ts
const page1 = await context.newPage();
const page2 = await context.newPage();
const page3 = await context.newPage();
```

Visually:

```text
Browser
   │
   └── Context
        │
        ├── Page 1 → Tab 1
        ├── Page 2 → Tab 2
        └── Page 3 → Tab 3
```

You can work with them independently.

```ts
await page1.goto("https://google.com");
await page2.goto("https://youtube.com");
await page3.goto("https://github.com");
```

---

# 5. Get all open pages

You can use:

```ts
const pages = context.pages();
```

Example:

```ts
test("Multiple Pages", async ({ browser }) => {

    const context = await browser.newContext();

    const page1 = await context.newPage();
    const page2 = await context.newPage();

    await page1.goto("https://google.com");
    await page2.goto("https://youtube.com");

    const pages = context.pages();

    console.log("Number of pages:", pages.length);

    for (const page of pages) {
        console.log(await page.title());
    }
});
```

---

# 6. Handling a New Tab / Popup

This is probably the **most important part**.

Suppose you have:

```html
<a target="_blank" href="https://example.com">
    Open New Tab
</a>
```

Clicking it opens a new page.

You need to tell Playwright:

> "Wait for a new page while I click this button."

Use:

```ts
const newPagePromise = context.waitForEvent("page");

await page.locator("#newTab").click();

const newPage = await newPagePromise;
```

Then:

```ts
await newPage.waitForLoadState();

console.log(await newPage.title());
```

---

# 7. Complete New Tab Example

```ts
import { test, expect } from "@playwright/test";

test("Handle New Tab", async ({ browser }) => {

    const context = await browser.newContext();

    const page = await context.newPage();

    await page.goto("https://testautomationpractice.blogspot.com/");

    const newPagePromise = context.waitForEvent("page");

    await page.locator("#Wikipedia1_wikipedia-search-input").fill("Playwright");

    // Example only — use the actual element that opens a new tab
    // await page.locator("a[target='_blank']").click();

    const newPage = await newPagePromise;

    await newPage.waitForLoadState();

    console.log("New page URL:", newPage.url());
});
```

The important pattern is:

```ts
const newPagePromise = context.waitForEvent("page");

await page.locator("button").click();

const newPage = await newPagePromise;
```

---

# 8. Why do we create the promise BEFORE clicking?

This is very important.

### ❌ Don't do this

```ts
await page.locator("#newTab").click();

const newPage = await context.waitForEvent("page");
```

The new page may already have opened before Playwright starts waiting for it.

### ✅ Correct

```ts
const newPagePromise = context.waitForEvent("page");

await page.locator("#newTab").click();

const newPage = await newPagePromise;
```

You start listening **before the action that triggers the event**.

---

# 9. Another approach: `page.waitForEvent("popup")`

If the new page is specifically opened by the current page, you can use:

```ts
const popupPromise = page.waitForEvent("popup");

await page.locator("#newTab").click();

const popup = await popupPromise;
```

Then:

```ts
await popup.waitForLoadState();

console.log(popup.url());
```

This is very common.

---

# 10. `context.waitForEvent("page")` vs `page.waitForEvent("popup")`

This distinction is useful for interviews.

### `context.waitForEvent("page")`

Waits for a new page created anywhere in the context.

```ts
const newPagePromise = context.waitForEvent("page");
```

### `page.waitForEvent("popup")`

Waits for a popup opened specifically by that page.

```ts
const popupPromise = page.waitForEvent("popup");
```

For a button/link that opens a new tab, I commonly use:

```ts
const popupPromise = page.waitForEvent("popup");

await page.locator("#openTab").click();

const popup = await popupPromise;

await popup.waitForLoadState();

console.log(await popup.title());
```

---

# 11. Switching between tabs

One important thing:

**Playwright doesn't have a Selenium-style `switchTo().window()` method.**

You simply keep references to the pages.

```ts
const pages = context.pages();

const firstPage = pages[0];
const secondPage = pages[1];

await firstPage.bringToFront();
```

Then:

```ts
await secondPage.bringToFront();
```

But usually you don't even need `bringToFront()`.

You can directly:

```ts
await secondPage.locator("#username").fill("Gajanan");
```

---

# 12. Example: Parent Page + New Tab

Imagine:

```text
Tab 1
Google
   │
   └── Click "Open"
             ↓
Tab 2
Wikipedia
```

Code:

```ts
const popupPromise = page.waitForEvent("popup");

await page.locator("#open").click();

const popup = await popupPromise;

await popup.waitForLoadState();

console.log("Parent:", page.url());
console.log("Popup:", popup.url());

await popup.locator("#search").fill("Playwright");
```

You can still use the original page:

```ts
await page.locator("#someElement").click();
```

---

# 13. Multiple Tabs

Suppose clicking a button opens 2 new tabs.

You can monitor pages:

```ts
const pagePromise = context.waitForEvent("page");

await page.locator("#open").click();

const newPage = await pagePromise;

await newPage.waitForLoadState();

console.log(newPage.url());
```

Or inspect all pages:

```ts
const pages = context.pages();

console.log("Total pages:", pages.length);

for (const p of pages) {
    console.log(p.url());
}
```

---

# 14. Very Important Pattern to Remember

For a popup:

```ts
const popupPromise = page.waitForEvent("popup");

await page.locator("button").click();

const popup = await popupPromise;

await popup.waitForLoadState();

await popup.locator("#someElement").click();
```

Memorize this pattern.

```text
wait for popup
      ↓
perform click
      ↓
get popup
      ↓
wait for load
      ↓
work with popup
```

---

# 15. Browser vs Context vs Page

This is the most important conceptual part.

| Object    | Represents               |
| --------- | ------------------------ |
| `browser` | Browser instance         |
| `context` | Isolated browser session |
| `page`    | Tab                      |
| `popup`   | Newly opened tab/window  |

Example:

```ts
const context = await browser.newContext();

const page = await context.newPage();

const popupPromise = page.waitForEvent("popup");

await page.locator("#open").click();

const popup = await popupPromise;
```

Hierarchy:

```text
browser
   ↓
context
   ↓
page
   ↓
popup
```

Technically, **popup is also a `Page` object**.

That's an important point:

```ts
popup instanceof Page
```

Conceptually:

```text
Page
 ├── normal tab
 └── popup/new tab
```

---

# 16. What you should practice

Since you're learning Playwright step-by-step, I'd practice these **5 scenarios**:

### Test 1 — Create a new context

```ts
browser.newContext()
```

### Test 2 — Create multiple pages

```ts
context.newPage()
```

### Test 3 — Get all pages

```ts
context.pages()
```

### Test 4 — Open a new tab

```ts
page.waitForEvent("popup")
```

### Test 5 — Open a new tab and interact with it

```ts
const popupPromise = page.waitForEvent("popup");

await page.locator("...").click();

const popup = await popupPromise;

await popup.waitForLoadState();

await popup.locator("...").click();
```

If you can comfortably write those five, you have a **good understanding of Browser Context, Tabs, Pages, and Popups**.
