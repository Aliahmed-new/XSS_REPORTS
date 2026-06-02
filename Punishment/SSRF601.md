
## Title
Server-Side Request Forgery (SSRF) via Webhook URL Parameter

## Vulnerability Type
Server-Side Request Forgery (SSRF)

## Summary
The application has a webhook feature where you give it a URL and the server goes and fetches it. There is no restriction on what URL you can put in there. So i pointed it at my Burp Collaborator first to confirm the server is actually making the request, and then used file:///etc/passwd to make the server read its own files and send them back to me.
```
http://kzlabs.in/601.php
```
## Vulnerable Endpoint

```
POST /601.php
Parameter: webhook_url
```
## Steps to Reproduce

Step 1 — Confirming the SSRF with Burp Collaborator:

1.Open the following URL
```
http://kzlabs.in/601.php
```
2 Enter http://google.com

3.Turn on Intercept in Burp and click submit — capture the request

4.Send the request to Repeater and click Send

3.Go to Burp Collaborator and click Copy to Clipboard to get your unique Collaborator URL

4.Paste that URL in the repeater request - by adding https://

5.Go back to Collaborator and click Poll Now

6.You will see both DNS and HTTP interactions coming in from the server's IP — this confirms the server is making outbound requests to whatever URL you give it

7.The server IP in Collaborator is different from your own IP which confirms it is the server making the request, not your browser


Step 2 — Exploiting with file:// Protocol:

1.In Repeater change the URL value to file:///etc/passwd

2.Click Send

3.The response comes back with the full contents of the server's /etc/passwd file showing all system user accounts

## Crafted Request
```
url=file:///etc/passwd
```

## Proof of Concept

**Screenshot 1** — Burp Collaborator showing DNS and HTTP interactions confirming the server made an outbound request to the attacker controlled URL

<img width="1733" height="849" alt="image" src="https://github.com/user-attachments/assets/fd31f1b3-36cc-4c8e-8470-f04190ccdcbb" />



**Screenshot 2** — Repeater response showing the full contents of /etc/passwd returned after sending file:///etc/passwd as the webhook URL, contents included system accounts like root, daemon, www-data, sshd and many others

<img width="786" height="777" alt="image" src="https://github.com/user-attachments/assets/bfa12f0b-53b9-471b-b1f4-01a9e09521c5" />





## Impact
- Full account takeover is possible if configuration files containing database credentials or API keys are read 
- Attacker can read sensitive internal files from the server such as /etc/passwd, /etc/hosts, configuration files and potentially private keys
  

## Remediation
1. Only allow https:// and http:// protocols and block everything else like file://, gopher://, ftp:// completely
2. Block requests to internal IP ranges like 127.0.0.1, 169.254.x.x, and 10.x.x.x so the server cant be used to probe internal services
3. Never return the raw response from a fetched URL back to the user — just return a success or failure message so even if SSRF exists the attacker cant read the output
4. Validate and whitelist URLs on the server side — only allow requests to go to specific trusted domains, block everything else
