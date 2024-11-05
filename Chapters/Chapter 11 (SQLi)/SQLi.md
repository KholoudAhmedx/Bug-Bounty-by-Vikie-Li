# SQL Injection (SQLi)
SQL injection is an attack in which the attacker executes arbitrary SQL commands on an application's database by supplying malicious input inserted into the SQL statement. 
## What Attackers Can Acheive with SQLi
Attackers can
1. Bypass authentication
2. Data leaks
3. Tampering of database
4. RCE

# Mechanisms
SQLi happens when the attacker injects code (sql commands) in the sql statements that the target application uses to access its DB, thereby executing whatever SQL code the attacker wants. 
| First Order SQLi       | Second Order SQLi           |
|--------|------------|
| Happens when application uses user-submitted input directly into the SQL query | Happens when user input gets stored into a database, then retrieved and used unsafely in a SQL query
 
Attackers can inject characters that are special to the SQL to mess with the logic of the query</br>
### Examples on First Order SQLi
1. The attacker can send a payload like this in the username field while trying to login:</br>
  ![image](https://github.com/user-attachments/assets/d43e3fb8-4e5a-4293-b2f8-93b39c508739) </br>
  and the query will now be: </br>
  ![image](https://github.com/user-attachments/assets/b656954a-d0c7-4fc0-8692-cb60498c2a8a) </br>
  `admin';--` break down:</br>
         ' closes the value of the username and ; indicates the end of the query and -- is used to comment the rest of the query.</br>
   and now the query will be as if the user requests the id of the admin without providing the password which causes authentication bypassing:</br>
   ![image](https://github.com/user-attachments/assets/7f79985c-3b6a-4abc-a1c8-f71cbe8ee424)</br>
2. Another example, is when attacker accesses data he is not allowed to by injecting a payload like this: </br>
   ![image](https://github.com/user-attachments/assets/592b5ac8-e7a6-4ad3-8c0e-a21eb101142d)</br>
   This request is used to retreive emails of the user by giving their username and access key, but here the attacker uses SQLi to retreive data from another table, which is in this case the usernames and passwords.</br>
   The SQL request is now turned to be this one:</br>
   ![image](https://github.com/user-attachments/assets/4bd540fc-d4eb-4cea-ae5c-5a21e65252ec)</br>
   >[!Note]
   >The original SQL request was `SELECT Id FROM emails WHERE username=vikie AND accesskey='ZB6w0YLjzvAVmp6zvr';` </br>
### Examples on Second Order SQLi
Imagine a website where users can create new accounts by providing their usernames and passwords by sending a request like this to the server:</br>
![image](https://github.com/user-attachments/assets/39ff51df-8e8d-414f-891b-ffa6b3d179f4)</br>
This request submits the username vickie' UNION SELECT Username, Password FROM Users;-- and the password password123 to the `/signup` endpoint.</br>
And assuming the application handles the userinput properly using protection techniques and now the username is stored in the database. Now imagine there's another endpoint `/emails` where users can retrieve their emails by sending a request to `/emails` endpoint, and if they don't include their username, the request will get the username of the logged in user automatically and the corresponding sql query is:</br>
![image](https://github.com/user-attachments/assets/dc8767a2-2c88-4311-8be6-8368fff3b219)</br>
So when the server tries to retrieve the username of the logged in user, in our case `vickie' UNION SELECT Username,
Password FROM Users;--`, the sql code will be executed, returning all useranmes and passwords of users as email titles and bodies in the HTTP response.</br>
# Prevention
1. Use prepared statements (also called parameterized quieries)
2. Use allowed list for allowed values
   1. For example, if the application allows users to retreive their emails by providing their usernames and accesskeys and allows them to order their retrieved emails by a specific column (data column for example), the application can define list of allowed values (columns) the user can order their retrieved emails by, instead of allowing arbitrary input from the user;; (Allow Sender, Date and Title) (Reject all other user-input value).</br>![image](https://github.com/user-attachments/assets/ee5c19d1-250b-4319-beb0-1c4002cc1200)</br>
3. Sanitize and escape user input (not that strict because it it easily to miss special chars that attacker can use to construct SQLi attack)

### Why prepared statements make SQLi virtually impossible?
To understand why, we need to go through the life of a SQL query. SQL query is a program that when arrives at the SQL server, it gets compiled, parsed and optimized. Then the server will execute the query, and return the results as shown below:
</br>![image](https://github.com/user-attachments/assets/91a4f084-6c11-4d96-b0c4-9c1bd26805f0)</br>
When injecting user input into the SQL query, user input will be sent along with the query to the SQL server and and then gets compiled, parsed and optimized, meaning that it will be interpreted by the server as code if this user input is malicious and contains sql statement. </br>
![image](https://github.com/user-attachments/assets/646e7249-2849-475b-a886-75e4385f326c)</br>
**Prepared statements** work by making sure that user-supplied data does not alter your SQL query's logic. SQL statements are sent and compiled by the server before any user-supplied parameters are inserted, meaning that you define the SQL logic first, compile it and then insert user-supplied parameters into the query right before execution as shown below:</br>
![image](https://github.com/user-attachments/assets/44ea13f3-4a6b-4299-84ab-bfa7b159683b)</br>
After the parameters are inserted into the final query, the query will not be parsed and compiled again, so the database will distinguish between the code part and the data part of the SQL query.
# Hunting for SQLi 
A classification for SQL injections that helps in exploiting them is as the following, SQLi is classified into:</br>
1. Classic SQLi
2. Blind SQLi </br>

**A common way** to test for SQL injection -> insert singl quote (') into every user input in the application, and if the application is protected against SQLi, it should not trigger a database error, or alter the logic of the database query.</br>
**Another general way** to test for SQL injection -> fuzzing, where you sumbit specifically designed sql injection payloads to the application and monitor the server's respone.</br>
**Otherwise**, submit payloads designed to trigger a difference in database response, time delay, or a database error. </br>
### 1. Look for Classic SQLi
>[!Note] Classic SQLi is the easiest to find and exploit.</br>
In Classic SQLi -> results are returned directly to the attacker in the HTTP respone.</br>
Classic SQLi is divied into two types:
>Union based SQLi -> attacker uses UNION operator to concatenate the results of another query into web application response.
>Error based SQLi -> attacker triggers an error in the database to collect information from the returned error message.
#### Example on Union based SQLi
An attacker injects another query using UNION operator to retrieve usernames and passwords of all users as an email title and body:</br>
![image](https://github.com/user-attachments/assets/febea087-3945-43fe-8743-d250b8308980) </br>
The returned HTTP response is as the following:</br>
![image](https://github.com/user-attachments/assets/37bb90e4-d83d-4125-856b-c2c6cb3c0726)</br>

#### Example on Error based SQLi
An attacker injects CONVERT keyword to trigger and error in the database and reveal information from the returned error, where CONVERT takes two parms CONVERT(VALUE, FORMAT), and converts the value to the given format. For example: </br>
![image](https://github.com/user-attachments/assets/d558b236-eb32-4bfc-84bc-a872f4ce75d1)</br>
Here, the query will force the database to convert the password of the admin to a date format which returns the following error that reveals password of the admin:</br>
![image](https://github.com/user-attachments/assets/edc926b1-1241-4c48-bc4a-c2e85c88683f)

### 2. Look for Blind SQLi
>[!Note] Blind SQLi is harder to find and exploit.</br>
Blind SQLi happens -> when attacker cannot extract information directly from the database because the application doesn't return SQL data or descriptive error messages, therefore, the attacker inferes information by sending SQLi payloads to the server and monitor the subsequent server behavoir.</br>
Blind SQLi has two types:
1. Boolean based -> attacker injects test conditions into the SQL query that will return either true or false and based on these responses, the attacker can slowely infere the contents of the database. 
2. Time based -> similar to Boolean based, but instead of relying on visual clue in the web application, the attacker relies on the response-time difference caused by different SQLi injection payloads.
##### Example on Boolean Based SQLi

#### Example on Time Based SQLi
