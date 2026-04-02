# Lab: Username Enumeration via Subtly Different Responses

## Vulnerability Summary

The application returns slightly different responses during authentication depending on whether the username exists or not.
This behavior leads to a **Username Enumeration** vulnerability, which allows an attacker to identify valid usernames within the system.

Once a valid username is discovered, it becomes significantly easier to perform further attacks such as password brute-force attempts.

## Attack Steps

1. Intercept the login request using **Burp Suite**.
2. Send the request to **Burp Intruder**.
3. Configure the attack using a list of possible usernames.
4. Analyze the server responses and identify differences in response behavior.
5. Detect the valid username based on the distinct server response.
6. Perform a password brute-force attack for the discovered username.

## Exploitation

Initially, I attempted to use the **Cluster Bomb** attack type in Burp Intruder to brute-force both usernames and passwords simultaneously. However, this approach was inefficient and did not fully leverage the logic of the vulnerability.

After analyzing the application responses more carefully, I noticed that the server returned **slightly different responses depending on whether the username existed**.

By filtering the responses in Burp Suite, it became possible to identify the **valid username**. Once the correct username was discovered, I used the **Sniper attack type** to perform a password brute-force and successfully gain access to the account.

## Impact

The **Username Enumeration** vulnerability allows attackers to identify valid user accounts in the system.
This significantly reduces the complexity of further attacks such as:

Password brute-force

Credential stuffing

Targeted phishing attacks

As a result, the security of user accounts can be compromised.
