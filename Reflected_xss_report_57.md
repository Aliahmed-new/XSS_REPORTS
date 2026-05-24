---
## Title
Reflected Cross-Site Scripting (XSS) via `returnTo` Parameter via JavaScript URI in Anchor Tag
 
---
## Vulnerability Type
Reflected XSS

---
## Summary
ThI identified a reflected XSS in the `returnTo` parameter of the following endpoint. The parameter value is reflected directly into an <a href> tag without sanitization, allowing JavaScript execution when the link is clicked. 

---
## Vulnerable Endpoint
```
https://kzlabs.com/57.php?returnTo=
```
**Vulnerable Parameter:** `returnTo` parameter echoed raw into the `href` attribute of the Continue button without any validation

---
## Steps to Reproduce
1. Open the following URL in a browser `https://kzlabs.com/57.php`
2. First test with a unique term like `tix5uni` and view the page source.
3. In the source code around line 461 you can see the returnTo value reflected raw inside the Continue button href:
```
<a href="tix5uni"><" class="btn-continue">
```
4. The source comment confirms "$returnTo is echoed into the href WITHOUT any validation" and also notes that `javascript:alert(document.cookie)` as returnTo will execute JS when clicked.
5. Now craft the payload using a `javascript:` URI and inject a script tag to execute JavaScript:
```
unique<img width="1881" height="900" alt="Screenshot 2026-05-24 031552" src="https://github.com/user-attachments/assets/822c14f1-f784-4b21-87f4-e6ed86327a7d" />
'"><script>alert(1)</script>
```
6. Visit the following URL directly:
```
https://kzlabs.com/57.php?returnTo=tix5uni'"><script>alert(1)</script>">
```
7. This confirms that arbitrary JavaScript supplied via the returnTo parameter is executed in the browser when the link is clicked

---
## Payload Used
```
tix5uni'"><script>alert(1)</script>
```

---
## Proof of Concept

**Screenshot 1** — Page source showing the returnTo value `tix5uni` reflected raw inside the Continue button href at line 461, with the source comment clearly stating "$returnTo is echoed into the href WITHOUT any validation" and noting that `javascript:alert(document.cookie)` will execute JS when clicked, confirming the vulnerable injection point.

<img width="1881" height="900" alt="Screenshot 2026-05-24 031552" src="https://github.com/user-attachments/assets/deb3bc8a-bcb3-4cac-991d-4c725cfb73b2" />


**Screenshot 2** — Alert box displaying `1` triggered on the account confirmation page immediately after loading the crafted URL, with the full payload visible in the URL bar confirming reflected XSS execution via the returnTo parameter.

<img width="1905" height="947" alt="Screenshot 2026-05-24 031503" src="https://github.com/user-attachments/assets/3e6d172b-cd53-4b66-aac7-ac7c34788799" />


---
## Impact
- Cookie stealing
- Account takeover 
- The source code also confirms this enables open redirect meaning an attacker can silently redirect victims to any external site like `?returnTo=https://evil.com` for phishing

---
## Remediation
1. Validate the `returnTo` parameter against a strict allowlist of trusted URLs or paths before reflecting it anywhere on the page, reject anything that is not a relative path or a trusted domain
2. Filter out danagerous tags like  `<script>`, `<img>`, `<svg>` before they reach the page.
3. If you're using PHP then use `htmlspecialchars()` with `ENT_QUOTES` flag before echoing any user supplied value into an HTML attribute to prevent breaking out of the attribute context
4. Filter out JavaScript methods like `alert()`, `confirm()`, `prompt()` and block script tags from being injected through URL parameters
5. Use Cloudflare as they have so many WAF rules that almost all XSS payloads will be blocked automatically before even reaching the server
