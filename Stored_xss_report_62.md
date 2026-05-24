---
## Title
Stored Cross-Site Scripting (XSS) in Profile Signature Field Executes for Every User Who Views the Profile

---
## Vulnerability Type
Stored XSS

---
## Summary
I identified a stored Cross-Site Scripting (XSS) vulnerability in **MY profile tab** in the **Signature field** . This field accepts and stores raw HTML without any sanitization and renders it directly as HTML via a live preview. The payload executes automatically for every user who visits the profile.

---
## Vulnerable Endpoint
```
https://kzlabs.com/62.php
```
---
## Steps to Reproduce
1. Go to the application at `https://kzlabs.com/62.php` 
2. Navigate to the **My Profile** tab.
3. Fill in the all the fields with any value.
4. In the **signature** field, enter the following payload:
```
"><img src=x onerror=alert(1)>
```
5. Save profile
6. Observe that a JavaScript alert box pops up immediately displaying `1` — confirming the payload was stored and executed.
8. Anyone who opens that article will trigger the same alert.

---
## Payload Used
```
"><img src=x onerror=alert(1)>
```

---
## Proof of Concept

**Screenshot 1** — The Write form showing the payload entered inside the signature field.

<img width="1210" height="905" alt="lab-61" src="https://github.com/user-attachments/assets/3b5e90a1-b17b-4290-a154-c95e4e2f1082" />



**Screenshot 2** — Alert box displaying `1` triggered automatically after the article was published and the My Articles page loaded, with the app itself showing the toast message "Article published — XSS payload fires when your articles page loads" which confirms the stored execution.

<img width="1346" height="549" alt="lab_61" src="https://github.com/user-attachments/assets/528a5733-11c7-4521-99d6-a44ac548b784" />



---
## Impact
- steal cookies and paste in their browser 
- Take over account without needing passwords

---
## Remediation
1. Filter out dangerous HTML tags like `<script>`, `<img>`, `<svg>` from the Body field before saving anything to the database
2. Filter out dangerous Event Handler `<script>`, `<img>`, `<svg>`
3. Filter out JavaScript methods like `alert()`, `confirm()`, `prompt()` so even if a tag slips through the method won't execute
4. If you're using PHP then use `htmlspecialchars()` function before rendering any user input back to the page
5. Use Cloudflare as they have so many WAF rules that almost all XSS payloads will be blocked automatically before even reaching the server

