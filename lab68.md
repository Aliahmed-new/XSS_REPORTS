
## Title
DOM (XSS) in the #window.location.hash parameter

## Vulnerability Type
DOM XSS

## Summary
The kzlabs.com page has a DOM XSS vulnerability in window.location.hash parameter. whatever you type into the it gets embedded raw into a page response with no sanitization, By injecting an HTML tag with an event handler arbitary JavaScript executes immediately on page load and the popup fires.


## Vulnerable Endpoint
```
http://kzlabs.com/68.php
```

## Steps to Reproduce

1. Enter the crafted URL
```
 https://kzlabs.com/68.php#window.location.hash=%22%3E%3Cimg%20src=x%20onerror=alert(1)%3E
```

2.Wait for the page to load 

3.An alert popup appears showing 1


## Payload Used
```
"><img src=x onerror=alert(1)>
```


## Proof of Concept



**Screenshot 1** — Alert box displaying 1 triggered after submitting the payload "><img src=x onerror=alert(1)> confirming DOM XSS execution.

<img width="1886" height="950" alt="image" src="https://github.com/user-attachments/assets/846b5ef6-37bb-4d20-bd38-4e78154aa525" />




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
