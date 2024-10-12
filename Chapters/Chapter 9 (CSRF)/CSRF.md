# Cross Site Request Forgery
Goal: Attack other users of a web application.</br>
CSRF is a web vulnerability that allows attackers to send requests that carry unwanted actions on a behalf of a victim (user).</br>
CSRF is a client side vulerability.</br>
Target of CSRF: state-changing requests (modifying user settings, sending tweets) rather than the ones that reveal user info;</br> <mark> WHY? </mark></br>
Because the attackers won't be able to read the response to the forged request sent during the attack.</br>
# Mechanisms

## Impact of CSRF Depends on Where It is Found
