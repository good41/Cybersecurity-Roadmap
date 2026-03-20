  # Lab: 2FA broken logic
  ## Vulnerability Summary

The application contains a flaw in its two-factor authentication (2FA) mechanism.
The server does not properly bind the authentication request with the subsequent 2FA verification step.

This allows an attacker to:

Modify the username parameter between authentication and 2FA steps

Generate a valid 2FA code for a victim account

Brute-force the 2FA code without needing the victim’s password

This vulnerability effectively breaks the integrity of the authentication flow.

## Attack Steps

Intercept the HTTP request during the login process.

Modify the parameter:

verify=wiener → verify=carlos

Forward the request to trigger 2FA for the victim account (carlos).

On the 2FA verification step:

Intercept the request

Replace verify=wiener on verify=carlos

Remove the session cookie to avoid binding with the original session

## Exploitation

Since the 2FA code is already generated for carlos, the attacker can now brute-force it.

Perform a brute-force attack on the 2FA code:

Range: 0000–9999

Step: 1

Due to the large number of requests, ffuf was used instead of Burp Intruder community for higher performance.

Once the correct code is found, the attacker gains access to the victim’s account.

![5328067807738008511](https://github.com/user-attachments/assets/4d29599e-1c04-456a-af72-08204a870fa6)
## Impact

This vulnerability allows full account takeover with minimal requirements:

Only a valid username is needed

No password is required

Any authenticated user can exploit this against other users

This represents a critical authentication bypass and compromises all user accounts on the platform.
