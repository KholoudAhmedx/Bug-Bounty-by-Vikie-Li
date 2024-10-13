# Cross Site Request Forgery
Goal: Attack other users of a web application.</br>
CSRF is a web vulnerability that allows attackers to send requests that carry unwanted actions on a behalf of a victim (user).</br>
CSRF is a client side vulerability.</br>
Target of CSRF: state-changing requests (modifying user settings, sending tweets) rather than the ones that reveal user info;</br> <mark> WHY? </mark></br>
Because the attackers won't be able to read the response to the forged request sent during the attack.</br>
# Mechanisms
A simple example of exploiting this vulnerability is when the attacker creates a simple form page that sends tweets on the victim's behalf by specifiying the `action` part of the form to be twitter's sending tweet functionality (assuming there's `https://twitter.com/sending_tweet` endpoint that is responsible of sending tweets) and by taking advantage of session cookies that is responsible of authenticating users without requiring them to log in, the request is sent to twitter to send the tweet as if the user was the one who made it.</br>![image](https://github.com/user-attachments/assets/70af9596-52f3-4154-bd6d-f429463116dd)</b>
## Impact of CSRF Depends on Where It is Found
