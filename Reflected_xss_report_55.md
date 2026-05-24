
## Title
Reflected Cross-Site Scripting (XSS) via Search Parameter in JavaScript Analytics Context


## Vulnerability Type
Reflected XSS


## Summary
The Help center page has an XSS vulnerability in its search bar. whatever you type into the search bar gets embedded raw into a JavaScript object with no sanitiziation. This means by  breaking out of the string context with a quote and injecting JavaScript, the payload runs immediately when the page loads. just a malicious URL and the popup fires.


## Vulnerable Endpoint
```
https://kzlabs.com/55.php?search=
```
**Vulnerable Parameter:** `search` parameter reflected inside the JavaScript analytics block without string escaping


## Steps to Reproduce
1.Open this URL in your browser:
```
http://kzlabs.com/55.php?search=tix5uni"-alert(1)-"
```
2. Wait for the page to load
3. An alert popup appears showing 1



## Payload Used
```
tix5uni"-alert(1)-"
```


## Proof of Concept

**Screenshot 1** — Page source showing the search term `tix5uni` reflected raw inside the JavaScript analytics block at line 425 as the value of `internalSearchTerm`, with a comment in the source itself saying "The search term is reflected here WITHOUT JavaScript string escaping" confirming the vulnerability at the code level.

<img width="749" height="259" alt="lab_55" src="https://github.com/user-attachments/assets/599a2be1-f442-429f-9a5b-d6a84d408adb" />



**Screenshot 2** — Alert box displaying `1` triggered on the Search Results page after submitting the payload `tix5uni"-alert(1)-"`, with the payload visible in both the URL bar and the search input field confirming reflected XSS execution in the JavaScript context.

<img width="1286" height="445" alt="lab55" src="https://github.com/user-attachments/assets/038d8598-79eb-47b7-83b9-e509210dc480" />




## Impact
- Cookie stealing
- Account takeover by sending crafted link to victim
- JavaScript execution in victim browser
- Can be used to redirect victims to fake login pages that look identical to Equifax to harvest credentials
- The vulnerability exists inside an analytics script which shows the dev team never considered it as an injection point making it easy to miss during code review


## Remediation
1.  Filter out dangerous tags like `script`, `img`, `svg` from user input before reflecting anything back to the page
3. Filter out JavaScript methods like `alert()`, `confirm()`, `prompt()` so even if a quote slips through the method won't execute
4. If you're using PHP then use `htmlspecialchars()` function with `ENT_QUOTES` flag before rendering any user input inside HTML attributes as this encodes both single and double quotes
5. Use Cloudflare as they have so many WAF rules that almost all XSS payloads will be blocked automatically before even reaching the server
