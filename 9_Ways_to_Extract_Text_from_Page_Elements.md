In **Playwright**, there are several ways to extract text from page elements. The main methods are:

### 1. `textContent()`

Gets the raw text content, including hidden text in many cases.

```ts
const text = await page.locator("h1").textContent();
console.log(text);
```

Example HTML:

```html
<h1>Welcome to Playwright</h1>
```

Output:

```text
Welcome to Playwright
```

---

### 2. `innerText()`

Gets the **visible rendered text** of an element.

```ts
const text = await page.locator("h1").innerText();
console.log(text);
```

**Best when:** you want the text a user can actually see.

---

### 3. `allTextContents()`

Gets text from **multiple matching elements** as an array.

```ts
const texts = await page.locator(".product-name").allTextContents();

console.log(texts);
```

Example output:

```text
["Laptop", "Mobile", "Keyboard"]
```

---

### 4. `allInnerTexts()`

Gets the **visible text** from all matching elements.

```ts
const texts = await page.locator(".product-name").allInnerTexts();

console.log(texts);
```

Output:

```text
["Laptop", "Mobile", "Keyboard"]
```

---

### 5. `inputValue()`

For `<input>`, `<textarea>`, and similar form elements, use `inputValue()` instead of `textContent()`.

```ts
const value = await page.locator("#username").inputValue();

console.log(value);
```

HTML:

```html
<input id="username" value="Gajanan">
```

Output:

```text
Gajanan
```

---

### 6. `getAttribute()`

If the text/value you need is stored in an attribute:

```ts
const value = await page.locator("input").getAttribute("value");

console.log(value);
```

You can also get attributes such as:

```ts
const href = await page.locator("a").getAttribute("href");
const title = await page.locator("button").getAttribute("title");
```

---

## Quick comparison

| Method              | Use for                             |
| ------------------- | ----------------------------------- |
| `textContent()`     | Raw text content                    |
| `innerText()`       | Visible text                        |
| `allTextContents()` | Raw text from multiple elements     |
| `allInnerTexts()`   | Visible text from multiple elements |
| `inputValue()`      | Value of input/textarea             |
| `getAttribute()`    | Value stored in an HTML attribute   |

### ⭐ Playwright best practice

For **one visible element**:

```ts
const text = await page.locator("h1").innerText();
```

For **multiple elements**:

```ts
const texts = await page.locator(".item").allInnerTexts();
```

For **input fields**:

```ts
const value = await page.locator("#username").inputValue();
```

Also, if you're **verifying text**, prefer Playwright's web-first assertions rather than extracting the text yourself:

```ts
await expect(page.locator("h1")).toHaveText("Welcome");
```

This automatically waits for the expected text and is generally more reliable.
