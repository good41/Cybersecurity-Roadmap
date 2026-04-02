## Lab: Reflected XSS into HTML context with nothing encoded

### Vulnerability Summary

This is a **reflected Cross-Site Scripting (XSS)** vulnerability that allows an attacker to execute arbitrary JavaScript in the victim’s browser.

The application reflects user input directly into the HTML response **without any encoding or sanitization**, making it possible to inject and execute malicious scripts.

Since this is a reflected XSS vulnerability, the payload is **not stored on the server** and must be delivered to the victim (e.g., via a crafted link), often requiring social engineering.

### Attack Steps

1. Open the lab and locate the search functionality.

2. Enter a basic XSS payload into the search field to test for vulnerability:

   ```
   <script>alert(1)</script>
   ```

3. Submit the request.

4. Observe the response:

   - The payload is reflected directly into the HTML
   - The script executes in the browser

<img width="1920" height="1080" alt="XSS 1_1" src="https://github.com/user-attachments/assets/b5cd9f3b-319d-43c8-bc73-4eb090016c3e" />

<img width="1920" height="1080" alt="XSS 1_2" src="https://github.com/user-attachments/assets/1c552bcb-dfcd-4a81-a509-e791d5db00bb" />

### Exploitation

The application reflects user input without encoding, allowing injected JavaScript to be executed immediately.

When the payload:

```
<script>alert(1)</script>
```

is submitted, the browser executes it and displays an alert box, confirming the presence of XSS.

An attacker can craft a malicious URL containing the payload and trick a victim into opening it, leading to execution of arbitrary JavaScript in the victim’s session.

### Impact

- Execution of arbitrary JavaScript in the victim’s browser
- Theft of session cookies (if not protected by HttpOnly)
- Account takeover possibilities
- Access to sensitive user data
- Ability to perform actions on behalf of the user

Although reflected XSS requires user interaction, it remains a serious vulnerability due to its potential impact when combined with social engineering.




