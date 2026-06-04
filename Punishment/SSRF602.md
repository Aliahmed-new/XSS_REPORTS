
## Title
Server-Side Request Forgery (SSRF) via Link Snap URL

## Vulnerability Type
Server-Side Request Forgery (SSRF)

## Summary
The application has a Linksnap feature where you give it a URL and the server goes and fetches it. There is no restriction on what URL you can put in there. So i  used file:///etc/passwd to make the server read its own files and send them back to me.
```
http://kzlabs.in/601.php
```
## Vulnerable Endpoint

```
POST /602.php
Parameter: Linksnap_url
```
## Steps to Reproduce

1.Open the following URL
```
http://kzlabs.in/602.php
```
2.Go to link URLand enter 
```
file:///etc/passwd

```
3.Click Generate Preview

4.The response comes back with the full contents of the server's /etc/passwd file showing all system user accounts


## Crafted Request
```
file:///etc/passwd
```

## Proof of Concept


**Screenshot 1** — The response showing the full contents of /etc/passwd returned after sending file:///etc/passwd as the webhook URL, contents included system accounts like root, daemon, www-data, sshd and many others

<img width="1784" height="959" alt="image" src="https://github.com/user-attachments/assets/2ef5d431-6d88-42fe-a233-112d1f571470" />



## Impact
- Full account takeover is possible if configuration files containing database credentials or API keys are read 
- Attacker can read sensitive internal files from the server such as /etc/passwd, /etc/hosts, configuration files and potentially private keys
  

## Remediation
1. Only allow https:// and http:// protocols and block everything else like file://, gopher://, ftp:// completely
2. Block requests to internal IP ranges like 127.0.0.1, 169.254.x.x, and 10.x.x.x so the server cant be used to probe internal services
3. Never return the raw response from a fetched URL back to the user and use cookie attribute
4. Validate and whitelist URLs on the server side 
