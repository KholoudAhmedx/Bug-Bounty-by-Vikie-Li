# Open Redirects
Open Redirect is a web vulnerability that occurs when an attacker manipulates the value of HTTP or URL paramaters that websites often use to redirect users to another URL without user interaction. 
# Mechanisms 
1. **Using URL parameters** </br>
Websites often need to automatically redirecting users to save them time and improves their experience and a common scenario is when a user is trying to access a page that requires authentication.  </br> In the image below, the attacter tricks the user by providing them a URL from a legitimate site that redirects them to the malicious site,for example: `http://example.com/login?redirect=https://attacker.com`. After that, the attacker can launch a social engineering attack to steal users credientials. </br>
**Scenario 1:** 
![image](https://github.com/user-attachments/assets/e39c4e2d-52df-47ed-8217-374c742fa934) </br>

2. **Referer-based open redirects (HTTP Request)**\
Another common open-redirect technique is the referer-based open redirect.</br> In the image below, the user clicks on a link that redirects to the legitimate login page and the request is sent to `https://example.com/login`, that request contains a referer header which tells the location where your request comes from (in this case the attackers website `https://attacker.com`), then after logging in, it redirects the user back to the attacker's webpage.</br>
**Scenraio 2:**
![image](https://github.com/user-attachments/assets/b787ee1d-a9df-40a9-93a8-84a6773e947b) </br>
>[!Note]
>This happens when referer-based redirects are not validated and attackers exploit this behaviour by tricking users to start from their own page. </br>
# Prevention
Websites implement URL validators to ensure user-provided redirect points to a legitimate location.\
These validators uses either allowlist or blocklist.\
<mark>Allowlist</mark> -> checks whether the hostname portion of the URL matches allowed hostnames, then the redirect goes through.\
<mark>Blocklist</mark> -> checks whether the redirect URL contains malicious hostnames or special characters used in attacks, then it blocks the request.\
>[!NOTE]
>Validators can hardly identify hostname portions of the URL as decoding and parsing URL is difficult to get right, which makes open redirect one of the most common web vulnerabilities. 
