# Installation Process :

1) Node.js
2) Playwright ```npm init playwright@latest```


## 🚀 Playwright + TypeScript Roadmap

```text
TypeScript basics
       ↓
Playwright basics
       ↓
Locators
       ↓
Actions
       ↓
Assertions
       ↓
Waits
       ↓
Fixtures & Hooks
       ↓
Page Object Model
       ↓
Test Data
       ↓
API Testing
       ↓
Advanced Playwright
       ↓
Reports & Debugging
       ↓
Parallel Testing
       ↓
CI/CD
       ↓
Real Automation Framework
```

---

# 1. TypeScript basics

You don't need to become an expert in TypeScript first.

Learn these:

### Must know

```text
let / const
string
number
boolean
array
object
interface
type
function
arrow function
class
constructor
public / private
async / await
Promise
```

Especially understand:

```typescript
const name: string = "Gajanan";

const age: number = 25;

const active: boolean = true;
```

Then:

```typescript
interface User {
    name: string;
    age: number;
}
```

And:

```typescript
async function login(): Promise<void> {
    // ...
}
```

### Time

**3–5 days**

Don't spend one month learning TypeScript before touching Playwright.

---

# 2. Install Playwright

Create a project:

```bash
npm init playwright@latest
```

Choose:

```text
TypeScript
```

You'll get something like:

```text
playwright-project
│
├── tests/
│   └── example.spec.ts
│
├── playwright.config.ts
├── package.json
└── tsconfig.json
```

Run tests:

```bash
npx playwright test
```

Open the HTML report:

```bash
npx playwright show-report
```

---

# 3. Understand Playwright architecture

Learn these four things first:

```text
Playwright
    ↓
Browser
    ↓
Browser Context
    ↓
Page
```

Understand what each one does.

Then learn:

```text
test()
expect()
```

Your basic test structure becomes:

```typescript
import { test, expect } from '@playwright/test';

test('login test', async ({ page }) => {

    await page.goto('https://example.com');

    // actions

    // assertions
});
```

---

# 4. Locators ⭐⭐⭐

Spend serious time here.

Learn:

```typescript
getByRole()
getByText()
getByLabel()
getByPlaceholder()
getByTestId()
locator()
```

For example:

```typescript
await page.getByRole('button', { name: 'Login' }).click();
```

Learn how to locate:

```text
Text box
Button
Link
Checkbox
Radio button
Dropdown
Table
Image
```

Also understand:

```text
CSS selectors
XPath
```

But don't make XPath your first choice.

### Practice

Take a website and locate **20 different elements**.

---

# 5. Actions

Learn all common browser actions:

```text
click()
fill()
press()
check()
uncheck()
selectOption()
hover()
focus()
clear()
```

Then advanced actions:

```text
dragAndDrop()
upload files
download files
keyboard
mouse
```

Build small tests.

Example:

```text
Open login page
      ↓
Enter username
      ↓
Enter password
      ↓
Click login
      ↓
Verify dashboard
```

---

# 6. Assertions ⭐⭐⭐

Learn:

```typescript
expect()
```

Understand assertions for:

```text
URL
Title
Text
Visibility
Enabled
Disabled
Checked
Value
Attribute
```

For example:

```typescript
await expect(page).toHaveTitle(/Dashboard/);
```

and:

```typescript
await expect(page.getByText('Welcome')).toBeVisible();
```

Your mindset should be:

```text
ACTION
  ↓
EXPECTED RESULT
  ↓
ASSERTION
```

---

# 7. Waiting ⭐⭐⭐

This is very important.

Learn:

```text
Auto waiting
Locator waiting
waitForURL()
waitForResponse()
waitForLoadState()
```

Understand why Playwright waits automatically.

Avoid doing this everywhere:

```typescript
await page.waitForTimeout(5000);
```

You should understand **condition-based waiting** instead of arbitrary sleeping.

---

# 8. Test hooks

Learn:

```typescript
beforeEach()
afterEach()
beforeAll()
afterAll()
```

For example:

```text
beforeEach
    ↓
Open application
    ↓
Test
    ↓
afterEach
    ↓
Cleanup
```

---

# 9. Fixtures ⭐⭐⭐

This is where you start moving toward professional Playwright.

Understand:

