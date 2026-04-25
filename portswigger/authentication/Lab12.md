# Lab: Password brute-force via password change

## Vulnerability Summary

The application is vulnerable to a logic flaw in the password change functionality. An authenticated attacker with access to a valid account (for example, `wiener`) can abuse the password change request to brute-force the password of another user such as `carlos`.

The vulnerability exists because the application does not properly validate whether the `username` parameter inside the password change request belongs to the currently authenticated user. Additionally, the server returns different responses depending on whether the supplied `current-password` is correct.

This behavior creates an oracle that can be abused to enumerate valid passwords for other users.

## Attack Steps

1. Log into a valid account such as `wiener`.

2. Navigate to the password change functionality.

3. Intercept the password change POST request using entity["software","Burp Suite","Web security testing platform"].

4. Modify the request so that the values of the new password fields are different:

```http
new-password-1=test123
new-password-2=test456
```

5. Observe the application's response:

```text
New passwords do not match
```

6. Replace:

```http
username=wiener
```

with:

```http
username=carlos
```

7. Send the request to Intruder.

8. Configure a sniper attack against the `current-password` parameter using a password wordlist.

9. Add a grep match rule for:

```text
New passwords do not match
```

10. Start the attack and monitor responses.

11. When the response behavior changes or the grep match disappears, the correct password for `carlos` has been identified.

<img width="1280" height="720" alt="5436329489295151660" src="https://github.com/user-attachments/assets/46853118-bd38-4624-a0aa-056257e71731" />

## Exploitation

During testing, it was discovered that the application processes the `current-password` field before validating whether the two new password fields match.

If the supplied current password is correct, the application proceeds further in the password change logic and returns:

```text
New passwords do not match
```

However, when the current password is incorrect, the application returns a different response.

Because the `username` parameter can be modified to target another account, this behavior allows an attacker to brute-force another user's password while authenticated as their own account.

## Impact

Successful exploitation allows an attacker to:

- Compromise arbitrary user accounts.
- Take over privileged or administrator accounts.
- Abuse a legitimate authenticated session to attack other users.
- Bypass expected account isolation protections.

## Requirements for Successful Exploitation

- Access to a valid authenticated account.
- Knowledge of the target user's username.
- A password wordlist for brute-force attempts.

## Where I Got Stuck

- Initially, it was not obvious that intentionally mismatching the new password fields would expose an alternative application response.
- It also required additional testing to understand that the `username` parameter was not properly tied to the authenticated session.

## Wrong Assumptions

- At first, it seemed that the password change functionality would only operate on the currently authenticated user.
- The response `New passwords do not match` initially appeared harmless and unrelated to authentication logic.

## Key Insight

The key insight was recognizing that the application leaks authentication state through different response messages.

By intentionally forcing a password mismatch condition and modifying the `username` parameter, the password change functionality can be transformed into an oracle for brute-forcing another user's password.
<img width="1280" height="720" alt="5436329489295151660" src="https://github.com/user-attachments/assets/d77f8cb9-93d3-4f48-a990-ae7782d3b474" />
