# Parallelism | Parallel Testing in Playwright

**Parallel testing** means running multiple tests at the same time instead of one after another.

This is one of the main ways Playwright makes a large test suite faster.

---

## 1. Sequential vs Parallel Testing

### Sequential

Suppose you have 4 tests:

```text
Test 1 ──────►
              Test 2 ──────►
                           Test 3 ──────►
                                        Test 4 ──────►
```

If each test takes 5 seconds:

```text
4 × 5 = 20 seconds
```

### Parallel

With 4 workers:

```text
Worker 1 → Test 1
Worker 2 → Test 2
Worker 3 → Test 3
Worker 4 → Test 4
```

Approximately:

```text
5 seconds
```

So parallelism can significantly reduce execution time.

---

# 2. Playwright Workers

Playwright uses **workers** to execute tests in parallel.

For example:

```bash
npx playwright test --workers=4
```

This tells Playwright to use **4 worker processes**.

Conceptually:

```text
                Playwright
                    │
       ┌────────────┼────────────┐
       ↓            ↓            ↓
   Worker 1      Worker 2      Worker 3
       │            │            │
    Test 1       Test 2       Test 3
```

---

# 3. Configure Workers

You can configure workers in `playwright.config.ts`:

```ts
import { defineConfig } from "@playwright/test";

export default defineConfig({
    workers: 4
});
```

Now Playwright can use up to 4 workers.

You can also use:

```bash
npx playwright test --workers=4
```

Command-line configuration is useful when you want to temporarily change the number of workers.

---

# 4. Playwright Runs Test Files in Parallel

By default, Playwright can run **test files in parallel**.

Suppose you have:

```text
tests/
│
├── login.spec.ts
├── signup.spec.ts
├── product.spec.ts
└── cart.spec.ts
```

With multiple workers:

```text
Worker 1 → login.spec.ts
Worker 2 → signup.spec.ts
Worker 3 → product.spec.ts
Worker 4 → cart.spec.ts
```

This is called **file-level parallelism**.

---

# 5. Tests Inside One File

By default, tests within the same file run **in order**.

Example:

```ts
import { test } from "@playwright/test";

test("Test 1", async ({ page }) => {
    console.log("Test 1");
});

test("Test 2", async ({ page }) => {
    console.log("Test 2");
});

test("Test 3", async ({ page }) => {
    console.log("Test 3");
});
```

They normally execute:

```text
Test 1
   ↓
Test 2
   ↓
Test 3
```

---

# 6. Parallel Tests Inside a File

If you want tests inside the same file to run in parallel, use:

```ts
test.describe.configure({ mode: "parallel" });
```

Example:

```ts
import { test } from "@playwright/test";

test.describe.configure({ mode: "parallel" });

test("Test 1", async ({ page }) => {
    console.log("Test 1");
});

test("Test 2", async ({ page }) => {
    console.log("Test 2");
});

test("Test 3", async ({ page }) => {
    console.log("Test 3");
});
```

Now:

```text
Worker 1 → Test 1
Worker 2 → Test 2
Worker 3 → Test 3
```

---

# 7. Parallel Mode with `describe`

You can also limit parallelism to a particular group.

```ts
test.describe("Login Tests", () => {

    test.describe.configure({ mode: "parallel" });

    test("Valid Login", async ({ page }) => {
    });

    test("Invalid Login", async ({ page }) => {
    });

    test("Locked User", async ({ page }) => {
    });

});
```

These tests can execute independently.

---

# 8. Three Modes

Playwright's test grouping has three important modes:

```text
default
parallel
serial
```

### Default

```ts
test.describe.configure({ mode: "default" });
```

Tests in the group run sequentially.

### Parallel

```ts
test.describe.configure({ mode: "parallel" });
```

Tests can run in parallel.

### Serial

```ts
test.describe.configure({ mode: "serial" });
```

Tests run sequentially, and failure behavior is different: if one test fails, subsequent tests in that serial group are skipped.

---

# 9. Important: Tests Should Be Independent

Parallel testing works best when tests **don't depend on each other**.

❌ Bad design:

```text
Test 1 → Create user
   ↓
Test 2 → Login with that user
   ↓
Test 3 → Delete that user
```

These tests depend on one another.

If you run them in parallel:

