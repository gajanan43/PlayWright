# Playwright Actions

In Playwright, **actions** are the operations you perform on web-page elements, such as clicking a button, entering text, selecting a dropdown option, checking a checkbox, or pressing a keyboard key.

Think of it as:

> **Locate the element → Perform an action → Verify the result**

For example:

```typescript
await page.locator("#Email").fill("gajanan@gmail.com");
await page.locator("#register-button").click();
```

Here, `fill()` and `click()` are **Playwright actions**.

---

## 1. `click()`

Used to click a button, link, checkbox, etc.

```typescript
await page.locator("#register-button").click();
```

Example:

```typescript
await page.getByRole("button", { name: "Login" }).click();
```

---

## 2. `fill()`

Used to enter text into an input field.

```typescript
await page.locator("#FirstName").fill("Gajanan");
```

For email:

```typescript
await page.locator("#Email").fill("gajanan@gmail.com");
```

### Important

`fill()` replaces the existing value.

If the field contains:

```text
Hello
```

and you execute:

```typescript
await locator.fill("Gajanan");
```

the result will be:

```text
Gajanan
```

---

## 3. `press()`

Used to press a keyboard key.

```typescript
await page.locator("#Email").press("Enter");
```

Other examples:

```typescript
await locator.press("Tab");

await locator.press("Escape");

await locator.press("ArrowDown");

await locator.press("Control+A");
```

---

## 4. `check()`

Used for a checkbox or radio button.

HTML:

```html
<input type="checkbox" id="terms">
```

Playwright:

```typescript
await page.locator("#terms").check();
```

For a radio button:

```typescript
await page.locator("#gender-male").check();
```

---

## 5. `uncheck()`

Used to remove the check from a checkbox.

```typescript
await page.locator("#terms").uncheck();
```

---

## 6. `selectOption()`

Used for `<select>` dropdowns.

HTML:

```html
<select id="country">
    <option value="india">India</option>
    <option value="usa">USA</option>
</select>
```

Playwright:

```typescript
await page.locator("#country").selectOption("india");
```

You can also select by visible label:

```typescript
await page.locator("#country").selectOption({ label: "India" });
```

---

## 7. `hover()`

Moves the mouse over an element.

```typescript
await page.locator("#products").hover();
```

Useful for menus that appear when you move the mouse over them.

Example:

```typescript
await page.getByText("Products").hover();
await page.getByText("Electronics").click();
```

---

## 8. `dblclick()`

Double-click an element.

```typescript
await page.locator("#file").dblclick();
```

---

## 9. `right-click`

You can perform a right-click using:

```typescript
await page.locator("#element").click({ button: "right" });
```

---

## 10. `focus()`

Moves focus to an element.

```typescript
await page.locator("#Email").focus();
```

---

## 11. `clear()`

Playwright's `fill()` already clears the existing value before entering new text.

So instead of:

```typescript
await locator.clear();
await locator.fill("Gajanan");
```

you normally just use:

```typescript
await locator.fill("Gajanan");
```

---

# 12. `type()` vs `fill()`

This is important when learning Playwright.

### `fill()`

```typescript
await page.locator("#Email").fill("gajanan@gmail.com");
```

It sets the complete value.

### `pressSequentially()`

If you specifically want Playwright to enter characters sequentially:

```typescript
await page.locator("#Email").pressSequentially("gajanan@gmail.com");
```

This can be useful when an application reacts to individual keystrokes.

---

# 13. Mouse actions

Playwright also provides mouse operations.

### Move mouse

```typescript
await page.mouse.move(500, 300);
```

### Click

```typescript
await page.mouse.click(500, 300);
```

### Double-click

```typescript
await page.mouse.dblclick(500, 300);
```

### Mouse down/up

```typescript
await page.mouse.down();
await page.mouse.up();
```

These are generally used for more advanced interactions.

---

# 14. Keyboard actions

You can use the keyboard directly:

```typescript
await page.keyboard.press("Enter");
```

Examples:

```typescript
await page.keyboard.press("Tab");

await page.keyboard.press("Escape");

await page.keyboard.press("Control+A");

await page.keyboard.press("Control+C");

await page.keyboard.press("Control+V");
```

---

# 15. Navigation actions

Playwright also has page-level actions.

### Open a URL

```typescript
await page.goto("https://demowebshop.tricentis.com/");
```

### Go back

```typescript
await page.goBack();
```

### Go forward

```typescript
await page.goForward();
```

### Reload

```typescript
await page.reload();
```

---

# 16. Example using your Demo Web Shop

Your registration test contains several Playwright actions:

```typescript
import { test, expect } from "@playwright/test";

test("Registration", async ({ page }) => {

    // Navigation action
    await page.goto("https://demowebshop.tricentis.com/");

    // Click action
    await page.locator("//a[@class='ico-register']").click();

    // Fill actions
    await page.locator("#FirstName").fill("Gajanan");
    await page.locator("#LastName").fill("Narwade");
    await page.locator("#Email").fill("gajanan@gmail.com");
    await page.locator("#Password").fill("demo@123");
    await page.locator("#ConfirmPassword").fill("demo@123");

    // Click action
    await page.locator("#register-button").click();

    // Assertion
    await expect(page.locator(".result"))
        .toHaveText("Your registration completed");
});
```

Notice the difference:

```text
Action                         Purpose
------------------------------------------------
goto()                         Navigate
click()                        Click
fill()                         Enter text
check()                        Check checkbox/radio
uncheck()                      Uncheck checkbox
selectOption()                 Select dropdown
hover()                        Move mouse over element
press()                        Press keyboard key
dblclick()                     Double-click
focus()                        Focus element
```

And:

```typescript
await expect(...)
```

is **not an action**.

It is an **assertion**, used to verify whether the expected result occurred.

### Easy way to remember

```text
LOCATOR
   ↓
ACTION
   ↓
APPLICATION RESPONSE
   ↓
ASSERTION
   ↓
PASS / FAIL
```

For example:

```typescript
await page.locator("#Email").fill("test@gmail.com");
                 ↑
              Locator

.fill()
   ↑
 Action

await expect(page.locator(".result")).toBeVisible();
             ↑
          Assertion
```

This **Locator → Action → Assertion** pattern is one of the most important fundamentals in Playwright.
