# Insecure Direct Object References (IDOR)
**IDORs** are type of bugs where the application grants access to resources based on user's request without validation.</br>
# Mechanisms
IDOR is essentially a missing access control.</br>
When an application allows users to access resources that don't belong to them by directly referencing the object ID, object number of file name, **that's when IDOR happens**.
## Example
Assume we have a website `www.example.com` that allows you chat with your friends and when you sign up in this website, it gives you a user id `1234`. </br>
Assume there's a button `View your messages` on the home page that allows you to access the messages between you and your friends and takes you to this endpoint `www.example.com/messages?user_id=1234`.</br>
If you change that `user_id` to be for example, `1233` instead of `1234` and the application still views the messages between that user and his friends, then this website is prone to IDOR vulnerability.</br>
**NOTE:** The application naively trusts user input, and it directly loads resources based on the user-provided `user_id` value.</br>


