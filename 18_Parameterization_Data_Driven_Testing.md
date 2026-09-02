# Parameterization | Data-Driven Testing in Playwright

**Parameterization** means running the same test with **different sets of data** instead of writing separate tests for every data set.

For example, instead of:

```text
Login with user1
Login with user2
Login with user3
```

you write **one test** and provide different data.

---

## 1. Simple Parameterization Using an Array

Suppose we want to test multiple users:

```ts
import { test, expect } from "@playwright/test";

const users = [
    { username: "user1", password: "pass1" },
    { username: "user2", password: "pass2" },
    { username: "user3", password: "pass3" }
];

for (const user of users) {

    test(`Login test - ${user.username}`, async ({ page }) => {

        await page.goto("https://www.demoblaze.com/");

        await page.locator("#login2").click();

        await page.locator("#loginusername")
            .fill(user.username);

        await page.locator("#loginpassword")
            .fill(user.password);

        await page.getByRole("button", {
            name: "Log in"
        }).click();
    });
}
```

Playwright will create **three tests**:

```text
Login test - user1
Login test - user2
Login test - user3
```

---

# 2. Why Use Data-Driven Testing?

Without parameterization:

```ts
test("Login user1", async ({ page }) => {
    // login user1
});

test("Login user2", async ({ page }) => {
    // same code
});

test("Login user3", async ({ page }) => {
    // same code
});
```

This creates duplicate code.

With parameterization:

```ts
const users = [
    { username: "user1", password: "pass1" },
    { username: "user2", password: "pass2" },
    { username: "user3", password: "pass3" }
];

for (const user of users) {
    test(`Login - ${user.username}`, async ({ page }) => {
        // common test logic
    });
}
```

Much cleaner.

---

# 3. Parameterizing Expected Results

Data-driven testing isn't only for input data.

You can also provide the expected result.

```ts
const loginData = [
    {
        username: "validUser",
        password: "validPass",
        expected: true
    },
    {
        username: "invalidUser",
        password: "wrongPass",
        expected: false
    }
];
```

Then:

```ts
for (const data of loginData) {

    test(`Login - ${data.username}`, async ({ page }) => {

        await page.goto("https://www.demoblaze.com/");

        await page.locator("#login2").click();

        await page.locator("#loginusername")
            .fill(data.username);

        await page.locator("#loginpassword")
            .fill(data.password);

        await page.getByRole("button", {
            name: "Log in"
        }).click();

        if (data.expected) {
            // verify successful login
        } else {
            // verify error message
        }
    });
}
```

---

# 4. Using an Array of Arrays

You can also use simple arrays:

```ts
const loginData = [
    ["user1", "pass1"],
    ["user2", "pass2"],
    ["user3", "pass3"]
];

for (const [username, password] of loginData) {

    test(`Login test - ${username}`, async ({ page }) => {

        await page.goto("https://www.demoblaze.com/");

        await page.locator("#login2").click();

        await page.locator("#loginusername")
            .fill(username);

        await page.locator("#loginpassword")
            .fill(password);

    });
}
```

However, **objects are generally easier to maintain** when you have many fields.

---

# 5. Parameterizing Search Data

For example:

```ts
const products = [
    "Samsung galaxy s6",
    "Nokia lumia 1520",
    "Sony xperia z5"
];

for (const product of products) {

    test(`Verify product - ${product}`, async ({ page }) => {

        await page.goto("https://www.demoblaze.com/");

        await expect(
            page.getByText(product)
        ).toBeVisible();

    });
}
```

This generates:

```text
Verify product - Samsung galaxy s6
Verify product - Nokia lumia 1520
Verify product - Sony xperia z5
```

---

# 6. Data-Driven Testing + `describe`

You can also organize parameterized tests inside a group:

```ts
import { test, expect } from "@playwright/test";

const products = [
    "Samsung galaxy s6",
    "Nokia lumia 1520",
    "Sony xperia z5"
];

test.describe("Product Tests", () => {

    for (const product of products) {

        test(`Verify ${product}`, async ({ page }) => {

            await page.goto("https://www.demoblaze.com/");

            await expect(
                page.getByText(product)
            ).toBeVisible();

        });

    }

});
```

