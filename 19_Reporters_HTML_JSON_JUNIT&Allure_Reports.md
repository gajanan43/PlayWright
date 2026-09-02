# Reporters | HTML, JSON, JUnit & Allure Reports in Playwright

A **reporter** determines how Playwright presents the results of your test execution.

Instead of only seeing:

```text
3 passed
1 failed
```

a reporter can provide detailed information about:

* Passed tests
* Failed tests
* Skipped tests
* Test duration
* Errors
* Screenshots
* Videos
* Traces
* Test suites

---

# 1. Default List Reporter

When you run:

```bash
npx playwright test
```

Playwright displays results in the terminal.

Example:

```text
Running 3 tests using 3 workers

✓ login.spec.ts
✓ product.spec.ts
✘ checkout.spec.ts

2 passed
1 failed
```

This is useful while developing tests, but for a project we often need a more detailed report.

---

# 2. HTML Reporter

The **HTML reporter** creates a graphical report that you can open in a browser.

Configure it in `playwright.config.ts`:

```ts
import { defineConfig } from "@playwright/test";

export default defineConfig({
    reporter: [["html", { outputFolder: "playwright-report" }]]
});
```

Run:

```bash
npx playwright test
```

Then open the report:

```bash
npx playwright show-report
```

You'll get a report similar to:

```text
Test Report
│
├── ✓ Login Test
├── ✓ Product Test
├── ✘ Checkout Test
└── ✓ Logout Test
```

You can click a failed test and investigate its details.

### Why HTML report is useful

It provides a user-friendly interface for:

* Test results
* Errors
* Duration
* Test steps
* Attachments
* Screenshots
* Videos
* Traces

For learning and most projects, **HTML reporter is the first reporter you should learn**.

---

# 3. JSON Reporter

JSON reporter generates test results in **JSON format**.

Configure:

```ts
export default defineConfig({
    reporter: [
        ["json", { outputFile: "test-results/results.json" }]
    ]
});
```

Run:

```bash
npx playwright test
```

You will get:

```text
test-results/
    results.json
```

The JSON file contains structured test information.

Conceptually:

```json
{
    "config": {},
    "suites": [
        {
            "title": "Login Test",
            "specs": []
        }
    ]
}
```

### Why use JSON?

JSON is useful when another application needs to **consume test results programmatically**.

For example:

```text
Playwright
    ↓
results.json
    ↓
CI/CD system
    ↓
Dashboard
```

---

# 4. JUnit Reporter

JUnit produces test results in **XML format**.

Configure:

```ts
export default defineConfig({
    reporter: [
        ["junit", { outputFile: "test-results/results.xml" }]
    ]
});
```

Run:

```bash
npx playwright test
```

Output:

```text
test-results/
    results.xml
```

### Why JUnit?

JUnit XML is commonly understood by CI/CD systems.

For example:

```text
Playwright
    ↓
JUnit XML
    ↓
Jenkins / CI system
    ↓
Test Results
```

So if your company uses Jenkins or another CI platform, JUnit reporting is often useful.

---

# 5. Allure Report

**Allure** is a third-party reporting framework that provides a rich test report.

Unlike Playwright's built-in HTML, JSON, and JUnit reporters, Allure requires additional setup.

A common setup uses the Playwright Allure reporter package.

Install:

```bash
npm install -D allure-playwright
```

Then configure:

```ts
import { defineConfig } from "@playwright/test";

export default defineConfig({
    reporter: [
        ["allure-playwright"]
    ]
});
```

Run your tests:

```bash
npx playwright test
```

Then generate/open the Allure report using the Allure CLI.

Typical commands are:

```bash
allure generate allure-results
```

and:

```bash
allure open
```

Or, for local development:

```bash
allure serve allure-results
```

---

# 6. Allure Report Structure

Allure gives you a rich dashboard containing information such as:

```text
Allure Report
│
├── Overview
├── Suites
├── Graphs
├── Categories
├── Timeline
├── Behaviors
└── Attachments
```

It's particularly useful for larger automation projects.

---

# 7. Multiple Reporters Together

This is **very important**.

You don't have to choose only one reporter.

You can configure multiple reporters:

```ts
import { defineConfig } from "@playwright/test";

export default defineConfig({
    reporter: [
        ["list"],
        ["html", {
            outputFolder: "playwright-report"
        }],
        ["json", {
            outputFile: "test-results/results.json"
        }],
        ["junit", {
            outputFile: "test-results/results.xml"
        }]
    ]
});
```

Now one test execution produces:

```text
                 Playwright
                     │
       ┌─────────────┼──────────────┐
       ↓             ↓              ↓
     HTML           JSON           JUnit
       ↓             ↓              ↓
 Browser report   API/data      CI/CD system
```

