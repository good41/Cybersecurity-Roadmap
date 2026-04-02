# Lab: Password Reset Broken Logic

## 🎯 Objective

Exploit flawed password reset logic to gain access to another user's account.

---

## 🧩 Initial Information

The lab provides:
- A valid account with access to its email.
- The goal is to reset the password of another user.

---

## 🔍 Analysis

1. Clicked **"Forgot password"**.
2. Entered the username of the account for which we have email access.
3. Received a password reset link in the email client.
4. Intercepted the reset request using **Burp Suite**.

During analysis of the intercepted request, it was observed that:

- The HTTP request contained a `username` parameter.
- This parameter was still modifiable at the moment of password reset.

This indicates a logic flaw — the server does not properly bind the reset token to a specific user.

---

## 🛠 Exploitation Steps

1. Intercept the password reset request in Burp.
2. Modify the `username` parameter to the target account.
3. Forward the modified request.
4. Set a new password for the target user.
5. Log in successfully using the target username and the newly set password.

---

## ❗ Why This Works

The application fails to properly validate that the reset token belongs to the same user whose password is being changed.

The server trusts the `username` parameter from the request instead of strictly associating the reset token with a specific account.

This results in a **broken authentication logic vulnerability**.

---

## 📌 Key Takeaways

- Always verify that reset tokens are bound to a specific user.
- Never trust client-controlled parameters in sensitive flows.
- Authentication logic flaws can be more dangerous than technical vulnerabilities.
