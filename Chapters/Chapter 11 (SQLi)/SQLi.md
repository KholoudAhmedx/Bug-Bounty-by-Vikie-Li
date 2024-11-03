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

1. Attackers can inject characters that are special to the SQL to mess with the logic of the query</br>
  For example, the attacker can send a payload like this in the username field while trying to login:</br>
  ![image](https://github.com/user-attachments/assets/d43e3fb8-4e5a-4293-b2f8-93b39c508739) </br>
  and the query will now be: </br>
  ![image](https://github.com/user-attachments/assets/b656954a-d0c7-4fc0-8692-cb60498c2a8a) </br>
  `admin';--` break down:</br>
         ' closes the value of the username and ; indicates the end of the query and -- is used to comment the rest of the query.</br>
   and now the query will be as if the user requests the id of the admin without providing the password which causes authentication bypassing:</br>
   ![image](https://github.com/user-attachments/assets/7f79985c-3b6a-4abc-a1c8-f71cbe8ee424)</br>
   Another example, is when attacker accesses data he is not allowed to by injecting a payload like this: </br>
   ![image](https://github.com/user-attachments/assets/592b5ac8-e7a6-4ad3-8c0e-a21eb101142d)</br>
   This request is used to retreive emails of the user by giving their username and access key, but here the attacker uses SQLi to retreive data from another table, which is in this case the usernames and passwords.</br>
   The SQL request is now turned to be this one:</br>
   ![image](https://github.com/user-attachments/assets/4bd540fc-d4eb-4cea-ae5c-5a21e65252ec)</br>
   >[!Note]
   >The original SQL request was `SELECT Id FROM emails WHERE username=vikie AND accesskey='ZB6w0YLjzvAVmp6zvr';`
2. 



