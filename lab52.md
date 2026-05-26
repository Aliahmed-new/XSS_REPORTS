
## Title
Self Cross-Site Scripting (XSS) in name feild

## Vulnerability Type
Self XSS

## Summary
The kzlabs.com page has an self XSS vulnerability in site  name feild -whatever you type into the it gets embedded raw into a page response with no sanitization, By injecting an HTML tag with an event handler arbitary JavaScript executes immediately on page load and the popup fires.


## Vulnerable Endpoint
```
http://kzlabs.com/52.php
```

## Steps to Reproduce

1. Go to the following URL
```
 http://kzlabs.com/52.php?
```
   
3. In the search bar -Enter the below payload
```

```

4.Wait for the page to load 

5.An alert popup appears showing 1


## Payload Used
```
<script>alert(1)</script>
```


## Proof of Concept

**Screenshot 1** Entering the detatils and payload refelcts as is.

<img width="1685" height="678" alt="image" src="https://github.com/user-attachments/assets/62fb03dc-f820-4261-92dc-28c95e6db81e" />




**Screenshot 2** — Alert box displaying 1 triggered after submitting the payload </div><script>alert(1)</sccript> confirming stored XSS execution in the JavaScript context.

<img width="1558" height="683" alt="image" src="https://github.com/user-attachments/assets/b6ada8f4-de2d-4285-b947-f8a2030b94fd" />




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
