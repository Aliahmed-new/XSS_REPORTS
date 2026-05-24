---
## Title
Reflected Cross-Site Scripting (XSS) via Search Parameter in HTML Attribute Context

---
## Vulnerability Type
Reflected XSS

---
## Summary
The PUBG community contains a reflected XSS vulnerability in its search functionality. User supplied input is embedded directly into a data-query HTML attribute without any encoding - a source code itslef includes a comment that the p parameter inserted without htmlspecialchars.

---
## Vulnerable Endpoint
```
https://kzlabs.com/56.php?p=
```
**Vulnerable Parameter:** `p` parameter reflected inside the `data-query` HTML attribute without encoding

---
## Steps to Reproduce
1. Go to `https://kzlabs.com/56.php` 
2. First search for a unique term like `tix5uni'">` and view the page source.
3. In the source code around line 435 you can see the search term reflected raw inside the `data-query` attribute:
```
data-query='tix5uni'">'
```
4. The source code comment confirms "The $p parameter is echoed directly into the data-query attribute WITHOUT htmlspecialchars" confirming the injection point.
5. Since the attribute uses single quotes and no escaping is applied, craft the following payload to break out.
```
tix5uni'><script>alert(1)</script>
```
6. visit the following URL directly:
```
http://kzlabs.com/56.php?p='><script>alert(1)</script>
```
7. The page loads and hovering over the search results area triggers the JavaScript alert box displaying `1` confirming the payload broke out of the attribute and executed.

---
## Payload Used
```
tix5uni'><script>alert(1)</script>
```

---
## Proof of Concept

**Screenshot 1** — Page source showing the search term `tix5uni` reflected raw inside the `data-query` attribute at line 435 with the source comment clearly stating "The $p parameter is echoed directly into the data-query attribute WITHOUT htmlspecialchars" and "Payload to exploit: '><script>alert(1)</script> confirming the vulnerable injection point.

<img width="1108" height="346" alt="lab56" src="https://github.com/user-attachments/assets/dc009f83-43bc-417a-89bc-72f265ee88d0" />


**Screenshot 2** — Alert box displaying `1` triggered on the PUBG Community Hub search results page after submitting the payload, with the payload tix5uni'><script>alert(1)</script>  visible in the search filter field and the URL bar confirming reflected XSS execution in the HTML attribute context.

<img width="1315" height="346" alt="lab-56," src="https://github.com/user-attachments/assets/529aa4b4-b913-4fe0-ace4-cd9150b783bb" />


---
## Impact
- An attacker can craft a malicious URL and send it to any PUBG Community Hub user and the moment they interact with the page the script executes in their browser
- Steal session cookies of anyone who visits the crafted link giving the attacker full access to their account
- Can be used to redirect victims to fake login pages to harvest credentials
- The vulnerability exists inside an HTML attribute which is a less obvious injection point and easy to miss during code review

---
## Remediation
1. Escape the `p` parameter before reflecting it inside the `data-query` attribute by replacing `'` with `&#39;` and `"` with `&quot;` so it cannot break out of the attribute context
2. Filter out dangerous tags like `script`, `img`, `svg` from user input before reflecting anything back to the page
3. Filter out JavaScript methods like `alert()`, `confirm()`, `prompt()` so even if a quote slips through the method won't execute
4. If you're using PHP then use `htmlspecialchars()` function with `ENT_QUOTES` flag before rendering any user input inside HTML attributes as this encodes both single and double quotes
5. Use Cloudflare as they have so many WAF rules that almost all XSS payloads will be blocked automatically before even reaching the server
