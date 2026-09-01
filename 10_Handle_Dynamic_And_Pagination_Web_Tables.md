# Handling Dynamic and Pagination Web Tables in Playwright

Web tables are very common in automation testing. The main challenge is that **rows may change dynamically** and data may be spread across **multiple pages**.

Let's understand this step by step.

---

## 1. Basic HTML Table

Suppose we have:

```html
<table id="employees">
    <thead>
        <tr>
            <th>Name</th>
            <th>Age</th>
            <th>Department</th>
        </tr>
    </thead>

    <tbody>
        <tr>
            <td>Gajanan</td>
            <td>25</td>
            <td>IT</td>
        </tr>
        <tr>
            <td>Rahul</td>
            <td>28</td>
            <td>HR</td>
        </tr>
    </tbody>
</table>
```

In Playwright:

```ts
const table = page.locator("#employees");
```

---

# 2. Get Number of Rows

```ts
const rows = page.locator("#employees tbody tr");

console.log(await rows.count());
```

If there are 2 rows:

```text
2
```

---

# 3. Get Number of Columns

```ts
const columns = page.locator("#employees thead th");

console.log(await columns.count());
```

---

# 4. Get Text From a Particular Row

```ts
const row = page.locator("#employees tbody tr").nth(0);

console.log(await row.innerText());
```

Output:

```text
Gajanan    25    IT
```

---

# 5. Get Text From a Particular Cell

For example, first row, first column:

```ts
const cell = page
    .locator("#employees tbody tr")
    .nth(0)
    .locator("td")
    .nth(0);

console.log(await cell.innerText());
```

Output:

```text
Gajanan
```

---

# 6. Read All Rows

A common approach:

```ts
const rows = page.locator("#employees tbody tr");

const rowCount = await rows.count();

for (let i = 0; i < rowCount; i++) {
    console.log(await rows.nth(i).innerText());
}
```

Output:

```text
Gajanan    25    IT
Rahul      28    HR
```

---

# 7. Read Every Cell

You can also loop through columns:

```ts
const rows = page.locator("#employees tbody tr");

for (let i = 0; i < await rows.count(); i++) {

    const cells = rows.nth(i).locator("td");

    for (let j = 0; j < await cells.count(); j++) {
        console.log(await cells.nth(j).innerText());
    }
}
```

---

# 8. Find a Particular Row

Suppose you want to find the row containing **Gajanan**.

Instead of manually looping, Playwright provides `filter()`:

```ts
const row = page
    .locator("#employees tbody tr")
    .filter({ hasText: "Gajanan" });

console.log(await row.innerText());
```

This is a very useful Playwright technique.

---

# 9. Find a Button Inside a Row

Suppose your table is:

```html
<tr>
    <td>Gajanan</td>
    <td>25</td>
    <td><button>Edit</button></td>
</tr>
```

You can locate the row and then the button:

```ts
const row = page
    .locator("tbody tr")
    .filter({ hasText: "Gajanan" });

await row.getByRole("button", { name: "Edit" }).click();
```

### ⭐ This is better than:

```ts
page.locator("button").nth(5).click();
```

because the index can change when the table changes.

---

# 10. Dynamic Web Tables

A dynamic table means rows/data are generated or changed at runtime.

For example:

```html
<table>
    <tbody>
        <!-- Rows generated dynamically -->
    </tbody>
</table>
```

Don't assume a fixed number of rows.

Instead:

```ts
const rows = page.locator("table tbody tr");

await expect(rows.first()).toBeVisible();

console.log("Rows:", await rows.count());
```

Then process the rows dynamically.

---

# 11. Example: Find a User and Click Delete

Suppose:

```text
Name       Role       Action

Gajanan    Developer  Delete
Rahul      Tester     Delete
Amit       Manager    Delete
```

You want to delete **Rahul**.

```ts
const row = page
    .locator("table tbody tr")
    .filter({ hasText: "Rahul" });

await row.getByRole("button", { name: "Delete" }).click();
```

This is a very good approach for dynamic tables.

---

# 12. Pagination

Now consider a table:

```text
Page 1

Gajanan
Rahul
Amit

[Previous] [Next]
```

Clicking **Next** loads:

```text
Page 2

John
David
Steve
```

You need to handle the pagination.

---

## Basic Pagination Example

