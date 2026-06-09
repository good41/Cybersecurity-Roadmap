## Lab: DOM XSS in document.write sink using source location.search inside a select element

### Vulnerability Summary

This vulnerability occurs because user-controlled data from `location.search` is passed directly into the `document.write()` sink without proper sanitization or validation. As a result, an attacker can inject malicious HTML or JavaScript through URL parameters and achieve DOM-based Cross-Site Scripting (XSS).

### Attack Steps

1. Open the lab page and inspect the JavaScript code using the browser's DevTools.

2. Identify that data from `location.search` is written to the page through `document.write()`. During the analysis, it becomes apparent that the application uses a parameter that is not directly visible in the page interface.

3. Supply a crafted value through the URL parameter:

```text
?ProductId=1&StoreId=<script>alert(1)</script>
```

4. Reload the page and observe that the injected JavaScript executes in the victim's browser context.

### Exploitation

The vulnerability can be identified and verified using only a web browser and the built-in DevTools. Since this is a DOM-based XSS vulnerability, the payload is processed entirely on the client side.

In a real-world scenario, successful exploitation would typically require convincing a victim to visit a specially crafted URL containing the malicious payload.

### Impact

An attacker may be able to:

- Execute arbitrary JavaScript in the victim's browser.
- Access data available to client-side scripts.
- Perform actions on behalf of the victim within the current session.
- Steal sensitive information such as session tokens or user data.

### Where I Got Stuck

Initially, I focused on the `ProductId` parameter because it appeared to be the most relevant input. However, further analysis showed that this parameter was not responsible for the vulnerable behavior.

### Wrong Assumptions

I assumed that `ProductId` was the parameter being processed by the vulnerable JavaScript code and spent time testing it before examining other parameters.

### Key Insight

The crucial observation was that the application actually used the `StoreId` parameter from the URL. Once I identified the correct parameter and traced its flow into `document.write()`, exploitation became straightforward.
