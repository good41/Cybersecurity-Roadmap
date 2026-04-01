# Lab: Broken brute-force protection, IP block
## Vulnerability Summary

The vulnerability in this lab lies in the incorrect implementation of brute-force protection.
The application blocks an IP address only after three consecutive failed login attempts. However, this protection can be bypassed if a valid account is used to successfully authenticate, which resets the failed login counter.
By periodically performing a successful login with another valid account, it is possible to continue brute-forcing the password for the target user without triggering the IP block.

## Attack Steps
- Intercept the login request using Burp Proxy.
- Send the request to Burp Repeater and analyze the server responses.
- It was observed that the application blocks the IP address after three consecutive failed login attempts, forcing the attacker to wait approximately one minute before attempting to log in again.
In order to bypass this protection, a valid account was used to reset the failed login counter.

The attack sequence was structured as follows:

carlos
carlos
wiener

Corresponding password sequence:

pass1
pass2
peter

Where:

carlos is the target account

wiener:peter is a valid account used to reset the attempt counter

The attack was executed using Burp Intruder with a Pitchfork attack type.

The resulting request sequence:

carlos : pass1
carlos : pass2
wiener : peter
carlos : pass3
carlos : pass4
wiener : peter

This prevents the server from registering three failed login attempts in a row.

## Exploitation
During the brute-force attack, a successful login attempt returned the following response:

HTTP 302 Found

Location: /my-account

This redirect indicates successful authentication and grants access to the target account.

![5282800613374759855](https://github.com/user-attachments/assets/5e7cb58f-912f-49cd-bb80-d20da70eba66)

During additional analysis, another unexpected behavior was observed. One of the login attempts using a different password also resulted in a 302 response and allowed access to the account.
However, this behavior could not be reproduced consistently and the password was later confirmed to be incorrect. This may indicate a potential race condition or inconsistent authentication state handling.

![5282800613374759854](https://github.com/user-attachments/assets/9489d83b-312f-4144-910d-34f0a5aed60e)

## Impact
This vulnerability allows attackers to bypass brute-force protection mechanisms and perform password guessing attacks against user accounts.
By periodically authenticating with a valid account, the attacker can reset the failed login counter and continue brute-forcing passwords without triggering the IP block. This could potentially lead to account compromise.
