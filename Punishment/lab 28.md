
## Title
Reflected XSS in hidden **cat** parameter 

## Vulnerability Type
Reflected XSS

## Summary
The kzlabs.com/punishment page has an Reflected XSS vulnerability in the **cat** parameter. whatever you type into the **cat** parameter gets embedded raw into a page response with no sanitization, By injecting an HTML tag with an event handler arbitary JavaScript executes immediately when they visit crafted URL
## Vulnerable Endpoint
```
http://kzlabs.com/punishment/28.php?cat=
```

## Steps to Reproduce
1. Go to the following crafted URL
```
https://kzlabs.com/punishment/28.php?cat=%22%3E%3CIMG%20src=x%20onerror=alalertert(1)%3E
```
3.Wait for the page to load

4.An alert popup appears showing 1


## Payload Used
```
"><IMG src=x onerror=alalertert(1)>
```

## Proof of Concept

**Screenshot 1** — URL bar showing payload injected directly into the **cat** parameter with the response refelcting raw in the page source without any sanitization 


<img width="1237" height="891" alt="image" src="https://github.com/user-attachments/assets/b85fc81c-7bd5-427f-9dc8-10c5c91ed7ef" />



**Screenshot 2** — Alert box displaying 1 triggered after submitting the payload "><IMG src=x onerror=alalertert(1)> ,with the payload visible in the URL bar  confirming reflected XSS execution in the browser.

<img width="1460" height="900" alt="image" src="https://github.com/user-attachments/assets/926ed57d-7f03-484a-ac3c-6cfab17c9e8c" />


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
