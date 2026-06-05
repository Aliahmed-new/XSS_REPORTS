
## Title
Server-Side Request Forgery (SSRF) via Link MainFlow URL

## Vulnerability Type
Server-Side Request Forgery (SSRF)

## Summary
The application has a MainFlow feature where you give it a URL and the server goes and fetches it. There is no restriction on what URL you can put in there. So i  used file:///etc/passwd to make the server read its own files and send them back to me.
```
http://kzlabs.in/605.php
```
## Vulnerable Endpoint

```
POST /605.php
Parameter: MainFlow_url
```
## Steps to Reproduce

1.Open the following URL
```
http://kzlabs.in/605.php
```
2.Go to link URLand enter 
```
http://[::ffff:127.0.0.1]

```
3.Click Scan Link

4.The response comes back with the full contents of the server's.


## Crafted Request
```
http://[::ffff:127.0.0.1]
```

## Proof of Concept


**Screenshot 1** — The response showing the full contents returned after sending http://[::ffff:127.0.0.1] as the URL, contents included system accounts.

<img width="1840" height="954" alt="image" src="https://github.com/user-attachments/assets/b7a73d33-3f9a-4e5f-a44e-fabc21b46e89" />




## Impact
- Full account takeover is possible if configuration files containing database credentials or API keys are read 
- Attacker can read sensitive internal files from the server such as /etc/passwd, /etc/hosts, configuration files and potentially private keys
  

## Remediation
1. Only allow https:// and http:// protocols and block everything else like file://, gopher://, ftp:// completely
2. Block requests to internal IP ranges like 127.0.0.1, 169.254.x.x, and 10.x.x.x so the server cant be used to probe internal services
3. Never return the raw response from a fetched URL back to the user and use cookie attribute
4. Validate and whitelist URLs on the server side 
