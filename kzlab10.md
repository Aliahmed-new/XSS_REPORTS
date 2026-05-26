
## Title
Refelcted (XSS) in last name parameter

## Vulnerability Type
Reflected XSS

## Summary
The kzlabs.com/punishment page has an XSS vulnerability in its last name feild . whatever you type into the last name feild gets embedded raw into a page response with no sanitization, By injecting an HTML tag with an event handler arbitary JavaScript executes immediately on page load and the popup fires.


## Vulnerable Endpoint
```
http://kzlabs.com/10.php
```

## Steps to Reproduce
1. Go to the following URL
```
 http://kzlabs.com/10.php?lname=
```
2. In the **last name** enter the following XSS payload
```
"><svg onmouseover=eval('ale'+'rt(1)')>test</svg>
```
3.Wait for the page to load and move the mouse 

4.An alert popup appears showing 1


## Payload Used
```
"><svg onmouseover=eval('ale'+'rt(1)')>test</svg>
```


## Proof of Concept

**Screenshot 1** — Page source showing the payload reflected raw .

<img width="1796" height="611" alt="image" src="https://github.com/user-attachments/assets/b4e6dea2-38d8-4847-b77c-756fd4156ed9" />






**Screenshot 2** — Alert box displaying 1 triggered after submitting the payload "><svg onmouseover=eval('ale'+'rt(1)')>test</svg> ,with the payload visible in the URL bar  confirming reflected XSS execution in the JavaScript context.

<img width="1535" height="941" alt="image" src="https://github.com/user-attachments/assets/99a88dfc-9b13-4b3d-a900-a6030572462f" />





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
