
## Title
Refelcted (XSS) in academy parameter

## Vulnerability Type
Reflected XSS

## Summary
The kzlabs.com/punishment page has an XSS vulnerability in academy parameter . whatever you type into the it gets embedded raw into a page response with no sanitization, By injecting an HTML tag with an event handler arbitary JavaScript executes immediately on page load and the popup fires.


## Vulnerable Endpoint
```
http://kzlabs.com/13.php
```

## Steps to Reproduce
1. Go to the following crafted URL
```
 http://kzlabs.com/13.php?academy=%3Cbody/style%20onload=print()%3E
```

3.Wait for the page to load

4.An alert popup appears showing 1


## Payload Used
```
<body/style onload=print()>
```


## Proof of Concept



**Screenshot 1** — Print box displaying 1 triggered after submitting the payload <body/style onload=print()> ,with the payload visible in the URL bar  confirming reflected XSS execution in the JavaScript context.

<img width="1647" height="944" alt="image" src="https://github.com/user-attachments/assets/3fceb55a-ddab-4098-a948-9c7fb367dd61" />




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