This is a very practical configuration.

---

# 8. HTML + JSON + JUnit + Allure

You can also configure all of them:

```ts
import { defineConfig } from "@playwright/test";

export default defineConfig({

    reporter: [
        ["html", {
            outputFolder: "playwright-report"
        }],

        ["json", {
            outputFile: "test-results/results.json"
        }],

        ["junit", {
            outputFile: "test-results/results.xml"
        }],

        ["allure-playwright"]
    ]

});
```

Then:

```bash
npx playwright test
```

will generate results for all configured reporters.

---

# 9. Reporter Comparison

| Reporter   | Output         | Main Purpose                      |
| ---------- | -------------- | --------------------------------- |
| **List**   | Terminal       | Quick local results               |
| **HTML**   | HTML           | Human-friendly interactive report |
| **JSON**   | `.json`        | Programmatic processing           |
| **JUnit**  | `.xml`         | CI/CD integration                 |
| **Allure** | HTML dashboard | Rich automation reporting         |

---

# 10. Which Reporter Should You Use?

### For learning

Use:

```ts
reporter: "html"
```

Then:

```bash
npx playwright test
npx playwright show-report
```

### For a real project

A common combination is:

```ts
reporter: [
    ["list"],
    ["html"],
    ["junit"]
]
```

You get:

```text
Terminal → Developer
HTML     → QA / Developer
JUnit    → CI/CD
```

### For advanced reporting

Add Allure:

```text
List
 +
HTML
 +
JUnit
 +
Allure
```

---

# 11. Reporter + Screenshot + Video + Trace

Reporters become especially useful when combined with debugging artifacts.

For example:

```ts
import { defineConfig } from "@playwright/test";

export default defineConfig({

    reporter: [
        ["html", {
            outputFolder: "playwright-report"
        }],
        ["junit", {
            outputFile: "test-results/results.xml"
        }]
    ],

    use: {
        screenshot: "only-on-failure",
        video: "retain-on-failure",
        trace: "on-first-retry"
    }

});
```

Now your flow becomes:

```text
                    Test
                     │
             ┌───────┴───────┐
             ↓               ↓
          Passed           Failed
                             │
                  ┌──────────┼──────────┐
                  ↓          ↓          ↓
             Screenshot    Video      Trace
                  │          │          │
                  └──────────┼──────────┘
                             ↓
                       HTML Report
```

This is a **very useful setup for debugging failed tests**.

---

# 12. Running Different Reporters from Command Line

You can also specify a reporter when running tests.

For example:

```bash
npx playwright test --reporter=html
```

Or:

```bash
npx playwright test --reporter=list
```

Or:

```bash
npx playwright test --reporter=json
```

This is useful when you don't want to permanently change your configuration.

---

# 13. Important Difference: Reporter vs Trace

Don't confuse these two.

### Reporter

Answers:

> **What was the result of my test?**

Example:

```text
Login Test → Passed
Checkout Test → Failed
```

### Trace

Answers:

> **What exactly happened during the test?**

For example:

```text
goto()
 ↓
click()
 ↓
fill()
 ↓
click()
 ↓
FAIL ❌
```

So:

```text
Reporter → Test results
Trace    → Detailed debugging
```

---

# 14. Recommended `playwright.config.ts`

For your current learning path, I'd recommend starting with:

```ts
import { defineConfig } from "@playwright/test";

export default defineConfig({

    reporter: [
        ["list"],
        ["html", {
            outputFolder: "playwright-report"
        }]
    ],

    use: {
        screenshot: "only-on-failure",
        video: "retain-on-failure",
        trace: "on-first-retry"
    }

});
```

Then run:

```bash
npx playwright test
```

and:

```bash
npx playwright show-report
```

Once you're comfortable with HTML reports, add:

```ts
["json", {
    outputFile: "test-results/results.json"
}],

["junit", {
    outputFile: "test-results/results.xml"
}]
```

and later Allure.

---

## ⭐ Interview Answer

> **Playwright reporters are used to present and store test execution results.** Playwright provides built-in reporters such as List, HTML, JSON, and JUnit. HTML is useful for interactive human-readable reports, JSON is useful for programmatic processing, and JUnit XML is commonly used for CI/CD integration. Allure is a third-party reporting framework that provides a richer dashboard with additional test organization and visualization features. Multiple reporters can be configured together in `playwright.config.ts`.

### Remember

```text
List   → Terminal
HTML   → Human-readable report
JSON   → Data / automation
JUnit  → CI/CD
Allure → Rich reporting dashboard
```
