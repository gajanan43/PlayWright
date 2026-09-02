# Grouping Tests | Hooks | Annotations & Tags in Playwright

These four concepts help you organize and control your Playwright test suite.

---

# 1. Grouping Tests

Use `test.describe()` to group related tests.

```ts
import { test, expect } from "@playwright/test";

test.describe("Login Tests", () => {

    test("Valid login", async ({ page }) => {
        console.log("Valid login test");
    });

    test("Invalid login", async ({ page }) => {
        console.log("Invalid login test");
    });

});
```

This produces a structure like:

```text
Login Tests
 ├── Valid login
 └── Invalid login
```

### Why use `test.describe()`?

It helps you:

* Organize related tests
* Apply hooks to a group
* Apply configuration to a group
* Group tests in reports

---

# 2. Nested Test Groups

You can put one `describe` inside another.

```ts
test.describe("E-commerce Tests", () => {

    test.describe("Login", () => {

        test("Valid login", async ({ page }) => {
        });

        test("Invalid login", async ({ page }) => {
        });

    });

    test.describe("Products", () => {

        test("Add product", async ({ page }) => {
        });

        test("Remove product", async ({ page }) => {
        });

    });

});
```

Structure:

```text
E-commerce Tests
│
├── Login
│   ├── Valid login
│   └── Invalid login
│
└── Products
    ├── Add product
    └── Remove product
```

---

# 3. Hooks

Hooks allow you to run common code **before or after tests**.

Playwright provides four commonly used hooks:

```text
beforeAll
afterAll
beforeEach
afterEach
```

---

## `beforeEach`

Runs **before every test**.

```ts
test.beforeEach(async ({ page }) => {

    await page.goto("https://www.demoblaze.com/");

});
```

Example:

```ts
test.describe("Login Tests", () => {

    test.beforeEach(async ({ page }) => {
        await page.goto("https://www.demoblaze.com/");
    });

    test("Test 1", async ({ page }) => {
        // page is already opened
    });

    test("Test 2", async ({ page }) => {
        // page is already opened
    });

});
```

Execution:

```text
beforeEach
   ↓
Test 1
   ↓
beforeEach
   ↓
Test 2
```

---

# 4. `afterEach`

Runs **after every test**.

```ts
test.afterEach(async ({ page }) => {

    console.log("Test completed");

});
```

Execution:

```text
beforeEach
     ↓
Test
     ↓
afterEach
```

---

# 5. `beforeAll`

Runs **once before all tests in the group**.

```ts
test.beforeAll(async () => {

    console.log("Runs once before all tests");

});
```

Example:

```ts
test.describe("User Tests", () => {

    test.beforeAll(async () => {
        console.log("Before all tests");
    });

    test("Test 1", async () => {
        console.log("Test 1");
    });

    test("Test 2", async () => {
        console.log("Test 2");
    });

});
```

Output:

```text
Before all tests
Test 1
Test 2
```

---

# 6. `afterAll`

Runs **once after all tests in the group**.

```ts
test.afterAll(async () => {

    console.log("After all tests");

});
```

Execution:

```text
beforeAll
   ↓
Test 1
   ↓
Test 2
   ↓
afterAll
```

---

# 7. Hook Execution Order

Suppose you have:

```ts
test.beforeAll();

test.beforeEach();

test("Test 1");

test.afterEach();

test.afterAll();
```

The basic execution order is:

```text
beforeAll
   ↓
beforeEach
   ↓
Test 1
   ↓
afterEach
   ↓
afterAll
```

For multiple tests:

```text
beforeAll
   ↓
beforeEach
   ↓
Test 1
   ↓
afterEach
   ↓
beforeEach
   ↓
Test 2
   ↓
afterEach
   ↓
afterAll
```

---

# 8. Hooks Inside `describe`

This is very useful.

```ts
test.describe("Login Tests", () => {

    test.beforeEach(async ({ page }) => {
        await page.goto("https://www.demoblaze.com/");
    });

    test("Valid Login", async ({ page }) => {
        // test
    });

    test("Invalid Login", async ({ page }) => {
        // test
    });

});
```

The `beforeEach` applies to the tests **inside that describe block**.

---

# 9. Annotations

Annotations provide additional information or instructions about tests.

Common Playwright annotations include:

