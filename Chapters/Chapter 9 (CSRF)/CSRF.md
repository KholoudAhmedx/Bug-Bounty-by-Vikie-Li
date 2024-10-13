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
1. The best way to prevent against CSRF attacks is by using **CSRF tokens**. </br>
  **CSRF tokens** are random and unpredictable strings that applications embed in every form on their websites.</br>
  Servers generate CSRF tokens for each session and or HTML form, and send them to the browser, so when the user makes a state-changing request, the browser embeds these tokens with the request for the server to be able to make sure that the request orginated from the original website.</br>
  **How the server generates CSRF tokens and embeds it within the form**<mark> (using PHP) </mark></br>
  ![image](https://github.com/user-attachments/assets/252ec5ac-9afe-4325-a52e-da078a123baa)</br>
  **How the form is displayed for users**</br>
  ![image](https://github.com/user-attachments/assets/fa8b438b-0010-4958-9fbc-ff7357c9179d)</br>
  The server requires the browser to send the correct CRSF token post param to validate the request, and if the CSRF token is missed or incorrect, the server rejects the request.</br>
  **How the request should look like**</br>
  ![image](https://github.com/user-attachments/assets/e3c2eb23-a648-48a8-a0f3-70c55a3f5e86) </br>
2. Use `SameSite` flag to protect user's cookie
  When the `SameSite` flag on a cookie is set to `Strict`, the client's browser won't send the cookie during cross-site requests.</br>
  ![image](https://github.com/user-attachments/assets/0dc71961-bfdd-4041-9332-26b70e89560d)</br>
3. Another possible way is to set `SameSite` flag to `lax`</br>
   `lax` tells the browser to send cookies in requests that cause top-level navigation (when users actively clicks on a button or a link that navigates them to the site) because the cross-site request is intentional.</br>
   Example is when user navigates to facebook from a third-party site such as twitter, your cookies will be sent in the request (GET request).</br>![image](https://github.com/user-attachments/assets/b86d7694-51e6-42de-a9a1-eadb827edcca)</br> But if the third-party initiates a POST request to Facebook or tries to embed the contents of Facebook within an iframe, cookies won’t be sent.

**Notes:**
1. Chrom and other browsers made `SameSite=lax` is the default cookie settings that prevented attackers from being able to attack a victim who uses Chrome with POST CSRF
2. On FireFox, the `SameSite` default setting is a feature that needs to be enabled</br>
### Even if browsers adopt the `SameSite` default settings, CSRF is still possible under some conditions.</br>
1. If the site allows state-changing requests with the GET HTTP method, third-party sites can attack users by creating CSRF with a GET request
 For example, if the site allows you to change a password with a GET request, you could post a link like this to trick users into clicking it: https://email.example.com/password_change?new_password=abc123. And since clicking a link is a top-level navigation, the cookies will be included in the GET request.
2. When the user uses a browser that doesn't set the `SameSite` cookie to `Lax` by default, if the web application doesn't implement protection against CSRF, then traditional CSRF attacks will work
3. When sites set the `SameSite` attribute to None, they allow cookies to be sent with cross-site requests, enabling third-party features like social logins or embedded services.

# Hunting for CSRFs
1. Spot state-changing actions</br>
  1.Log in to your target site and browse it, go through all functionalities, click all links and buttons.</br>
  2.Intercept the generated request with a proxy and write down their URL endpoints.</br>
  3.Record these endpoints one by one in a list like the one below so you can visit them later.</br> Assuming we are testing this subdomain `email.example.com`</br>![image](https://github.com/user-attachments/assets/221786b6-e727-4798-b6e5-c9ae69c83a75)</br>
