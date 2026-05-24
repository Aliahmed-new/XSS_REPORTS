
## Title
Reflected Cross-Site Scripting (XSS) via Username in URL Path Segment


## Vulnerability Type
Reflected XSS


## Summary
I identified a reflected XSS in the URL path segment of the following endpoint. The path segment between account/USERNAME/messages is reflected directly into a href tag without sanitization, allowing JavaScript execution through an event handler injection.

## Vulnerable Endpoint
```
https://kzlabs.com/58.php/account/{username}/messages
```
**Vulnerable Parameter:** Username segment in the URL path reflected raw inside the `href` attribute of the profile link


## Steps to Reproduce
1. Open the following URL in a browser
```
http://kzlabs.com/58.php/account/tix5uni"><img src=x onerror=alert(1)>/messages

```
2. wait for the page to load
3. A JavaScript alert box triggers immediately 
4. The page loads and the alert box fires immediately displaying `1` confirming the payload broke out of the href attribute and executed via the onerror handler.


## Payload Used
```
tix5uni"><img src=x onerror=alert(1)>
```


## Proof of Concept

**Screenshot 1** — Page source showing the username `tix5uni` reflected raw inside the profile link `href` attribute at line 382, with the source comment clearly stating "VULNERABLE: $username is echoed raw into this double-quoted href" and the suggested payload `testcatplzignore"><img src=x onerror=prompt(document.domain)>` confirming the exact injection point.

<img width="1213" height="459" alt="image" src="https://github.com/user-attachments/assets/d245d117-3837-429b-bf21-a82fe37fcab6" />




**Screenshot 2** — Alert box displaying `1` triggered on the Imgur-style messages page immediately after loading the crafted URL, with the full payload `tix5uni"><img src=x onerror=alert(1)>` visible in the URL bar and the raw payload rendering on the profile card confirming reflected XSS execution via the URL path segment.

<img width="1330" height="363" alt="lab-58" src="https://github.com/user-attachments/assets/da93df11-6f14-4c11-8f88-cefe8e6dedd1" />




## Impact
- An user visiting a crafted URL has a malicious code executed in their browser without clicking anything.
- Cookie Stealing
- Since the payload fires via an img onerror handler the victim does not need to click or hover over anything making it a fully automatic execution
- Can be used to silently redirect victims to fake login pages to harvest credentials


## Remediation
1. Escape the username before reflecting it inside the `href` attribute by replacing `"` with `&quot;` so it cannot break out of the attribute context
2. Filter out HTML tags like `<script>`, `<img>`, `<svg>` from the URL path segment before reflecting anything back to the page
3. Filter out JavaScript methods like `alert()`, `confirm()`, `prompt()` so even if a quote slips through the method won't execute
4. If you're using PHP then use `htmlspecialchars()` function with `ENT_QUOTES` flag before echoing any user supplied value into an HTML attribute as this encodes both single and double quotes
5. Use Cloudflare as they have so many WAF rules that almost all XSS payloads will be blocked automatically before even reaching the server
