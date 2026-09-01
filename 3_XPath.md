# Types of XPath Locators

In Selenium, XPath is used to **locate elements on a web page**. There are two main types:

## 1. Absolute XPath

Starts from the **root (`html`)** and follows the complete path to the element.

**Syntax:**

```xpath
/html/body/div/div[2]/div/input
```

**Example:**

```xpath
/html/body/div[4]/div[1]/input
```

❌ **Disadvantage:** Very fragile. If the page structure changes, the XPath can stop working.

---

## 2. Relative XPath

Starts from anywhere in the HTML DOM using `//`.

**Syntax:**

```xpath
//tagname[@attribute='value']
```

**Example:**

```xpath
//input[@id='Email']
```

This is generally **preferred in Selenium automation**.

---

# Common Types of Relative XPath

### A. XPath using ID

```xpath
//input[@id='Email']
```

### B. XPath using Name

```xpath
//input[@name='Email']
```

### C. XPath using Class

```xpath
//input[@class='username']
```

### D. XPath using Text

```xpath
//a[text()='Register']
```

Useful when the element has visible text.

### E. `contains()`

Used when an attribute value is partially known.

```xpath
//input[contains(@id,'email')]
```

Example:

```html
<input id="user_email_123">
```

XPath:

```xpath
//input[contains(@id,'email')]
```

### F. `starts-with()`

Used when an attribute starts with known text.

```xpath
//input[starts-with(@id,'user')]
```

### G. Multiple Attributes

```xpath
//input[@type='text' and @name='Email']
```

### H. `or`

```xpath
//input[@id='Email' or @name='Email']
```

### I. XPath using Parent

```xpath
//label[text()='Email']/parent::div
```

### J. XPath using Following

```xpath
//label[text()='Email']/following::input[1]
```

### K. XPath using Ancestor

```xpath
//input[@id='Email']/ancestor::div[1]
```

### L. Index

```xpath
(//input[@type='text'])[2]
```

Selects the **second matching input**.

---

## Quick Summary

| Type / Technique    | Example                                       |
| ------------------- | --------------------------------------------- |
| Absolute XPath      | `/html/body/div/input`                        |
| Relative XPath      | `//input[@id='Email']`                        |
| ID                  | `//input[@id='Email']`                        |
| Name                | `//input[@name='Email']`                      |
| Class               | `//input[@class='username']`                  |
| Text                | `//a[text()='Register']`                      |
| Contains            | `//input[contains(@id,'email')]`              |
| Starts-with         | `//input[starts-with(@id,'user')]`            |
| Multiple attributes | `//input[@type='text' and @name='Email']`     |
| Parent              | `//label[text()='Email']/parent::div`         |
| Following           | `//label[text()='Email']/following::input[1]` |
| Ancestor            | `//input[@id='Email']/ancestor::div[1]`       |
| Index               | `(//input)[2]`                                |

### ⭐ For Selenium interviews

Remember these **most important XPath techniques**:

**Absolute XPath → Relative XPath → Attribute → `text()` → `contains()` → `starts-with()` → `and/or` → Parent/Child → Following/Preceding → Ancestor → Index**
