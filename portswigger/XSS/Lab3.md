## Lab: DOM XSS in document.write sink using source location.search

### Vulnerability Summary

This is a **DOM-based Cross-Site Scripting (XSS)** vulnerability, where the execution of malicious JavaScript occurs entirely in the client-side browser without server involvement.

The vulnerability arises because user-controlled data from `location.search` is passed directly into a dangerous sink (`document.write`) without proper sanitization or encoding.

As a result, an attacker can inject arbitrary JavaScript that will be executed in the victim’s browser.

### Attack Steps

1. Enter any input into the search field.

2. Inspect the page source using browser developer tools.

3. Identify that user input is taken from `location.search` and written into the DOM via `document.write`.

4. Craft a payload that breaks out of the existing HTML context and injects JavaScript:

   ```
   "><script>alert(1)</script>
   ```

5. Append the payload to the URL and load the page.

6. Observe that the script executes in the browser.

<img width="1920" height="1080" alt="Screenshot_2026-04-03_07_44_53" src="https://github.com/user-attachments/assets/b496e0bc-ecb4-4168-9550-16a30e33ba6a" />

<img width="1920" height="1080" alt="Screenshot_2026-04-03_07_44_20" src="https://github.com/user-attachments/assets/f730c044-f727-443d-b9f6-52757a812f97" />


### Exploitation

The application dynamically writes user-controlled input into the DOM using `document.write`, creating a direct XSS sink.

When the payload:

```
"><script>alert(1)</script>
```

is injected via the URL parameter, it breaks out of the existing HTML structure and executes arbitrary JavaScript.

This confirms the presence of DOM-based XSS.

Since the attack is executed entirely on the client side, it bypasses server-side protections.

<img width="1920" height="1080" alt="Screenshot_2026-04-03_07_42_31" src="https://github.com/user-attachments/assets/081b05f9-4886-4405-bf4d-2a15fe3452a7" />


### Impact

- Execution of arbitrary JavaScript in the victim’s browser
- Session hijacking
- Theft of sensitive data
- Account takeover
- Ability to perform actions on behalf of the user

Although DOM XSS requires user interaction (e.g., clicking a malicious link), it remains highly dangerous due to its ability to bypass server-side defenses.
