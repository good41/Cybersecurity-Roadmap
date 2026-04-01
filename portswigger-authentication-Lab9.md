# Lab: Brute-forcing a stay-logged-in cookie

## Vulnerability Summary

The vulnerability allows an attacker to brute-force the `stay-logged-in` cookie used for automatic user authentication.

The cookie is constructed using:

* Base64-encoded username
* An MD5 hash of the user's password

Since the cookie generation algorithm is predictable and lacks additional security mechanisms (such as a cryptographic signature or random token), an attacker can generate valid cookie values using a password wordlist.

This enables unauthorized access to other users' accounts without knowing their actual credentials.

## Attack Steps

1. Log in using a valid account (`wiener`) with the "stay logged in" option enabled.

2. Log out and capture the `GET /my-account` request that includes the `stay-logged-in` cookie.

<img width="1920" height="1080" alt="Screenshot_2026-03-30_22_20_02" src="https://github.com/user-attachments/assets/4b6c09b3-3d28-41fc-8f6a-28118a40b5c2" />

3. Decode the cookie value:

   * First part — Base64 (username)
   * Second part — MD5 hash of the password

4. Using a password wordlist, generate:

   * MD5(password)
   * Combine with the username
   * Encode the result in Base64

5. Perform a brute-force attack (using Intruder or Turbo Intruder), replacing the cookie value with generated candidates.

<img width="1920" height="1080" alt="Screenshot_2026-03-30_22_25_33" src="https://github.com/user-attachments/assets/8e27b6a6-4eed-4056-9165-f635c7e7d72e" />

## Exploitation

During the brute-force process, a response with status **200 OK** was identified, differing from other responses (such as redirects or errors).

This indicates that a valid combination was found:

* Correct username
* Correct password (via MD5 hash)

The valid cookie allowed access to another user's account without requiring password authentication.

## Impact

* Full compromise of user accounts
* Ability to perform large-scale credential attacks
* No protection against replay or tampering attacks
* Violation of secure authentication principles

This vulnerability is critical, as it allows bypassing the login mechanism entirely without knowledge of the user's actual password.









