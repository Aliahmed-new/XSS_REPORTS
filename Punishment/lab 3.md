
## Title
Refelcted (XSS) in kzlabs.com/punishment

## Vulnerability Type
Reflected XSS

## Summary
The kzlabs.com/punishment page has an XSS vulnerability in its first name feild . whatever you type into the first name feild gets embedded raw into a page response with no sanitization, By injecting an HTML tag with an event handler arbitary JavaScript executes immediately on page load and the popup fires.

## Vulnerable Endpoint
```
http://kzlabs.com/punishment/3.php
```

## Steps to Reproduce
1. Go to the following URL
```
 http://kzlabs.com/punishment/3.php
```
2. In the **First name** enter the following XSS payload
```
tix5uni"><IMG src=x onerror=alert(1)>
```
3.Wait for the page to load
4.An alert popup appears showing 1


## Payload Used
```
tix5uni"><IMG src=x onerror=alert(1)>
```


## Proof of Concept

**Screenshot 1** — Page source showing the search term tix5uni reflected raw .

<img width="1373" height="266" alt="image" src="https://github.com/user-attachments/assets/934f21a5-ef93-4ca4-b9fa-fefb65a5b73f" />




**Screenshot 2** — Alert box displaying 1 triggered after submitting the payload tix5uni"><img src=x onerror=alert(1)> ,with the payload visible in the URL bar  confirming reflected XSS execution in the JavaScript context.

<img width="1470" height="429" alt="image" src="https://github.com/user-attachments/assets/17d074e7-c1f7-44d8-b85b-effe1e0fe473" />





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