Structure:

```text
Product Tests
│
├── Verify Samsung galaxy s6
├── Verify Nokia lumia 1520
└── Verify Sony xperia z5
```

---

# 7. External Test Data

In real projects, test data may come from:

```text
CSV
Excel
JSON
Database
API
```

For example, a JSON file:

### `testData.json`

```json
[
    {
        "username": "user1",
        "password": "pass1"
    },
    {
        "username": "user2",
        "password": "pass2"
    },
    {
        "username": "user3",
        "password": "pass3"
    }
]
```

Then import it:

```ts
import testData from "./testData.json";
```

and use:

```ts
for (const data of testData) {

    test(`Login - ${data.username}`, async ({ page }) => {

        await page.goto("https://www.demoblaze.com/");

        await page.locator("#login2").click();

        await page.locator("#loginusername")
            .fill(data.username);

        await page.locator("#loginpassword")
            .fill(data.password);

    });
}
```

If you're using TypeScript, your `tsconfig.json` may need:

```json
{
    "compilerOptions": {
        "resolveJsonModule": true
    }
}
```

---

# 8. Parameterization + Parallelism

This is particularly powerful.

Suppose:

```ts
const users = [
    { username: "user1", password: "pass1" },
    { username: "user2", password: "pass2" },
    { username: "user3", password: "pass3" }
];
```

The loop creates three separate Playwright tests.

If you enable parallel execution, they can run concurrently:

```text
                Users
                  │
       ┌──────────┼──────────┐
       ↓          ↓          ↓
    Worker 1   Worker 2   Worker 3
       ↓          ↓          ↓
     user1      user2      user3
```

This is one of the advantages of creating **separate tests** rather than putting all data iterations inside one test.

---

# 9. Important Difference

### This:

```ts
for (const user of users) {

    test(`Login ${user.username}`, async ({ page }) => {
        // test
    });

}
```

creates **multiple Playwright tests**.

But this:

```ts
test("Login users", async ({ page }) => {

    for (const user of users) {
        // login
    }

});
```

creates **one Playwright test** containing multiple iterations.

### Prefer separate tests when:

* You want separate reporting
* You want independent failures
* You want parallel execution
* You want to retry individual data sets

---

# 10. Practical Example for Your DemoBlaze Practice

```ts
import { test, expect } from "@playwright/test";

const users = [
    {
        username: "demo9144",
        password: "demo"
    },
    {
        username: "demo123",
        password: "demo"
    },
    {
        username: "demo456",
        password: "demo"
    }
];

for (const user of users) {

    test(`Login Test - ${user.username}`, async ({ page }) => {

        await page.goto("https://www.demoblaze.com/");

        await page.locator("#login2").click();

        await page.locator("#loginusername")
            .fill(user.username);

        await page.locator("#loginpassword")
            .fill(user.password);

        await page.getByRole("button", {
            name: "Log in"
        }).click();

        // Add your expected-result assertion here
        // based on whether the account is valid.
    });
}
```

---

## ⭐ Easy Interview Answer

> **Parameterization or data-driven testing** means executing the same test logic with multiple sets of test data. In Playwright, we can achieve this using JavaScript/TypeScript arrays or objects and generate separate tests using a `for...of` loop. Test data can also be maintained externally in JSON, CSV, Excel, or other data sources. Creating separate tests for each data set provides better reporting, independent failures, retries, and parallel execution.

### Remember this pattern

```ts
const testData = [
    { input: "A", expected: "X" },
    { input: "B", expected: "Y" },
    { input: "C", expected: "Z" }
];

for (const data of testData) {

    test(`Test - ${data.input}`, async ({ page }) => {

        // use data.input

        // verify data.expected

    });
}
```

This is the **basic pattern you should remember for Playwright data-driven testing**.
