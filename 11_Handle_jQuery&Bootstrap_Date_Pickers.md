## Handling jQuery & Bootstrap Date Pickers in Playwright

Date pickers are common in web applications. In Playwright, the approach depends on **how the date picker is implemented**.

The important thing is: **don't treat every date picker like a normal `<input>`**.

### 1. jQuery Date Picker

A typical jQuery date picker looks like this:

```html
<input type="text" id="datepicker">
```

When you click it, a calendar appears.

![Image](https://images.openai.com/static-rsc-4/cNSIpiX4m6AJ_X4VNzTWLcZ5ZMAfWkTQo1wSL6Tl7SfVgbMVCI4hfvfvS21xlfHHmNcmK56qspEoUb5bjZLTP-AmOxFCNnEq2PI1yWEhHcxUuDIVA5bYQDd7ndCMz10M1tFf3DHR1FW5nQe_KZxK4L-cS4Tu9wZOAJp52TLeRiq0_uBFmqaxeVbiz_h1SwRn?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/48i0oid_GIDHH-l7HVhWEdSPuuAKiyY7MnvqXL0sZ1bE2KH6P_9elmu917NP_YuqgQPoy-6PiIWrVzvud-ohvRESDx-FOqlKRvY2GK5tD4gdi0dyDZnzn6Bh9ciCqPyabyzoNbo2TejLkvOufZLs-IMVBPPygQ_YT8MYXGSwkPhBHEMEeoBGmA-EZHZvhqPJ?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/cwz8LeY869roGQzRrY1kVoZzgF19m1huRYbf7HAJO-Mgfmv22R2wXoESyhsZcnYmI6KZ_ytxrkQ_3ZXvASnNVE4V0UUmdo3xvMBFFPgplhy9IAWyoH1LTKuyEkZHOvXJliw4ghdM9w57Pa-m0lUpeL2hZGCW6JEZgWgBVZ7I2lZdlH8Xaem4h9VADiwtkwg9?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/fBP2VJEUoWM0agJ6S7RX8vScUWVoi5x6M8uGMR_wIpHaOK3fntFbUi57rkT5agWtpdgji_vwPTubY3eNLnRQiwEQFPRzFDmukgG2feR_HNW0H2TXO6qQvXkHGcNxYQaf1AePH3sfwFEnMRYbd67AW_F3OQNf5hJ31phpqyFRh3AzsGQ-JxGZm_5Sr7QF1cFW?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/OogymUZHNLCZMD0nrH59FBTUJa7melKisB9Vab1MO7Ci-uHSZegm2TCitaW5xgfJ2B5GezxnKqe4DvG0Lrj-6bDO3e5b1YBcc45N1hpvsKIhMQEop0FfwF8xPjoxOBGKpDOs3c4qDvGZOXu7vAWSgXOPt2ZIqmzfGwhxkCWjQImb9kH0cJkh0lsB9G-uVbg3?purpose=fullsize)

### Basic approach

```ts
import { test, expect } from "@playwright/test";

test("Handle jQuery Date Picker", async ({ page }) => {

    await page.goto("https://jqueryui.com/datepicker/");

    // jQuery UI demo is inside iframe
    const frame = page.frameLocator(".demo-frame");

    await frame.locator("#datepicker").click();

    // Select date
    await frame.getByRole("link", { name: "15" }).click();

});
```

### 2. Selecting a Specific Month and Year

Suppose you need:

**15 September 2026**

You should not simply click `15`, because there may be a `15` from another month.

Instead:

```ts
await frame.locator("#ui-datepicker-div .ui-datepicker-title").waitFor();

const month = frame.locator(".ui-datepicker-month");
const year = frame.locator(".ui-datepicker-year");

await month.selectOption({ label: "Sep" });
await year.selectOption("2026");

await frame.locator(".ui-datepicker-calendar")
    .getByRole("link", { name: "15", exact: true })
    .click();
```

However, the exact selectors depend on the date-picker implementation.

---

# 3. Bootstrap Date Picker

Bootstrap date pickers often look like:

![Image](https://images.openai.com/static-rsc-4/AT6YcUXJCLcmY7LsmnUXIqRHJ-erB6v9OGZCQWGBirucv5MWK-4KLFPmab7gs9euDuU5NsDdCAcyhB1r4fW9Mff8XOJX-CNVHZuimhMqAEZWEnB0pw6jysNryRKpufOKBH4HiQHYMoCUWv3ETEvjM3Rc4YECAolctlzvYkS5yvjw212NW0hfZ6JetYGHhrRt?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/bu1dY4A7Nkgn-nYdKM8w5B8ItSD_3TfPmahqQYz7VK6gVn7AxSZHKlb9AKUVnmg0kEIbrqjMsBvRVAUGm-RQ0RqDbTlZe_hFw1QoUh-9IJ74JrcPlVhHtlVJXSFeYIPxnMEw34RLgHBoNXNL5A45pFGWrxIITvCFGebIJM6VT1KyYVzMr3a6wPcYZnus7RI6?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/isnai2bdPYaMC2dYnR3qkUwMu9aR9dre26E1l-3Hl-zQNyxhPsFyh3e5eWKo5B7x7uEG26PXwaG3WANEkNGnlnyGqHL_PIMl5x5eB3t3vRWRQZVZ-E4yCLnpMKuWCFJ8S8uDwXzLFWHP3sCoLFmRlwRBM9cqbNDw8qrzAFD5_OZrB1MTlM06L96SgDQ1R7Sz?purpose=fullsize)

Example HTML:

```html
<input type="text" class="form-control" id="date">
```

Click the input:

```ts
await page.locator("#date").click();
```

Then inspect the calendar that appears.

For example:

```ts
await page.locator(".datepicker").getByText("15", { exact: true }).click();
```

---

# 4. The Most Important Technique: Inspect the Calendar

When you click a date-picker, **inspect the calendar DOM**.

For example, you might see:

```html
<div class="datepicker">
    <div class="datepicker-days">
        <table>
            <tbody>
                <tr>
                    <td class="day">12</td>
                    <td class="day">13</td>
                    <td class="day">14</td>
                    <td class="day">15</td>
                </tr>
            </tbody>
        </table>
    </div>
</div>
```

Then Playwright can use:

```ts
await page.locator(".datepicker")
    .locator(".day")
    .getByText("15", { exact: true })
    .click();
```

---

# 5. Handling Next / Previous Month

This is the most common interview/test scenario.

Suppose the current month is **August 2026**, but you need **December 2026**.

You can click the **Next** button repeatedly.

```ts
while (await page.locator(".datepicker .next").isVisible()) {

    const currentMonth =
        await page.locator(".datepicker .datepicker-switch").textContent();

    if (currentMonth?.includes("December")) {
        break;
    }

    await page.locator(".datepicker .next").click();
}
```

Then select the day:

```ts
await page.locator(".datepicker .day")
    .getByText("15", { exact: true })
    .click();
```

The exact classes (`.next`, `.datepicker-switch`, etc.) depend on the Bootstrap date-picker library you're using.

---

# 6. Better Approach: Create a Reusable Method

For automation frameworks, don't write the same date-picker logic in every test.

Create a method:

```ts
async function selectDate(
    page,
    date: string
) {
    await page.locator("#date").click();

    // Navigate to required month/year
    // ...

    // Select required day
    await page.locator(".datepicker .day")
        .getByText(date, { exact: true })
        .click();
}
```

Then:

```ts
await selectDate(page, "15");
```

---

# 7. If the Date Is Directly Editable

Sometimes the calendar is only a UI wrapper around an input:

```html
<input id="date" type="text">
```

In that case, you may not need to interact with the calendar at all.

You can do:

```ts
await page.locator("#date").fill("15/09/2026");
```

Or:

```ts
await page.locator("#date").pressSequentially("15/09/2026");
```

**But only use this if the application allows direct input.**

---

## Key Difference

| Date picker            | Playwright approach                      |
| ---------------------- | ---------------------------------------- |
| Normal `<input>`       | `fill()`                                 |
| jQuery UI              | Click → navigate month/year → select day |
| Bootstrap Datepicker   | Click → navigate → select day            |
| Custom date picker     | Inspect DOM → interact with its elements |
| `<input type="date">`  | `fill("2026-09-15")`                     |
| Calendar inside iframe | `frameLocator()`                         |

### Best practice

Don't build a complicated XPath like:

```ts
//td[contains(text(),'15')]
```

Prefer Playwright locators:

```ts
page.getByRole("button", { name: "15" })
```

or:

```ts
page.locator(".day").getByText("15", { exact: true })
```

**The key workflow is:**

**Click input → inspect calendar → identify month/year controls → navigate → select exact day → verify input value.**
