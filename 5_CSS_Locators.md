# CSS Locators in Playwright

**CSS Locator** is one of the most commonly used ways to find elements in Playwright.

Instead of using XPath like:

```typescript
page.locator("//input[@id='Email']")
```

you can use CSS:

```typescript
page.locator("#Email")
```

---

## 1. What is a CSS Locator?

CSS selectors are normally used to identify HTML elements.

Example HTML:

```html
<input id="Email" class="email-input" type="text">
```

You can locate it using:

```typescript
await page.locator("#Email").fill("demo@gmail.com");
```

Here:

```text
#Email
  ↑
  ID
```

---

# 2. ID Selector `#`

If an element has an `id`, use:

```css
#Email
```

HTML:

```html
<input id="Email">
```

Playwright:

```typescript
await page.locator("#Email").fill("demo@gmail.com");
```

### XPath equivalent

```xpath
//input[@id='Email']
```

### CSS

```css
#Email
```

**Usually CSS is shorter and easier.**

---

# 3. Class Selector `.`

HTML:

```html
<input class="username">
```

CSS:

```css
.username
```

Playwright:

```typescript
await page.locator(".username").fill("Gajanan");
```

### XPath equivalent

```xpath
//input[@class='username']
```

### CSS

```css
.username
```

---

# 4. Tag Selector

You can select an element using its HTML tag.

HTML:

```html
input
```

CSS:

```css
input
```

Playwright:

```typescript
const inputs = page.locator("input");
```

But be careful: there may be many `<input>` elements.

---

# 5. Attribute Selector

You can locate an element using any HTML attribute.

HTML:

```html
<input type="text" name="Email">
```

CSS:

```css
input[name='Email']
```

Playwright:

```typescript
await page.locator("input[name='Email']").fill("demo@gmail.com");
```

Another example:

```html
<input type="password" id="Password">
```

```css
input[type='password']
```

---

# 6. Multiple Attributes

You can combine multiple attributes.

HTML:

```html
<input id="Email" class="email-input" type="text">
```

CSS:

```css
input#Email.email-input
```

Or:

```css
input[id='Email'][class='email-input']
```

Playwright:

```typescript
await page.locator("input[id='Email'][type='text']");
```

---

# 7. Descendant Selector

Suppose HTML is:

```html
<div class="login">
    <input id="Email">
</div>
```

You can locate the input using:

```css
.login #Email
```

Meaning:

> Find `#Email` inside `.login`.

Playwright:

```typescript
await page.locator(".login #Email");
```

---

# 8. Child Selector `>`

Suppose:

```html
<div class="login">
    <input id="Email">
</div>
```

Use:

```css
.login > #Email
```

`>` means **direct child**.

Diagram:

```text
div.login
    ↓
   input
```

---

# 9. CSS `:nth-child()`

Suppose:

```html
<ul>
    <li>Java</li>
    <li>Python</li>
    <li>JavaScript</li>
</ul>
```

To select the second `<li>`:

```css
li:nth-child(2)
```

Playwright:

```typescript
await page.locator("li:nth-child(2)").click();
```

⚠️ Avoid relying heavily on positions because the page structure can change.

---

# 10. CSS `:first-child`

```css
li:first-child
```

Selects the first child.

---

# 11. CSS `:last-child`

```css
li:last-child
```

Selects the last child.

---

# 12. Combining Tag + Class

HTML:

```html
<button class="register-button">Register</button>
```

CSS:

```css
button.register-button
```

Playwright:

```typescript
await page.locator("button.register-button").click();
```

---

# 13. CSS Selector for Your Demo Web Shop

Your registration page has:

```html
<input id="FirstName">
```

Instead of XPath:

```typescript
await page.locator("//input[@id='FirstName']").fill("Gajanan");
```

CSS:

```typescript
await page.locator("#FirstName").fill("Gajanan");
```

For Last Name:

```typescript
await page.locator("#LastName").fill("Narwade");
```

Email:

```typescript
await page.locator("#Email").fill("demo@gmail.com");
```

Password:

```typescript
await page.locator("#Password").fill("demo@123");
```

Confirm Password:

```typescript
await page.locator("#ConfirmPassword").fill("demo@123");
```

Register button:

```typescript
await page.locator("#register-button").click();
```

This is much cleaner.

---

# 14. CSS vs XPath

| Requirement              | XPath                                   | CSS                        |
| ------------------------ | --------------------------------------- | -------------------------- |
| ID                       | `//input[@id='Email']`                  | `#Email`                   |
| Class                    | `//input[@class='email']`               | `.email`                   |
| Tag                      | `//input`                               | `input`                    |
| Attribute                | `//input[@name='Email']`                | `input[name='Email']`      |
| Multiple attributes      | `//input[@id='Email' and @type='text']` | `input#Email[type='text']` |
| Parent navigation        | Easy                                    | Limited                    |
| Ancestor navigation      | Easy                                    | Not directly               |
| Sibling relationships    | Very powerful                           | Limited                    |
| Simple element selection | Good                                    | **Excellent**              |

---

# 15. CSS vs XPath — When to use which?

### Use CSS when you have:

```html
id
class
name
type
data-* attributes
```

For example:

```typescript
page.locator("#Email")
page.locator(".username")
page.locator("input[name='Email']")
```

### Use XPath when you need relationships

For example:

```text
Find the input next to "First Name"
```

You could use:

```xpath
//label[text()='First Name']/following-sibling::input
```

That's where XPath axes become useful.

---

# 16. Best Practice in Playwright

Don't automatically choose XPath or CSS for everything.

Playwright generally recommends **user-facing locators** when they make the test more meaningful:

```typescript
page.getByRole("button", { name: "Register" })

page.getByLabel("First name:")

page.getByLabel("Email:")

page.getByText("Your registration completed")
```

Then use CSS or XPath when appropriate.

A useful priority for learning is:

```text
1. getByRole()
       ↓
2. getByLabel()
       ↓
3. getByText()
       ↓
4. CSS Locator
       ↓
5. XPath
```

However, **CSS and XPath are both valid Playwright locators**, and you should know both because real automation projects often contain existing tests using them.
