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
   1. Log in to your target site and browse it, go through all functionalities, click all links and buttons.</br>
   2. Intercept the generated request with a proxy and write down their URL endpoints.</br>
   3. Record these endpoints one by one in a list like the one below so you can visit them later.</br> Assuming we are testing this subdomain `email.example.com`</br>![image](https://github.com/user-attachments/assets/221786b6-e727-4798-b6e5-c9ae69c83a75)</br>
2. Test them for CSRFs (Check if there's CSRF protection on these request or not)</br>
   1. Go through the endpoints you listed and intercept the traffic using burp to see if any CSRF protection is implemented; look for any CSRF tokens that are sent via the URL parameter, or sent as a request header,cookies or via POST body parameter</br>
3. Confirm the vulnerability</br>
   1. You can do this by crafting a malicious HTML form that mimics the request sent to the orignial site, as this one below </br>![image](https://github.com/user-attachments/assets/57942bd2-9348-4a2f-beb5-0713242d7e95)</br>
>[!Note]
>Some websites protect against CSRFs by checking the `referer` header in the request to check if the request is sent from a legitimate site or not, though it can be manipulated so developers should not depend on this protection method only. 
# Bypassing CSRF Protection
1. Exploit Clickjacking
   1. If the endpoit is implementing CSRF token, but the page itself is vulnerable to clickjacking you can acheive the same results as a CSRF attack on the endpoint; you can frame the page in a malicious site while having the state-changing request orginate from a legitimate site and then tricks the user into making the request. 
2. Changing the request method
   1. Sometimes sites will accept multiple request methods for the same endpoint, and maybe not all methods are protected against CSRF; So for example if the POST request of the `password_change` endpoint is protected against CSRF by using CSRF tokens like this:</br>![image](https://github.com/user-attachments/assets/03905b4e-96bc-43e4-b872-a6174c895d9a) </br>, we can test if we can change the request method to GET, does it execute the action without protection or not?</br> So instead of sending a POST request, we will send a GET request that looks like this:</br>![image](https://github.com/user-attachments/assets/e1094cd8-73e4-418b-b830-c3f18249d667)</br> And simply the malicious HTML page would look something like this:</br>![image](https://github.com/user-attachments/assets/4f59b5fb-e3bd-4567-8908-82d0f0a84788) </br> Once the image is loaded, it will send a GET request to the endpoint with the new_password value, and if this action is executed, then this endpoint is vulnerable to CSRF and you bypassed the protection by changing the request method.
3. Bypassing CSRF tokens stored on the server</br>
<mark>Just because the website uses CSRF tokens, does it mean it validates them properly.</mark></br>
 If the site is not validating CSRF tokens properly, we can try different ways to acheive CSRF attack by modifying the malicious HTML page a little;</br>
      1. Try deleting the token parameter or sending a blank token parameter; this might work because of the common applications' logic mistakes
          1. Applications sometimes check the validty of the token only if the token exists or is not blank; if not it will execute the action</br>![image](https://github.com/user-attachments/assets/40d19425-61c6-42d8-833f-7d9d849c1d1a)</br>
      2. Try submitting the request with another session's CSRF token; this might work because of the common applications' logic mistakes
          1. Some applications check only if the token is valid, without confirming that is belong to the current user</b>![image](https://github.com/user-attachments/assets/b3088da5-ac89-453d-9e78-f96a4516660f)</br>![image](https://github.com/user-attachments/assets/e1fba533-911e-411a-b62b-9930c793f3eb)</br>
4. Bypass double-submit CSRF tokens</br>
In this technique, the state-changing request includes the CSRF tokens as both a cookie and a POST request parameter</br>![image](https://github.com/user-attachments/assets/9860d5ae-9210-4397-82ae-1d951d3ee3d9) </br> And the double-submit system checks only if these tokens are the same, then the request is considered legitimate regardless of whether they are valid tokens or not.</br>
So entering bogus tokens in both values will still be considered as legitimate since the server has no records of the valid tokens as it doesn't check the validty of the tokens once it receives it</br>

**Attack will happen in two steps:** </br>
1. You’d use a session-fixation technique to make the victim’s browser store whatever value you choose as the CSRF token cookie</br>
2. Then, you’d execute the CSRF with the same CSRF token that you chose as the cookie
 >[!Note]
 >Generally, you shouldn’t have the power to change another user’s cook-ies. But if you can find a way to make the victim’s browser send along a fake cookie, you’ll be able to execute the CSRF</br>

5. Bypass CSRF referer header check
To bypass this type of protection: 
     1. you can try to remove the referer header by using `<meta>` tag to the page from where you are hosting the request</br>![image](https://github.com/user-attachments/assets/73b817ef-b04c-4514-9e80-0a035c75f150)</br>
        This works because the application might have logic error such as validating the referer only if it exists, if not it executes the request.
     2. You can bypass the logic check used to validate the referer URL
        1. If the application is looking for a string `example.com` in the referer header to consider the request as legitimate, you can bypass this protection by placing the target's domain name as a subdomain such as `example.com.attacker.com` or by placing the it as a path name such as `attacker.com/example.com` (you can create this by creating a file name with the name of the target's domain and host your HTML page here.
        



 


