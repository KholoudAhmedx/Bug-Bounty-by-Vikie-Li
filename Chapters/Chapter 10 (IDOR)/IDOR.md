# Insecure Direct Object References (IDOR)
**IDORs** are type of bugs where the application grants access to resources based on user's request without validation.</br>
# Mechanisms
IDOR is essentially a missing access control.</br>
When an application allows users to access resources that don't belong to them by directly referencing the object ID, object number of file name, **that's when IDOR happens**.
## Example 1
**IDORs and accessing other's information** </br>
Assume we have a website `www.example.com` that allows you chat with your friends and when you sign up in this website, it gives you a user id `1234`. </br>
Assume there's a button `View your messages` on the home page that allows you to access the messages between you and your friends and takes you to this endpoint `www.example.com/messages?user_id=1234`.</br>
If you change that `user_id` to be for example, `1233` instead of `1234` and the application still views the messages between that user and his friends, then this website is prone to IDOR vulnerability.</br>
**NOTE:** The application naively trusts user input, and it directly loads resources based on the user-provided `user_id` value.</br>

## Example 2
**IDORs and editing other's information** </br>
Assume we have a website that allows you to change your password by sending a POST request to this endpoint `/change_password` and provides your user_id and the new password as shown below:</br>
![image](https://github.com/user-attachments/assets/4c295c64-50c8-4a50-8ef5-8f4480664a01) </br>
An attacker can change the `user_id` to be something else for example, `1233` and therefore changes the password of another user, if the application doesn't validate that the submitted user ID corresponds to the currently logged-in user.</br>

## Example 3 
**IDORs and affecting resources other than database objects** </br>
Assume we have a website that allows users to reference system files directly through a URL like this `https://example.com/uploads?file=user1234-01.jpeg` , a user can easily deduce that user-uploaded files has a naming convention `USER_ID-FILE_NUMBER.FILE_EXT` , therefore an attacker can access the uploaded files of another user as for example `user1233-01.jpeg` if the application doesn't restrict user's access to files that belong to another users.</br>
