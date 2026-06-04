
## Title
Server-Side Request Forgery (SSRF) via Run Health check URL

## Vulnerability Type
Server-Side Request Forgery (SSRF)

## Summary
The application has a Run Health Check feature where you give it a URL and the server goes and fetches it. There is no restriction on what URL you can put in there. So i  used file:///etc/passwd to make the server read its own files and send them back to me.
```
http://kzlabs.in/603.php
```
## Vulnerable Endpoint

```
POST /603.php
Parameter: Run Health Check_url
```
## Steps to Reproduce

1.Open the following URL
```
http://kzlabs.in/603.php
```
2.Go to Service Endpoint URL and enter 
```
file:///etc/passwd

```
3.Click Check now

4.The response comes back with the full contents of the server's /etc/passwd file showing all system user accounts


## Crafted Request
```
file:///etc/passwd
```

## Proof of Concept


**Screenshot 1** — The response showing the full contents of /etc/passwd returned after sending file:///etc/passwd as the webhook URL, contents included system accounts like root, daemon, www-data, sshd and many others

<img width="1832" height="992" alt="image" src="https://github.com/user-attachments/assets/03f630c3-0ff2-4ff6-ae1d-f5dc76c9fba8" />



## Impact
- Full account takeover is possible if configuration files containing database credentials or API keys are read 
- Attacker can read sensitive internal files from the server such as /etc/passwd, /etc/hosts, configuration files and potentially private keys
  

## Remediation
1. Only allow https:// and http:// protocols and block everything else like file://, gopher://, ftp:// completely
2. Block requests to internal IP ranges like 127.0.0.1, 169.254.x.x, and 10.x.x.x so the server cant be used to probe internal services
3. Never return the raw response from a fetched URL back to the user and use cookie attribute
4. Validate and whitelist URLs on the server side 
