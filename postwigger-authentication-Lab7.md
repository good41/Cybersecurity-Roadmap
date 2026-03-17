# Lab: Username enumeration via account lock
## Vulnerability Summary

The vulnerability is caused by an incorrect implementation of the account lock mechanism.

After several failed login attempts, the application locks a valid account. However:

The response length changes when a valid username becomes locked.

The application still allows unrestricted password brute-forcing for that identified valid account.

This behavior enables:

Username enumeration via response length analysis.

Subsequent password brute-force without effective protection.

# Attack Steps
1️ Intercept the login request

Intercept the HTTP POST request to /login using Burp Proxy.

2️ Username Enumeration (Cluster Bomb)

Use a Cluster Bomb attack in Intruder:

Payload 1 → list of possible usernames

Payload 2 → a single fixed random password

The password must be incorrect and different from the provided password list.

Why use a single random password?

If the correct password is accidentally used, the account will not be locked, and the length difference will not appear — preventing identification of the valid username.

After several failed attempts, the valid account becomes locked.

![5316574303455221732](https://github.com/user-attachments/assets/56c44452-49aa-49ff-b332-48b201932d63)


# Impact

Username enumeration is possible.

Password brute-force becomes feasible.

Full unauthorized access to user accounts.

Alternative for Community Edition

In the free version of Burp Suite, Intruder is heavily rate-limited.

A faster alternative is:

ffuf

It allows:

Filtering by response size (-fs)

Filtering by status code (-mc 302)

Much faster brute-force execution

![5316574303455221733](https://github.com/user-attachments/assets/2314584d-c81a-4e46-931e-2e33a82c9d6f)