```typescript
test
page
browser
context
request
```

Then learn how to create **custom fixtures**.

For example:

```text
Login fixture
    ↓
Create authenticated session
    ↓
Tests use logged-in user
```

---

# 10. Page Object Model ⭐⭐⭐

This is essential for a framework.

Instead of:

```text
login.spec.ts
    ↓
locators
    ↓
actions
```

create:

```text
pages/
   LoginPage.ts
   HomePage.ts
   ProductPage.ts

tests/
   login.spec.ts
   product.spec.ts
```

Example architecture:

```text
LoginTest
     ↓
LoginPage
     ↓
Browser
```

Your test should read almost like a business scenario:

```text
Open login page
Login with valid credentials
Verify dashboard
```

while the actual locators stay inside `LoginPage.ts`.

---

# 11. Test data

Learn how to manage:

```text
Username
Password
Products
Users
URLs
Environment variables
```

For example:

```text
.env
test-data/
config/
```

Don't hard-code everything inside your tests.

---

# 12. API Testing ⭐⭐

Playwright can also test APIs.

Learn:

```text
GET
POST
PUT
PATCH
DELETE
```

Example architecture:

```text
API
 ↓
Create user
 ↓
UI
 ↓
Login user
 ↓
Verify user
```

This is very useful for real QA automation.

---

# 13. Authentication

Learn how to avoid logging in before every test.

Understand:

```text
storageState
```

Architecture:

```text
Login once
    ↓
Save authentication state
    ↓
Reuse state
    ↓
Run tests
```

This can make your test suite significantly faster.

---

# 14. Debugging ⭐⭐⭐

Master:

```bash
npx playwright test --debug
```

Learn:

```text
Playwright Inspector
Trace Viewer
Screenshots
Videos
HTML reports
```

Especially understand **Trace Viewer**.

When a test fails, you should be able to determine:

```text
What happened?
 ↓
Which locator failed?
 ↓
What was the page state?
 ↓
What happened immediately before failure?
```

---

# 15. Multiple browsers

Learn how to run tests against:

```text
Chromium
Firefox
WebKit
```

Then understand projects in:

```text
playwright.config.ts
```

For example:

```text
Chrome
Firefox
Safari
```

---

# 16. Parallel execution

Learn:

```text
workers
parallel
projects
sharding
```

Understand how to safely run:

```text
Test 1 ──┐
Test 2 ──┤
Test 3 ──┼──> Parallel
Test 4 ──┤
Test 5 ──┘
```

Also understand why tests need to be **independent**.

---

# 17. Reports

Learn:

```text
HTML reporter
JUnit reporter
JSON reporter
```

For company projects, reports are important because CI/CD systems need machine-readable results.

---

# 18. CI/CD ⭐⭐⭐

Learn:

```text
Git
GitHub
GitHub Actions
```

Your final workflow should look like:

```text
Developer
   ↓
Git push
   ↓
GitHub
   ↓
GitHub Actions
   ↓
Install dependencies
   ↓
Run Playwright
   ↓
Generate report
   ↓
Pass / Fail
```

---

# 19. Build a real framework

Once you know the above, create:

```text
playwright-framework/
│
├── tests/
│   ├── login.spec.ts
│   ├── product.spec.ts
│   └── checkout.spec.ts
│
├── pages/
│   ├── LoginPage.ts
│   ├── HomePage.ts
│   ├── ProductPage.ts
│   └── CheckoutPage.ts
│
├── fixtures/
│   └── testFixtures.ts
│
├── utils/
│   ├── testData.ts
│   └── helpers.ts
│
├── test-data/
│
├── playwright.config.ts
├── package.json
└── tsconfig.json
```

---

# 🏆 Project to master Playwright

Don't just do tutorials.

Build an **e-commerce automation framework**.

### Test cases

```text
1. Login with valid credentials
2. Login with invalid credentials
3. Search product
4. Filter product
5. Open product
6. Add product to cart
7. Remove product
8. Update quantity
9. Checkout
10. Logout
```

Then add:

```text
✓ Page Object Model
✓ Fixtures
✓ Authentication state
✓ API tests
✓ Multiple browsers
✓ Parallel execution
✓ Screenshots
✓ Trace
✓ Reports
✓ GitHub
✓ CI/CD
```

---




