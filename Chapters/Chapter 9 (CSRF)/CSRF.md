# Cross Site Request Forgery
Goal: Attack other users of a web application.</br>
CSRF is a web vulnerability that allows attackers to send requests that carry unwanted actions on a behalf of a victim (user).</br>
CSRF is a client side vulerability.</br>
Target of CSRF: state-changing requests (modifying user settings, sending tweets) rather than the ones that reveal user info;</br> <mark> WHY? </mark></br>
Because the attackers won't be able to read the response to the forged request sent during the attack.</br>
# Mechanisms
A simple example of exploiting this vulnerability is when the attacker creates a simple form page that sends tweets on the victim's behalf by specifiying the `action` part of the form to be twitter's sending tweet functionality (assuming there's `https://twitter.com/sending_tweet` endpoint that is responsible of sending tweets) and by taking advantage of session cookies that is responsible of authenticating users without requiring them to log in, the request is sent to twitter to send the tweet as if the user was the one who made it.</br>![image](https://github.com/user-attachments/assets/70af9596-52f3-4154-bd6d-f429463116dd)</b>
## Impact of CSRF Depends on Where It is Found
### Scenario 1
Imagine CSRF vulnerability exists on requests that emptie user's online shopping cart, it will at most cause annoyance to users and will not caues any financial harm or identity theft.</br>
### Scenario 2 
Imagine CSRF vulnerability exists on requests that change users' passwords, this can cause the attacker to change users' passwords on their behalf and take over their accounts.</br>
### Scenario 3 
Imagine CSRF vulnerability exists on requests that handle user finances, like account balance, this can cause the attacker to make unauthorized balance transfers to their bank accounts.</br>
# Prevention
The best way to prevent against CSRF attacks is by using **CSRF tokens**. </br>
**CSRF tokens** are random and unpredictable strings that applications embed in every form on their websites.</br>
Servers generate CSRF tokens for each session and or HTML form, and send them to the browser, so when the user makes a state-changing request, the browser embeds these tokens with the request for the server to be able to make sure that the request orginated from the original website.</br>
**How the server generates CSRF tokens and embeds it within the form**<mark> (using PHP) </mark></br>
![image](https://github.com/user-attachments/assets/252ec5ac-9afe-4325-a52e-da078a123baa)</br>
**How the form is displayed for users**</br>
![image](https://github.com/user-attachments/assets/fa8b438b-0010-4958-9fbc-ff7357c9179d)</br>
The server requires the browser to send the correct CRSF token post param to validate the request, and if the CSRF token is missed or incorrect, the server rejects the request.</br>
**How the request should look like**</br>
![image](https://github.com/user-attachments/assets/e3c2eb23-a648-48a8-a0f3-70c55a3f5e86) </br>

