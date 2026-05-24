
## Title
Reflected Cross-Site Scripting (XSS) via `returnTo` Parameter via JavaScript URI in Anchor Tag
 

## Vulnerability Type
Reflected XSS


## Summary
I identified a reflected XSS in the `returnTo` parameter of the following endpoint. The parameter value is reflected directly into an href tag without sanitization, allowing JavaScript execution when the link is clicked. 


## Vulnerable Endpoint
```
https://kzlabs.com/57.php?returnTo=
```
**Vulnerable Parameter:** `returnTo` parameter echoed raw into the `href` attribute of the Continue button without any validation


## Steps to Reproduce
1. Open the following URL in a browser
```
http://kzlabs.com/57.php?returnTo=tix5uni'"><script>alert(1)</script>
```
2.Click the "Continue" button on the page

3.JavaScript alert box triggers immediately

4.This confirms that arbitrary JavaScript supplied via the returnTo parameter is executed in the browser when the link is clicked

## Payload Used
```
tix5uni'"><script>alert(1)</script>
```

## Proof of Concept

**Screenshot 1** — Page source showing the returnTo value `tix5uni` reflected raw inside the Continue button href at line 461, with the source comment clearly stating "$returnTo is echoed into the href WITHOUT any validation" and noting that `javascript:alert(document.cookie)` will execute JS when clicked, confirming the vulnerable injection point.

<img width="1250" height="513" alt="lab 57" src="https://github.com/user-attachments/assets/9eef7425-3cd4-4688-a66d-8e5bb2ca3ed9" />


**Screenshot 2** — Alert box displaying `1` triggered on the account confirmation page immediately after loading the crafted URL, with the full payload visible in the URL bar confirming reflected XSS execution via the returnTo parameter.

<img width="1085" height="301" alt="57" src="https://github.com/user-attachments/assets/9198a32c-6875-4a9b-adbe-1fe5d94d4208" />



## Impact
- Cookie stealing
- Account takeover 
- The source code also confirms this enables open redirect meaning an attacker can silently redirect victims to any external site like `?returnTo=https://evil.com` for phishing


## Remediation
1. Validate the `returnTo` parameter against a strict allowlist of trusted URLs or paths before reflecting it anywhere on the page, reject anything that is not a relative path or a trusted domain
2. Filter out danagerous tags like  `<script>`, `<img>`, `<svg>` before they reach the page.
3. If you're using PHP then use `htmlspecialchars()` with `ENT_QUOTES` flag before echoing any user supplied value into an HTML attribute to prevent breaking out of the attribute context
4. Filter out JavaScript methods like `alert()`, `confirm()`, `prompt()` and block script tags from being injected through URL parameters
5. Use Cloudflare as they have so many WAF rules that almost all XSS payloads will be blocked automatically before even reaching the server
