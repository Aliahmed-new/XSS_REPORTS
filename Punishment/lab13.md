
## Title
Reflected XSS in http://kzlabs.com/punishment/13.php

## Vulnerability Type
Reflected XSS

## Summary
The kzlabs.com/punishment page has an Reflected XSS vulnerability in the last name. whatever you type into the Last Name gets embedded raw into a page response with no sanitization, By injecting an HTML tag with an event handler arbitary JavaScript executes immediately when they visit crafted URL

## Vulnerable Endpoint
```
http://kzlabs.com/punishment/13.php?lname

```

## Steps to Reproduce
1. Go to the following crafted URL
```
http://kzlabs.com/punishment/13.php?fname=&lname=%22%3E%3CIMG+src%3Dx+onerror%3Dalalertert%281%29%3E

```
3.Wait for the page to load

4.An alert popup appears showing 1


## Payload Used
```
"><IMG src=x onerror=alalertert(1)>
```

## Proof of Concept

**Screenshot 1** — URL bar showing payload injected directly into the **Last Name** with the response refelcting raw in the page source without any sanitization 

<img width="1237" height="913" alt="image" src="https://github.com/user-attachments/assets/bb620669-b3ea-44cd-a0b2-b63a6d780d3c" />




**Screenshot 2** — Alert box displaying 1 triggered after submitting the payload "><IMG src=x onerror=alalertert(1)> ,with the payload visible in the URL bar  confirming reflected XSS execution in the browser.

<img width="1403" height="978" alt="image" src="https://github.com/user-attachments/assets/734df3e3-af5a-4864-b194-02880f8f32c8" />




## Impact
- Cookie stealing 
- Account takeover by sending crafted link to victim
- JavaScript execution in victim browser
- Can be used to redirect victims to fake login pages that look identical to Equifax to harvest credentials


## Remediation
1. Filter out dangerous HTML tags like `<script>`, `<img>`, `<svg>` from the Company Name field before saving anything to the database
2. Filter out JavaScript methods like `alert()`, `confirm()`, `prompt()` and block external script sources from being injected through input fields
3. If you're using PHP then use `htmlspecialchars()` function before rendering any user input back to the page
5. Use Cloudflare as they have so many WAF rules that almost all XSS payloads will be blocked automatically before even reaching the serv
