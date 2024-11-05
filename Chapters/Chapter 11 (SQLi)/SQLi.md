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
2. 

### Why prepared statements make SQLi virtually impossible?
To understand why, we need to go through the life of a SQL query. SQL query is a program that when arrives at the SQL server, it gets compiled, parsed and optimized. Then the server will execute the query, and return the results as shown below:
</br>![image](https://github.com/user-attachments/assets/91a4f084-6c11-4d96-b0c4-9c1bd26805f0)</br>
When injecting user input into the SQL query, user input will be sent along with the query to the SQL server and and then gets compiled, parsed and optimized, meaning that it will be interpreted by the server as code if this user input is malicious and contains sql statement. </br>
![image](https://github.com/user-attachments/assets/646e7249-2849-475b-a886-75e4385f326c)</br>
**Prepared statements** work by making sure that user-supplied data does not alter your SQL query's logic. SQL statements are sent and compiled by the server before any user-supplied parameters are inserted, meaning that you define the SQL logic first, compile it and then insert user-supplied parameters into the query right before execution as shown below:</br>
![image](https://github.com/user-attachments/assets/44ea13f3-4a6b-4299-84ab-bfa7b159683b)</br>
After the parameters are inserted into the final query, the query will not be parsed and compiled again, so the database will distinguish between the code part and the data part of the SQL query.



