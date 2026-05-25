
## Title
Refelcted (XSS) in Krazeplanet.com

## Vulnerability Type
Reflected XSS

## Summary
The kzlabs.com/punishment page has an XSS vulnerability in its First name feild . whatever you type into the first name feild gets embedded raw into a JavaScript object with no sanitiziation. This means by breaking out of the string context with a quote and injecting JavaScript, the payload runs immediately when the page loads. just a malicious URL and the popup fires.

## Vulnerable Endpoint
```
http://kzlabs.com/punishment/1.php
```

## Steps to Reproduce
1. Go to the following URL
```
 http://kzlabs.com/punishment/1.php
```
2. In the **Fname** enter the following XSS payload
```
tix5uni"><script>alert(1)</script>
```
3.Wait for the page to load
4.An alert popup appears showing 1


## Payload Used
```
tix5uni"><script>alert(1)</script>
```


## Proof of Concept

**Screenshot 1** — Page source showing the search term tix5uni reflected raw inside the JavaScript analytics block

<img width="1429" height="180" alt="111" src="https://github.com/user-attachments/assets/3b6735dc-38fd-415e-bca3-f4c6e3a84253" />



**Screenshot 2** — Alert box displaying 1 triggered after submitting the payload tix5uni"><script>alert(1)</script> ,with the payload visible in both the URL bar  confirming reflected XSS execution in the JavaScript context.


<img width="1290" height="681" alt="Capture" src="https://github.com/user-attachments/assets/abf37faa-5ff2-415e-bf3b-4782a705eead" />



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
