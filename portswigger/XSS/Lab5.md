## Lab: DOM XSS in innerHTML sink using source location.search

### Vulnerability Summary

This vulnerability occurs because user-controlled data from `location.search` is inserted into the page using the `innerHTML` sink without proper sanitization. As a result, an attacker can inject HTML elements and execute JavaScript in the victim's browser.

### Attack Steps

1. Analyze the page's JavaScript code using DevTools.

2. Identify that data from `location.search` is processed and inserted into the page through `innerHTML`.

3. Determine the exact location where the user-controlled input appears in the DOM.

<img width="1918" height="1028" alt="Lab1" src="https://github.com/user-attachments/assets/41482a40-3afb-4607-acf3-49ca08f2dd82" />

4. Inject a payload that creates an HTML element capable of executing JavaScript:

```html
<img src=1 onerror=alert(1)>
```

<img width="1918" height="1030" alt="Lab2" src="https://github.com/user-attachments/assets/72ac6e06-708a-4f51-bd0d-08c57c07de17" />

5. Observe that the payload executes when the page processes the input.

### Exploitation

During the analysis, it was observed that modern browsers do not execute `<script>` elements injected through `innerHTML`. Therefore, an alternative HTML element with an event handler was used to achieve JavaScript execution.

Since this is a DOM-based XSS vulnerability, successful exploitation in a real-world scenario would typically require persuading a victim to visit a specially crafted URL containing the payload.

### Impact

An attacker may be able to:

- Execute arbitrary JavaScript in the victim's browser.
- Access data available to client-side scripts.
- Perform actions on behalf of the victim within the current session.
- Steal sensitive information exposed to the page.

### Where I Got Stuck

Initially, I spent time understanding where the search input was reflected in the page and whether it was necessary to break out of an existing HTML context.

### Key Insight

By inspecting the DOM, I noticed that my input was already reflected directly in the HTML content. This meant that no escaping was required and I could inject a new HTML element immediately.