```text
test.skip()
test.fixme()
test.fail()
test.slow()
```

---

## `test.skip()`

Skip a test.

```ts
test.skip("Not implemented", async ({ page }) => {

});
```

The test will not execute.

You can also conditionally skip:

```ts
test("Test", async ({ page, browserName }) => {

    test.skip(browserName === "webkit", "Not supported in WebKit");

});
```

---

# 10. `test.fixme()`

Used when a test is currently broken and needs fixing.

```ts
test.fixme("Known issue", async ({ page }) => {

});
```

Think:

```text
skip → don't run this test
fixme → this test needs to be fixed
```

---

# 11. `test.fail()`

Used when you **expect the test to fail**.

```ts
test.fail("Known bug", async ({ page }) => {

    expect(true).toBe(false);

});
```

The test is expected to fail.

If it unexpectedly passes, Playwright reports that the expected failure did not happen.

---

# 12. `test.slow()`

Marks a test as slow and increases its timeout.

```ts
test("Large data test", async ({ page }) => {

    test.slow();

});
```

You can also provide a reason:

```ts
test("Large data test", async ({ page }) => {

    test.slow(true, "This test performs a large operation");

});
```

---

# 13. Tags

Tags allow you to categorize tests.

For example:

```ts
test("Login test", {
    tag: "@smoke"
}, async ({ page }) => {

});
```

Another:

```ts
test("Payment test", {
    tag: "@regression"
}, async ({ page }) => {

});
```

Now you can organize tests as:

```text
@smoke
 ├── Login test
 └── Registration test

@regression
 ├── Payment test
 └── Cart test
```

---

# 14. Multiple Tags

You can assign multiple tags.

```ts
test("Login test", {
    tag: ["@smoke", "@login"]
}, async ({ page }) => {

});
```

Now this test belongs to:

```text
@smoke
@login
```

---

# 15. Run Tests by Tag

For example, if you have:

```ts
test("Login test", {
    tag: "@smoke"
}, async ({ page }) => {
});
```

You can run smoke tests using:

```bash
npx playwright test --grep @smoke
```

To run regression:

```bash
npx playwright test --grep @regression
```

---

# 16. Complete Example

Here's how you might combine everything:

```ts
import { test, expect } from "@playwright/test";

test.describe("DemoBlaze Tests", () => {

    test.beforeAll(async () => {
        console.log("Starting DemoBlaze test suite");
    });

    test.beforeEach(async ({ page }) => {
        await page.goto("https://www.demoblaze.com/");
    });

    test("Login test", {
        tag: ["@smoke", "@login"]
    }, async ({ page }) => {

        await page.locator("#login2").click();

        await page.locator("#loginusername").fill("demo9144");

        await page.locator("#loginpassword").fill("demo");

        await page.getByRole("button", { name: "Log in" }).click();

    });

    test("Product test", {
        tag: "@regression"
    }, async ({ page }) => {

        await expect(page).toHaveTitle(/STORE/);

    });

    test.skip("Not ready test", async ({ page }) => {

    });

    test.afterEach(async () => {
        console.log("Test completed");
    });

    test.afterAll(async () => {
        console.log("Finished DemoBlaze test suite");
    });

});
```

---

# 17. Quick Revision

| Feature           | Purpose                          |
| ----------------- | -------------------------------- |
| `test.describe()` | Group related tests              |
| `beforeAll()`     | Run once before all tests        |
| `afterAll()`      | Run once after all tests         |
| `beforeEach()`    | Run before every test            |
| `afterEach()`     | Run after every test             |
| `test.skip()`     | Skip a test                      |
| `test.fixme()`    | Mark a broken test for fixing    |
| `test.fail()`     | Expect a test to fail            |
| `test.slow()`     | Increase timeout for a slow test |
| `tag`             | Categorize tests                 |
| `--grep @smoke`   | Run tests matching a tag         |

### ⭐ Easy interview explanation

> **Grouping** organizes related tests using `test.describe()`. **Hooks** execute common setup or cleanup code using `beforeAll`, `afterAll`, `beforeEach`, and `afterEach`. **Annotations** control or describe test behavior, such as skipping, expecting failure, or marking a test as slow. **Tags** categorize tests so we can selectively execute groups such as smoke or regression tests.
