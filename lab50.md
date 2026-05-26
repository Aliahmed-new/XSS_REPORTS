
## Title
Stored (XSS) in the http://kzlabs.com/50.php

## Vulnerability Type
Stored XSS

## Summary
The kzlabs.com page has an XSS vulnerability in site title,Descrption tab . whatever you type into the it gets embedded raw into a page response with no sanitization, By injecting an HTML tag with an event handler arbitary JavaScript executes immediately on page load and the popup fires.


## Vulnerable Endpoint
```
http://kzlabs.com/50.php
```

## Steps to Reproduce

1. Go to the following URL
```
 http://kzlabs.com/50.php?
```
   
3. In the search bar -Enter the below payload
```
</div><script>alert(1)</sccript>
```

4.Wait for the page to load 

5.An alert popup appears showing 1


## Payload Used
```
</div><script>alert(1)</sccript>
```


## Proof of Concept

**Screenshot 1** Entering the detatils and payload refelcts as is.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/fc0fe1fe-d26c-4731-9e85-091d83524979" />




**Screenshot 2** — Alert box displaying 1 triggered after submitting the payload </div><script>alert(1)</sccript> confirming stored XSS execution in the JavaScript context.


<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/51a19801-3e8f-4655-b52f-0cc0c8cfef06" />




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
