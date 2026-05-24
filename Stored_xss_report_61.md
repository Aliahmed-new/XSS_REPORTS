---
## Title
Stored Cross-Site Scripting (XSS) via Rich Text Editor HTML Tab in Article Body

---
## Vulnerability Type
Stored XSS

---
## Summary
The **write section** has a stored XSS. The body feild has an HTML tab where you can type raw HTML directly - and it saves to the database without any sanitization. when someone visits the article, that HTML renders on the page and the payload fires automatically.

---
## Vulnerable Endpoint
```
http://kzlabs.com/61.php/articles/new
```
---
## Steps to Reproduce
1. create Quill account
2. Log in to the account using following URL
 ```
http://kzlabs.com/61.php
```
4. Navigate to the **Write** tab.
5. Fill in the **Title** and **Tags** fields with any value.
6. In the **Body** field, click on the **HTML** tab and enter the following payload:
```
"><img src=x onerror=alert(1)>
```
5. Save and publish the article.
6. Navigate to the My Articles page 
7. Observe that a JavaScript alert box pops up immediately displaying `3` — confirming the payload was stored and executed.
8. Anyone who opens that article will trigger the same alert.

---
## Payload Used
```
"><img src=x onerror=alert(1)>
```

---
## Proof of Concept

**Screenshot 1** — The Write form showing the payload entered inside the HTML tab of the Body field, confirming the editor accepts raw HTML with no filtering at all.

<img width="1210" height="905" alt="lab-61" src="https://github.com/user-attachments/assets/3b5e90a1-b17b-4290-a154-c95e4e2f1082" />



**Screenshot 2** — Alert box displaying `3` triggered automatically after the article was published and the My Articles page loaded, with the app itself showing the toast message "Article published — XSS payload fires when your articles page loads" which confirms the stored execution.

<img width="1346" height="549" alt="lab_61" src="https://github.com/user-attachments/assets/528a5733-11c7-4521-99d6-a44ac548b784" />



---
## Impact
- steal cookies and paste in their browser 
- Take over accounts without needing passwords
- One published article is enough to silently hit every single visitor

---
## Remediation
1. Filter out dangerous HTML tags like `<script>`, `<img>`, `<svg>` from the Body field before saving anything to the database
2. Filter out JavaScript methods like `alert()`, `confirm()`, `prompt()` so even if a tag slips through the method won't execute
3. If you're using PHP then use `htmlspecialchars()` function before rendering any user input back to the page
4. Use Cloudflare as they have so many WAF rules that almost all XSS payloads will be blocked automatically before even reaching the server
