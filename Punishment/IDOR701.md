
## Title
Insecure Direct Object Reference (IDOR) via view invoice Parameter

## Vulnerability Type
IDOR

## Summary
The application has a invoice feature vulnerability. Invoices are loaded using a number in the URL, but the app has no idea if that invoice is actually yours. I swapped the number out for a different one and it worked  I was able to look at someone else's invoice. Any logged-in user could do the same and look on other people's order details without any issues.

## Vulnerable Endpoint

```
https://kzlabs.com/701.php?action=order&id=


```

## Steps to Reproduce

1.Create and login to the following URL
```
http://kzlabs.in/701.php
```

2.click on to the View invoice 

3. Observe the below URL

```
http://kzlabs.in/701.php?action=order&id=64

```

4. In the id parameter change the number to 62 or 63

5. you will see the invoices are displayed
   
6. This confimrs it contains different users order details



## Proof of Concept

**Screenshot 1** — Invoice belongs to unauntheticated user 

<img width="1745" height="995" alt="image" src="https://github.com/user-attachments/assets/164ea2c8-0579-4bb7-8c7a-3e2501c72fd1" />




## Impact
- Any authenticated user can access invoices that don't belong to them simply by guessing or changing a number in the URL.
-This exposes sensitive information such as other users' order details, personal data, and transaction history.
  

## Remediation
1. Always check that the invoice being requested actually belongs to the logged-in user before showing it to them.
2.Never trust the ID coming from the URL alone to decide whether someone should have access.
3.Make sure proper authorisation checks are in place on the server side for every single request, not just some of them.
