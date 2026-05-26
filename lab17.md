
## Title
Refelcted (XSS) in category&newest parameter

## Vulnerability Type
Reflected XSS

## Summary
The kzlabs.com/punishment page has an XSS vulnerability in category=all&sort=newest parameter . whatever you type into the it gets embedded raw into a page response with no sanitization, By injecting an HTML tag with an event handler arbitary JavaScript executes immediately on page load and the popup fires.


## Vulnerable Endpoint
```
http://kzlabs.com/17.php
```

## Steps to Reproduce
1. Go to the following crafted URL
```
http://kzlabs.com/17.php?category=all&sort=newest%22%3E%3Cscript%3Ealert(1)%3C/script%3E
```

3.Wait for the page to load

4.An alert popup appears showing 1


## Payload Used
```
"><script>alert(1)</script>
```


## Proof of Concept



**Screenshot 1** — Alert box displaying 1 triggered after submitting the payload "><script>alert(1)</script> ,with the payload visible in the URL bar  confirming reflected XSS execution in the JavaScript context.

<img width="1447" height="962" alt="image" src="https://github.com/user-attachments/assets/5549d1da-4716-4713-942e-3cf7a9df065f" />





## Impact
- Cookie stealing 
- Account takeover by sending crafted link to victim
- JavaScript execution in victim browser
- Can be used to redirect victims to fake login pages that look identical to Equifax to harvest credentials



## Remediation
1. Filter out dangerous HTML tags like `<script>`, `<img>`, `<svg>` from the Company Name field before saving anything to the database
2. Filter out JavaScript methods like `alert()`, `confirm()`, `prompt()` and block external script sources from being injected through input fields
3. If you're using PHP then use `htmlspecialchars()` function before rendering any user input back to the page
5. Use Cloudflare as they have so many WAF rules that almost all XSS payloads will be blocked automatically before even reaching the server
