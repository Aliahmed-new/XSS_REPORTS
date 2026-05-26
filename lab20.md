
## Title
Stored (XSS) in the User Name, and Bio tab

## Vulnerability Type
Stored XSS

## Summary
The kzlabs.com page has an XSS vulnerability in user Name, Full Name,Bio tab . whatever you type into the it gets embedded raw into a page response with no sanitization, By injecting an HTML tag with an event handler arbitary JavaScript executes immediately on page load and the popup fires.


## Vulnerable Endpoint
```
http://kzlabs.com/20.php
```

## Steps to Reproduce

1. Go to the following URL
```
 http://kzlabs.com/20.php?
```
2. create and Log in with your details
   
3. In the Username -Enter the below payload
```
<script>alert(1)</script>
```

4.Wait for the page to load 

5.An alert popup appears showing 1


## Payload Used
```
<script>alert(1)</script>
```


## Proof of Concept

**Screenshot 1** Entering the details and payload.

<img width="1701" height="981" alt="image" src="https://github.com/user-attachments/assets/00c275b2-9d61-4337-8ab7-912f69b11218" />


**Screenshot 2** — Alert box displaying 1 triggered after submitting the payload <script>alert(1)</script> confirming stored XSS execution in the JavaScript context.


<img width="1536" height="944" alt="image" src="https://github.com/user-attachments/assets/e92c7462-7ff9-407c-bdc1-8a05835db2a8" />


**Screenshot** 3 - Alert box displaying 2 triggered after submitting the payload <script>alert(1)</script> confirming stored XSS execution 


<img width="1619" height="926" alt="image" src="https://github.com/user-attachments/assets/cacccb62-4b30-4686-b869-ce9410181d2d" />


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
