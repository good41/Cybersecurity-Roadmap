## Lab: Stored XSS into HTML context with nothing encoded

### Vulnerability Summary

This is a **stored Cross-Site Scripting (XSS)** vulnerability, which is considered the most severe type of XSS because the malicious payload is permanently stored on the server (e.g., in a database or user-generated content such as comments).

The application stores user input and later renders it in the HTML response **without any encoding or sanitization**, allowing execution of arbitrary JavaScript in the browsers of all users who view the affected page.

### Attack Steps

1. Identify an input point where user data is stored and displayed (in this case, the comment functionality).

2. Submit a test payload to check for XSS:

   ```
   <script>alert(1)</script>
   ```

3. Post the comment.

4. Navigate to the page where the comment is displayed.

5. Observe that:

   - The payload is stored on the server
   - The script executes automatically when the page is loaded

<img width="1920" height="1080" alt="Screenshot_2026-04-02_07_43_44" src="https://github.com/user-attachments/assets/b093dfeb-684c-4032-a0e9-8e3c24a1fbae" />

<img width="1920" height="1080" alt="Screenshot_2026-04-02_07_42_52" src="https://github.com/user-attachments/assets/c02ea111-5c5b-46a8-a902-6c902dc1d75d" />

### Exploitation

The application stores user input without applying output encoding, resulting in persistent execution of injected JavaScript.

When the payload:

```
<script>alert(1)</script>
```

is submitted as a comment, it is saved on the server and executed every time the page is viewed.

This confirms the presence of stored XSS.

An attacker can exploit this by injecting a malicious payload that executes in the context of any user visiting the page.

### Impact

- Persistent execution of malicious JavaScript
- Session hijacking
- Theft of cookies (if not protected by HttpOnly)
- Account takeover
- Access to sensitive user data
- Ability to perform actions on behalf of victims

Stored XSS is particularly dangerous because it does not require user interaction beyond visiting the affected page, enabling large-scale exploitation.


