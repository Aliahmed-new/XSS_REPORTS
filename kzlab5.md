
## Title
Refelcted (XSS) in First name parameter

## Vulnerability Type
Reflected XSS

## Summary
The kzlabs.com/punishment page has an XSS vulnerability in its First name feild . whatever you type into the first name feild gets embedded raw into a page response with no sanitization, By injecting an HTML tag with an event handler arbitary JavaScript executes immediately on page load and the popup fires.


## Vulnerable Endpoint
```
http://kzlabs.com/5.php
```

## Steps to Reproduce
1. Go to the following URL
```
 http://kzlabs.com/5.php?fname=
```
2. In the **First name** enter the following XSS payload
```
1')"AutoFocus/ContentEditable/OnFocusIn=(confirm)(1)//
```
3.Wait for the page to load

4.An alert popup appears showing 1


## Payload Used
```
1')"AutoFocus/ContentEditable/OnFocusIn=(confirm)(1)//
```


## Proof of Concept

**Screenshot 1** — Page source showing the search term tix5uni reflected raw with payload.

<img width="1804" height="851" alt="image" src="https://github.com/user-attachments/assets/93b11407-5259-485e-bff9-d6666606b654" />





**Screenshot 2** — Alert box displaying 1 triggered after submitting the payload 1')"AutoFocus/ContentEditable/OnFocusIn=(confirm)(1)// ,with the payload visible in the URL bar  confirming reflected XSS execution in the JavaScript context.

<img width="1505" height="921" alt="image" src="https://github.com/user-attachments/assets/9766bc1b-9e62-4e45-8525-d1ba0cbb9f3c" />






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
