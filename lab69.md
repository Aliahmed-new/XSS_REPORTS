
## Title
DOM (XSS) in the path

## Vulnerability Type
DOM XSS

## Summary
The kzlabs.com page has a DOM XSS vulnerability due to JS document .location.search path in the page source . whatever you type into the it gets embedded raw into a page response with no sanitization, By injecting an HTML tag with an event handler arbitary JavaScript executes immediately on page load and the popup fires.


## Vulnerable Endpoint
```
http://kzlabs.com/69.php
```

## Steps to Reproduce

1. Enter the crafted URL
```
 http://kzlabs.com/69.php/pub/fujitsu/fm3v2/player/?javascript:print(1)
```

2.Wait for the page to load 

3.An alert popup appears showing 1


## Payload Used
```
javascript:print(1)
```


## Proof of Concept



**Screenshot 1** — Alert box displaying 1 triggered after submitting the payload javascript:print(1) confirming DOM XSS execution.

<img width="1920" height="959" alt="image" src="https://github.com/user-attachments/assets/4e89dbe9-3d03-4cdd-80f8-632d291b68da" />





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
