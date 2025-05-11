# Application Logic Errors and Broken Access Control
While vulnerabilities such as XSS, CSRF, etc,. are caused by faulty input validation, application logic erros and broken access control vulnerabilities are caused/triggered by perfectly valid HTTP requests that contain no ilegal or malformed inputs but crafted intentially to misuse the app's logic for malicious purposes.</br>

|Application Logic Errors|Broken Access Control|
|------|-------|
|Logic flaws in the application | Occurs when sensitive resources or functionalities are not properly protected|

# Application Logic Errors
Application Logic Erros (aka Business logic errors) are ways of using legitimate logic flow of an application to deliver unintended consequences to the organization.</br>
**Common Logic Error Vulnerability**:
- Logic flaw in MFA functionality;

**MFA** => is the practice of requiring users to prove their identities in differnt, multiple ways.</br>
MFA helps in protecting against password compromise attacks, where it requires users to prove their identities by providing their passwords, along with authentication code (for example, there are different methods) sent via their email or text message. </br>

## Example 1: Error Logic in MFA Flow
Assume there is an application than implements 3-step login proccess to login users. So for example, the application does the following:
- Checks user's password
- Sends MFA code to the user's email
- Ask a set of security questions </br>

The normal flow of the authentication would be as the following:
1. The user visits `https://example.com/login` and is prompted to enter their password.
2. If the user enters a correct password, the application would redirect user to `https://example.com/mfa` endpoint where the user is sent a code via his email and prompted to enter it as well.
3. If the user enters a valid MFA code, then the application would redirect the user to `https://example.com/security_questions` endpoint where the user is requested to answer set of questions. </br>

If the application is vulnerable and has a logic error, it can cause the attacker to forego one of the steps and move to the next without verifying, therefore bypass one of the authentication steps => <mark><strong>Skippable authentication step<strong> </mark></br>.
So for example, if the application redirects users to step 3 before verifying that step 2 is completed, the attacker could bypass the MFA code entirely and could directly access `https://example.com/security_questions` without needing to verify/enter a valid code by manipulating the redirect URL.</br>

## Example 2: Error Logic in Checkout Flow
Assume there is a web application that implements the checkout process of an item in multiple steps.</br> When the user makes an order, the application provides 2 methods of payment, the first one is the user can enter a saved payment method where the application does not verify it and the other is the user can provide a new payment method and in this case the application verifies the payment method during the checkout process. </br>
**An example of the request of a saved payment method would be like:** </br>
![image](https://github.com/user-attachments/assets/2dbdee52-afea-448c-a4e5-805464f99c8b) </br>
**And the other request that uses a new payment method would be like:** </br>
![image](https://github.com/user-attachments/assets/c7e3689e-6e39-4740-a24a-037b5b05475d) </br>
Since the application verifies the payment method only when it is new, and it does know that is a new method by noticing the `saved_card` parameter, the attacker can provide an invalid card number and use the `saved_card` paremeter to give the impression that the method is saved, and if the application is vulnerable, then it will not verify it and allow the attacker to buy unlimited items with an invalid credit number.</br>
![image](https://github.com/user-attachments/assets/96472a2e-56e7-46fc-8778-887f58bfb5a8)</br>



