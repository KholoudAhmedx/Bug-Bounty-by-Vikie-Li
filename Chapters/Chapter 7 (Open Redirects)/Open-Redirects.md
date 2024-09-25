# Open Redirects
Open Redirect occurs when an attacker manipulates the value of HTTP or URL paramaters that websites often use to redirect users to another URL without user interaction. 
# Mechanisms 
Websites often need to automatically redirecting users to save them time and improves their experience and a common scenario is when a user is trying to access a page that requires authentication. </br>
**Scenario:** 
![image](https://github.com/user-attachments/assets/e39c4e2d-52df-47ed-8217-374c742fa934) </br>
An open redirect occurs in a scenario like this when the attacter tricks the user by providing them a URL from a legitimate site that redirects them to the malicious site, for example: `http://example.com/login?redirect=https://attacker.com`.
After that, the attacker can launch a social engineering attack to steal users credientials. 
