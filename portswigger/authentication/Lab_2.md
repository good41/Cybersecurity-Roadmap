# Lab: 2FA simple bypass

This lab shows that two-factor authentication can sometimes be bypassed without using any tools.  
You only need to understand the application logic.

From the provided lab data, we had:
- two account with login and password.
- Access to its email to retrieve the OTP code for one account.

Honestly, I solved this lab intuitively.

First, I logged into the account that had access to its email and successfully completed the login process.

Then I logged out and tried to log in to another account that did not have access to its email.

After entering the credentials, instead of completing the 2FA step, I clicked the orange button at the top: **"Back to lab"**.

As a result, I successfully bypassed the second authentication step and completed the lab.

---

## Takeaway

This lab shows that sometimes the 2FA check is not properly enforced on the server side.  
If the session is already created after the password step, it may be possible to access protected functionality without completing the second factor.

Sometimes you don’t need tools — you need to understand how the flow works and try to break it.
