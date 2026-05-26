
## Title
Stored (XSS) in the comment tab

## Vulnerability Type
Stored XSS

## Summary
The kzlabs.com/punishment page has an XSS vulnerability in comment tab . whatever you type into the it gets embedded raw into a page response with no sanitization, By injecting an HTML tag with an event handler arbitary JavaScript executes immediately on page load and the popup fires.


## Vulnerable Endpoint
```
http://kzlabs.com/18.php
```

## Steps to Reproduce

1. Go to the following URL
```
 http://kzlabs.com/18.php?
```
3. create and Log in with your details
   
4. In the comment tab -Enter the below payload
```
<script>alert(1)</script>
```

3.Wait for the page to load 

4.An alert popup appears showing 1


## Payload Used
```
<script>alert(1)</script>
```


## Proof of Concept



**Screenshot 1** — Alert box displaying 1 triggered after submitting the payload <script>alert(1)</script> confirming stored XSS execution in the JavaScript context.

<img width="1826" height="869" alt="image" src="https://github.com/user-attachments/assets/f582f102-11e5-4d38-a40c-752af6800069" />




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
