
## Title
Reflected XSS in hidden **search** parameter 

## Vulnerability Type
Reflected XSS

## Summary
The kzlabs.com/punishment page has an Reflected XSS vulnerability in the search parameter. whatever you type into the id parameter gets embedded raw into a page response with no sanitization, By injecting an HTML tag with an event handler arbitary JavaScript executes immediately when they visit crafted URL
## Vulnerable Endpoint
```
http://kzlabs.com/punishment/25.php?search=
```

## Steps to Reproduce
1. Go to the following crafted URL
```
http://kzlabs.com/punishment/25.php?search=%22%3E%3CIMG%20src=x%20onerror=alert(1)%3E
```
3.Wait for the page to load

4.An alert popup appears showing 1


## Payload Used
```
"><IMG src=x onerror=alert(1)>
```

## Proof of Concept

**Screenshot 1** — URL bar showing payload injected directly into the **search** parameter with the response refelcting raw in the page source without any sanitization 

<img width="1621" height="816" alt="image" src="https://github.com/user-attachments/assets/b0dd4abf-549d-4532-af24-2aee00c47ef3" />




**Screenshot 2** — Alert box displaying 1 triggered after submitting the payload "><IMG src=x onerror=alert(1)> ,with the payload visible in the URL bar  confirming reflected XSS execution in the browser.

<img width="1427" height="801" alt="image" src="https://github.com/user-attachments/assets/e1b642d5-72cd-4e2b-9d42-790a313b2ba2" />



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