```text
Test 1 ────────── Create user
Test 2 ─ Login? ❌ User doesn't exist yet
Test 3 ─ Delete? ❌
```

Instead, make each test independent.

```ts
test("Login", async ({ page }) => {

    // Create/use required test data
    // Login
});

test("Delete user", async ({ page }) => {

    // Create/use required test data
    // Delete
});
```

---

# 10. Parallelism and Your Previous DemoBlaze Test

Your test was using:

```ts
test("tracing test", async ({ page }) => {
    ...
});
```

Suppose you create:

```ts
test("Sign Up", async ({ page }) => {
    ...
});

test("Login", async ({ page }) => {
    ...
});

test("Products", async ({ page }) => {
    ...
});
```

These tests can be executed independently with multiple workers.

But be careful with shared data.

For example, if all tests use:

```text
username = demo9144
```

parallel tests might interfere with each other.

Better:

```ts
const username = `user${Date.now()}`;
```

or generate unique test data.

---

# 11. Parallelism in CI/CD

Parallel execution becomes particularly useful in CI/CD.

For example:

```text
                 Test Suite
                     │
        ┌────────────┼────────────┐
        ↓            ↓            ↓
      Worker       Worker       Worker
        1             2            3
        ↓             ↓            ↓
     Login         Cart         Product
```

Instead of waiting for the entire suite sequentially, multiple tests execute simultaneously.

---

# 12. Parallelism vs Sharding

These are related but different.

### Parallelism

Multiple workers run tests on the **same machine**.

```text
Machine
 ├── Worker 1
 ├── Worker 2
 ├── Worker 3
 └── Worker 4
```

### Sharding

You divide the test suite across **multiple machines/CI jobs**.

```text
Machine 1 → Shard 1 → Tests 1-25
Machine 2 → Shard 2 → Tests 26-50
Machine 3 → Shard 3 → Tests 51-75
```

Example:

```bash
npx playwright test --shard=1/3
```

means:

> Run shard 1 out of 3 total shards.

Another machine could run:

```bash
npx playwright test --shard=2/3
```

and another:

```bash
npx playwright test --shard=3/3
```

---

# 13. Parallel Testing Example

```ts
import { test, expect } from "@playwright/test";

test.describe.configure({ mode: "parallel" });

test("Login Test", async ({ page }) => {

    await page.goto("https://www.demoblaze.com/");

    await page.locator("#login2").click();

    await page.locator("#loginusername").fill("demo9144");

    await page.locator("#loginpassword").fill("demo");

    await page.getByRole("button", { name: "Log in" }).click();
});

test("Home Page Test", async ({ page }) => {

    await page.goto("https://www.demoblaze.com/");

    await expect(page).toHaveTitle(/STORE/);
});

test("Product Test", async ({ page }) => {

    await page.goto("https://www.demoblaze.com/");

    await expect(
        page.getByText("Samsung galaxy s6")
    ).toBeVisible();
});
```

Because of:

```ts
test.describe.configure({ mode: "parallel" });
```

these tests can run concurrently.

---

# 14. Best Practices

### ✅ Good for parallel testing

* Tests are independent
* Unique test data
* No dependency between tests
* No shared mutable state
* Proper fixtures
* Multiple workers

### ❌ Avoid

```text
Test 1 creates data
      ↓
Test 2 uses data from Test 1
      ↓
Test 3 deletes data
```

This creates problems in parallel execution.

---

# Quick Revision

| Concept              | Meaning                                        |
| -------------------- | ---------------------------------------------- |
| **Parallel testing** | Execute multiple tests simultaneously          |
| **Worker**           | Process that executes tests                    |
| `workers: 4`         | Use up to 4 workers                            |
| `--workers=4`        | Set workers from command line                  |
| `mode: "parallel"`   | Run tests in a group in parallel               |
| `mode: "serial"`     | Run tests sequentially                         |
| **Sharding**         | Distribute tests across multiple machines/jobs |

### ⭐ Interview answer

> **Parallel testing in Playwright** allows multiple tests to execute simultaneously using workers. By default, Playwright can run test files in parallel. We can configure the number of workers using `workers` or `--workers`, and use `test.describe.configure({ mode: "parallel" })` when we want tests within a group to run in parallel. Tests should be independent and should not rely on shared state or execution order.
