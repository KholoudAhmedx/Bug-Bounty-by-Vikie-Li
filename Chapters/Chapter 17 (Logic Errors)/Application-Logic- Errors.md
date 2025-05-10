# Application Logic Errors and Broken Access Control
While vulnerabilities such as XSS, CSRF, etc,. are caused by faulty input validation, application logic erros and broken access control vulnerabilities are caused/triggered by perfectly valid HTTP requests that contain no ilegal or malformed inputs but crafted intentially to misuse the app's logic for malicious purposes.</br>

|Application Logic Errors|Broken Access Control|
|------|-------|
|Logic flaws in the application | Occurs when sensitive resources or functionalities are not properly protected|

# Application Logic Errors
Application Logic Erros (aka Business logic errors) are ways of using legitimate logic flow of an application to deliver uninteded consequences to the organization.</br>
**Common Logic Error Vulnerability**:
- Logic flaw in MFA functionality;

**MFA** => is the practice of requiring users to prove their identities in differnt, multiple ways.</br>
MFA helps in protecting against password compromise attacks, where it requires users to prove their identities by providing their passwords, along with authentication code (for example, there are different methods) sent via their email or text message. </br>

## Example 1: Error Logic in MFA flow
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
