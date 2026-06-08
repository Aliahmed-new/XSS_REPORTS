
## Title
Local File Inclusion (LFI) via command Parameter

## Vulnerability Type
LFI

## Summary
The application has a command feature vulnerability where user supplied input is executed directly. I confirmed this by giving cat/etc/passwd the server goes and retruns with the file contents. 

## Vulnerable Endpoint

```
http://kzlabs.in/1101.php?cmd=cat+%2Fetc%2Fpasswd
```

## Steps to Reproduce

1.Open the following URL
```
http://kzlabs.in/1101.php
```
2.Go to Command Configuration 

3.Enter cat/etc/passwd

9.The response comes back with the full contents of the server's /etc/passwd file showing all system user accounts


## Payload Used
```
cat/etc/passwd
```

## Proof of Concept

**Screenshot 1** — The response showing the full contents of cat/etc/passwd returned contents included system accounts like root, daemon, www-data, sshd

<img width="1831" height="985" alt="image" src="https://github.com/user-attachments/assets/4af7d5cf-25d4-481a-953a-9f363c74471b" />



## Impact
- Full account takeover is possible if configuration files containing database credentials or API keys are read 
- Attacker can read sensitive internal files from the server such as /etc/passwd, /etc/hosts, configuration files and potentially private keys
  

## Remediation
1. Only allow https:// and http:// protocols and block everything else like file://, gopher://, ftp:// completely
2. Block requests to internal IP ranges like 127.0.0.1, 169.254.x.x, and 10.x.x.x so the server cant be used to probe internal services
3. Never return the raw response from a fetched URL back to the user and use cookie attribute
4. Validate and whitelist URLs on the server side 
