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

# Prevention
IDORs happen when an application fails at two things:</br>
1. Implementing access control based on user's identity
2. Randomizing object IDs and instead keep references to data entry or files, predictable
</br>

Application can prevent IDORs in two ways:</br>
1. Application checks user's identity and permissions before granting access to a resource</br>
  **Example:** </br>
  Application checks if the user's session cookie corresponds to the `user_id` whose messages is being requested. 
2. Application can use a unique, unpredictable key or a hashed identifier to reference each user's resources such as this one `https://example.com/messages?user_key=6MT9EalV9F7r9pns0mK1eDAEW` .</br>
>[!Note]
>Hashing IDs with a secure algorithm and a secret key makes it difficult for attackers to guess the hashed ID string. (isn't a complete protection against IDORs)</br>

# Hunting for IDORs 
The best way to hunt for IDORs is through source code review that checks if all direct object references are protected by access control. </br>
If you cannot access source code, here's some tips to help you find them:</br>
1. **Prepare Accounts:**  
   - Create two accounts per permission level (e.g., two admin accounts, two regular user accounts).  
   - Use one account as the attacker (to launch IDOR attacks) and the other as the victim (to demonstrate impact).  

2. **Test Unauthenticated Access:**  
   - Repeat your tests without signing in to see if unauthenticated users can access functionalities or information intended for legitimate users.  

3. **Discover Features:**  
   - Explore all application features and focus on those that:  
     - Return user-specific information (e.g., display messages based on user ID).  
     - Modify user data (e.g., delete messages of a user).  
   - Document these features for reference.  

4. **Intercept Requests:**  
   - Capture all requests sent from the client to the server.  
   - Look for parameters containing numbers, usernames, or IDs in these requests.  

5. **Switch IDs:**  
   - Modify IDs in sensitive requests to see if the application returns or modifies data for other users.  

By following these steps, you can systematically identify IDOR vulnerabilities and assess their impact.  
>[!Note]
>You can trigger IDORs from different locations within the request, like URL parameters, form fields, file paths, headers and cookies.</br>
