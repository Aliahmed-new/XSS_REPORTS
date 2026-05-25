
## Title
Reflected XSS in **categoryid** parameter 

## Vulnerability Type
Reflected XSS

## Summary
The kzlabs.com/punishment page has an Reflected XSS vulnerability in the **categoryid** parameter. whatever you type into the **categoryid** parameter gets embedded raw into a page response with no sanitization, By injecting an HTML tag with an event handler arbitary JavaScript executes immediately when they visit crafted URL
## Vulnerable Endpoint
```
http://kzlabs.com/punishment/20.php?categoryid=
```

## Steps to Reproduce
1. Go to the following crafted URL
```
http://kzlabs.com/punishment/20.php?categoryid=%3CImg%20src=x%20onerror=%22eval(%27\x61\x6c\x65\x72\x74(1)%27)%22%3E
```
3.Wait for the page to load

4.An alert popup appears showing 1


## Payload Used
```
<Img src=x onerror="eval('\x61\x6c\x65\x72\x74(1)')">
```

## Proof of Concept

**Screenshot 1** — URL bar showing payload injected directly into the **categoryid** parameter with the response refelcting raw in the page source without any sanitization 

<img width="1437" height="835" alt="image" src="https://github.com/user-attachments/assets/114d5ffb-8baf-48c4-b8ff-3fd34d1c387f" />




**Screenshot 2** — Alert box displaying 1 triggered after submitting the payload <Img src=x onerror="eval('\x61\x6c\x65\x72\x74(1)')"> ,with the payload visible in the URL bar  confirming reflected XSS execution in the browser.

<img width="1637" height="938" alt="image" src="https://github.com/user-attachments/assets/0227ceee-a3c6-4f4c-8ac2-e248c2644dc5" />




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