```ts
while (true) {

    const rows = page.locator("table tbody tr");

    console.log("Rows:", await rows.count());

    for (let i = 0; i < await rows.count(); i++) {
        console.log(await rows.nth(i).innerText());
    }

    const nextButton = page.getByRole("button", { name: "Next" });

    if (await nextButton.isDisabled()) {
        break;
    }

    await nextButton.click();
}
```

This processes each page until the **Next** button becomes disabled.

---

# 13. Pagination With "Next" Link

Some websites use:

```html
<a>Next</a>
```

instead of a button.

Then:

```ts
const next = page.getByRole("link", { name: "Next" });

while (await next.isVisible()) {

    // Process current page

    await next.click();
}
```

But you should also account for the final page, where the link may still exist but be disabled/hidden.

---

# 14. Search for a Particular Record Across Pages

This is a very common automation interview question.

> Find "Gajanan" in a paginated table and click Edit.

Example:

```ts
while (true) {

    const row = page
        .locator("table tbody tr")
        .filter({ hasText: "Gajanan" });

    if (await row.count() > 0) {
        await row.getByRole("button", { name: "Edit" }).click();
        break;
    }

    const nextButton = page.getByRole("button", { name: "Next" });

    if (await nextButton.isDisabled()) {
        console.log("Gajanan not found");
        break;
    }

    await nextButton.click();
}
```

### Logic

```text
Page 1
   ↓
Search Gajanan
   ↓
Found? → Yes → Click Edit
   ↓ No
Click Next
   ↓
Page 2
   ↓
Search Gajanan
   ↓
Found? → Yes → Click Edit
   ↓ No
Click Next
   ↓
...
```

---

# 15. Important: Wait for New Page Data

For dynamic pagination, clicking Next may trigger an API request and update the table.

Instead of using arbitrary:

```ts
await page.waitForTimeout(2000);
```

prefer waiting for something meaningful.

For example:

```ts
await nextButton.click();

await expect(
    page.locator("table tbody tr").first()
).toBeVisible();
```

Or, if a page number changes:

```ts
await nextButton.click();

await expect(page.getByText("Page 2")).toBeVisible();
```

### ❌ Avoid unnecessary fixed waits

```ts
await page.waitForTimeout(3000);
```

Playwright's auto-waiting and web-first assertions are generally better.

---

# 16. Get All Table Data

If you want to collect the table data:

```ts
const rows = page.locator("table tbody tr");

const data: string[][] = [];

for (let i = 0; i < await rows.count(); i++) {

    const cells = rows.nth(i).locator("td");

    const rowData: string[] = [];

    for (let j = 0; j < await cells.count(); j++) {
        rowData.push((await cells.nth(j).innerText()).trim());
    }

    data.push(rowData);
}

console.log(data);
```

Result:

```text
[
    ["Gajanan", "25", "IT"],
    ["Rahul", "28", "HR"]
]
```

---

# 17. Using `allInnerTexts()`

For simple cases, you can make it shorter:

```ts
const rows = page.locator("table tbody tr");

const data = await rows.allInnerTexts();

console.log(data);
```

Output:

```text
[
    "Gajanan    25    IT",
    "Rahul      28    HR"
]
```

If you need individual columns, use `td` locators.

---

# 18. Interview Question

### Q: How do you handle a dynamic web table in Playwright?

A good answer:

> I avoid hardcoding row indexes because the number and position of rows can change dynamically. I locate the table rows using a stable locator, use `filter({ hasText })` to identify the required row, and then locate the required action within that row.

Example:

```ts
const row = page
    .locator("table tbody tr")
    .filter({ hasText: "Gajanan" });

await row.getByRole("button", { name: "Edit" }).click();
```

### Q: How do you handle pagination?

> I process the current page, check whether the Next button is enabled, click it, wait for the new table state, and continue until there are no more pages.

```ts
while (true) {

    // Process current page

    const next = page.getByRole("button", { name: "Next" });

    if (await next.isDisabled()) {
        break;
    }

    await next.click();
}
```

### Most important techniques to remember

```text
locator("table tbody tr")
        ↓
count()
        ↓
nth()
        ↓
filter({ hasText })
        ↓
locator("td")
        ↓
getByRole()
        ↓
Next → pagination
```

For real-world Playwright tests, **stable locators + row scoping + web-first assertions** are much more reliable than hardcoded indexes and `waitForTimeout()`.
