# XPath Axes Locators in Playwright

**XPath Axes** are used when you want to locate an element based on its **relationship with another element**.

They are especially useful when the element you want does **not have a unique `id`, `name`, or `class`**.

### 1. Basic idea

Suppose HTML looks like this:

```html
<div>
    <label>First Name</label>
    <input type="text">
</div>
```

If the input doesn't have a useful ID, you can locate it using the relationship:

```xpath
//label[text()='First Name']/following-sibling::input
```

In Playwright:

```typescript
await page.locator("//label[text()='First Name']/following-sibling::input").fill("Gajanan");
```

Here:

```text
label
  ↓
following-sibling
  ↓
input
```

---

# Important XPath Axes

There are several XPath axes, but these are the most important for automation testing:

| Axis                | Meaning                                                    |
| ------------------- | ---------------------------------------------------------- |
| `parent`            | Move to the parent element                                 |
| `child`             | Move to a child element                                    |
| `ancestor`          | Find parent, grandparent, etc.                             |
| `descendant`        | Find elements inside another element                       |
| `following-sibling` | Find elements after the current element at the same level  |
| `preceding-sibling` | Find elements before the current element at the same level |
| `following`         | Find elements appearing later in the document              |
| `preceding`         | Find elements appearing earlier in the document            |

---

## 2. `parent`

Moves from an element to its immediate parent.

HTML:

```html
<div class="form-group">
    <input id="Email">
</div>
```

XPath:

```xpath
//input[@id='Email']/parent::div
```

Playwright:

```typescript
const parent = page.locator("//input[@id='Email']/parent::div");
```

Think:

```text
input
  ↑
parent
  ↑
div
```

---

# 3. `child`

Moves from a parent to its direct child.

HTML:

```html
<div>
    <input id="Email">
</div>
```

XPath:

```xpath
//div/child::input
```

You can also write:

```xpath
//div/input
```

Playwright:

```typescript
await page.locator("//div/child::input");
```

---

# 4. `ancestor`

Used when you want to move upward through multiple levels.

HTML:

```html
<div class="container">
    <form>
        <div>
            <input id="Email">
        </div>
    </form>
</div>
```

You can find the `form`:

```xpath
//input[@id='Email']/ancestor::form
```

Or the container:

```xpath
//input[@id='Email']/ancestor::div[@class='container']
```

Think:

```text
container
   ↑
  form
   ↑
  div
   ↑
 input
```

---

# 5. `descendant`

Used to find an element somewhere inside another element.

HTML:

```html
<div class="login">
    <div>
        <input id="Email">
    </div>
</div>
```

XPath:

```xpath
//div[@class='login']/descendant::input
```

This can find the input even though it is not a **direct child**.

---

# 6. `following-sibling`

This is **very important in automation**.

Suppose:

```html
<div>
    <label>First Name</label>
    <input id="FirstName">
</div>
```

The label and input are siblings.

You can write:

```xpath
//label[text()='First Name']/following-sibling::input
```

Playwright:

```typescript
await page
    .locator("//label[text()='First Name']/following-sibling::input")
    .fill("Gajanan");
```

Diagram:

```text
label "First Name"
       │
       ↓
following-sibling
       │
       ↓
input
```

---

# 7. `preceding-sibling`

Opposite of `following-sibling`.

HTML:

```html
<div>
    <input id="FirstName">
    <label>First Name</label>
</div>
```

Find the input from the label:

```xpath
//label[text()='First Name']/preceding-sibling::input
```

Diagram:

```text
input
  ↓
preceding-sibling
  ↓
label
```

---

# 8. `following`

`following` can find elements that occur **later in the document**, not necessarily at the same level.

Example:

```xpath
//label[text()='First Name']/following::input
```

This means:

> Find an `input` that occurs somewhere after this label.

Be careful with this axis because it can potentially match an element farther away than you intended.

---

# 9. `preceding`

Opposite of `following`.

```xpath
//input[@id='Email']/preceding::label
```

This means:

> Find a `label` that appears before this input.

---

# Real Example from Demo Web Shop

Suppose you inspect the registration page and see:

```html
<div class="inputs">
    <label for="FirstName">First name:</label>
    <input type="text" id="FirstName">
</div>
```

You already know the easiest XPath:

```xpath
//input[@id='FirstName']
```

But imagine the `input` did **not** have an ID.

You could use:

```xpath
//label[text()='First name:']/following-sibling::input
```

Then:

```typescript
await page
    .locator("//label[text()='First name:']/following-sibling::input")
    .fill("Gajanan");
```

---

# How to decide which axis to use?

When inspecting HTML, ask:

### "Where is my target element relative to the element I already know?"

For example:

```text
Target is inside another element
        ↓
    descendant

Target is direct child
        ↓
      child

Target is parent
        ↓
     parent

Target is above several levels
        ↓
    ancestor

Target is next to it
        ↓
 following-sibling

Target is before it
        ↓
 preceding-sibling
```

### Most useful axes for beginners

I recommend learning these first:

```text
1. parent
2. child
3. ancestor
4. descendant
5. following-sibling
6. preceding-sibling
```

Once you understand these, `following` and `preceding` become much easier.

**Important:** In modern Playwright, prefer user-facing locators such as `getByRole()`, `getByLabel()`, and `getByText()` when they are reliable. XPath axes are useful when you specifically need to navigate relationships in the DOM.
