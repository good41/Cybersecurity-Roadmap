# Lab: Username Enumeration via Response Timing

## Vulnerability Summary

The vulnerability in this lab is based on **timing differences during authentication**.
The application takes slightly longer to process requests when a **valid username** is supplied compared to an **invalid username**.

When a long random password is used, the timing difference becomes more noticeable because the server performs additional checks for existing users.

Additionally, the application implements IP-based brute-force protection. However, this protection can be bypassed because the server trusts client-supplied HTTP headers such as **X-Forwarded-For**, allowing the attacker to spoof different IP addresses.

## Attack Steps

1. Intercept the login request using **Burp Suite**.
2. Send the intercepted **POST request** to **Burp Repeater**.
3. Test several login attempts and observe how the server responds.
4. Use a **long random password (15–20+ characters)** to amplify the response time difference.
5. Send the request to **Burp Intruder** and configure a **Cluster Bomb attack**:

   * Payload 1 → list of possible usernames
   * Payload 2 → different IP addresses in the **X-Forwarded-For** header
6. Sort the responses by **response time** and identify the valid username.
7. After discovering the valid username, perform another **Cluster Bomb attack** to brute-force the password.

## Exploitation

During testing, it became clear that the server processes authentication requests differently depending on whether the username exists.

By using a long random password, the timing difference between valid and invalid usernames becomes more apparent. This makes it possible to detect a valid username by analyzing the **response time**.

The application also attempts to prevent brute-force attacks using IP-based blocking. However, this protection can be bypassed by modifying the **X-Forwarded-For** header, effectively spoofing different client IP addresses.

## Impact

This vulnerability allows attackers to perform **username enumeration** using response timing differences. Once valid usernames are identified, attackers can significantly reduce the complexity of brute-force attacks.

Combined with the ability to bypass IP-based protections, this issue can lead to **account compromise**.
