#Lab: Username enumeration via different responses
Overview
This lab demonstrates a brute-force attack against a web login form.
The primary objective was not to analyze complex application logic, but to correctly configure and use offensive security tools for credential enumeration.
The lab was completed on the PortSwigger Web Security Academy platform by PortSwigger.
Objective
Perform a brute-force attack against a login endpoint using:
Burp Suite (Intruder)
ffuf (alternative approach due to Community edition limitations)
##Method 1 — Burp Suite (Intruder)
Tool used: Burp Suite
Steps
Intercept the login POST request using Burp Proxy.
Right-click the request → Send to Intruder.
In the Positions tab:
Clear automatic positions.
Select the username and password parameters.
Choose attack type: Cluster Bomb
Required to use two separate payload sets (logins and passwords).
In the Payloads tab:
Payload set 1 → usernames wordlist
Payload set 2 → passwords wordlist
Start the attack and analyze response differences.
Notes
Cluster Bomb is significantly slower in Burp Community Edition.
Response length and status codes must be carefully monitored.
Successful authentication often results in:
Different response size
Redirect (302)
Different status code
##Method 2 — Using ffuf
Tool used: ffuf
Due to performance limitations of Burp Community Edition, the brute-force attack was executed using ffuf.
Step 1 — Save Raw HTTP Request
Intercept the login POST request and save it as request.txt.
Modify the request body:
username=USER&password=PASS
USER and PASS act as placeholders.
ffuf replaces these values dynamically using the provided wordlists.
Step 2 — Execute ffuf Command
ffuf -request /path/to/request.txt \
-w /path/to/logins.txt:USER \
-w /path/to/passwords.txt:PASS \
-fc 200
Parameter Breakdown
-request → raw HTTP request file
-w → wordlists mapped to placeholders
-fc 200 → filters out HTTP 200 responses
In this lab, failed login attempts returned HTTP 200, so filtering them allowed easier identification of abnormal responses.
Important Considerations
Do not edit wordlists or request files in non-standard text editors.
Some editors modify encoding or line endings.
Always verify:
Status codes
Response size
Redirect behavior
Brute-force labs focus on:
Tool configuration
Payload positioning
Response filtering logic
Key Takeaways
Understanding raw HTTP structure is essential.
Correct payload placement is critical for automation tools.
Filtering responses efficiently reduces noise.

Alternative tooling (ffuf) can outperform default solutions depending on constraints.
