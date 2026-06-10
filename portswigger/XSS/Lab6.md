## Lab: DOM XSS in jQuery anchor href attribute sink using location.search source

### Vulnerability Summary

This vulnerability occurs because user-controlled data from `location.search` is passed to jQuery's `attr()` function and used to modify the `href` attribute of an anchor element. An attacker can supply a malicious `javascript:` URL, leading to DOM-based XSS when the victim interacts with the link.

### Attack Steps

1. Analyze the page's JavaScript code using DevTools.

2. Identify that a value from `location.search` is retrieved and assigned to an anchor's `href` attribute through jQuery's `attr()` function.

<img width="1918" height="1027" alt="Lab1" src="https://github.com/user-attachments/assets/428836f8-bfa5-429e-895c-602f494a6563" />

3. Supply a malicious URL parameter:

<img width="1918" height="1027" alt="Lab2" src="https://github.com/user-attachments/assets/4cf57c3a-b79e-4af8-8e7c-91536bd05d21" />

```text id="xlypww"
?returnPatch=javascript:alert(1)
```

4. Click the **Back** link to trigger JavaScript execution.

### Exploitation

The vulnerability can be identified by tracing the flow of user-controlled data from `location.search` to the `href` attribute sink. Since the payload is stored in a link, user interaction is required to trigger execution.

In a real-world scenario, an attacker would typically need to persuade a victim to visit a crafted URL and interact with the malicious link.

### Impact

An attacker may be able to:

- Execute arbitrary JavaScript in the victim's browser.
- Access data available to client-side scripts.
- Perform actions on behalf of the victim within the current session.
- Steal sensitive information exposed to the page.

### Where I Got Stuck

Initially, I focused on understanding why the payload did not execute immediately after the page loaded.

### Wrong Assumptions

I assumed that the payload would be executed automatically once it was injected into the page.

### Key Insight

The crucial observation was that the payload was placed inside the `href` attribute of a link. Therefore, JavaScript execution required user interaction by clicking the **Back** link.
