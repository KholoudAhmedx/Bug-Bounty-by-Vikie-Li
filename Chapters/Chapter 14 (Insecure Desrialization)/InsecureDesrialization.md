# Insecure Deserialization
**Insecure Deserialization** is a web vulnerability that happens when the application deserialize objects without proper precausions, therefore, allowing attackers to manipulate serialized objects and change the programs' behavior.</br>
Hard to find and exploit? because it tends to look different depending on the programming language & libraries used to build the application.</br>

# Mechanisms
| Serialization     |  Deserialization |
|------|---------|
|Is the processs of converting data into a stream of bytes to save it in a file or transfer it over network| Is the opposite process, where it reconstructs an object back from a stream of bytes |
|Example: Saving data in json format into a file|Example: Convert the json formated data back to my original object|

**Benefits of Serialization:** </br>
1. Some objects in programming languages are difficult to transfer across network or saved in a database without corrupution -> (complex data structures, need to be converted into a stream of bytes)</br>
   Example:</br>![image](https://github.com/user-attachments/assets/fa9637e7-7dca-4121-a9f2-ab09ca38ceb1)</br>
2. Allow programming languages to reconstruct identical program objects in different computing environments -> (save the state of the object for recreation without having to reprocess them)</br>

**Insecure Deserialization leads to:** </br>
1. Authentication bypasses
2. RCE</br>

**Insecure deserialization** happens when the attackers **abuse** the trust the developers have of serialized data to be unreadable to users.</br>
**Scenario #1:** </br>
If an application takes a serialized object (e.g. data in json format) from the user and uses the data contained in to to authenticate users, the malicious user can tamper the serialized object to authenticate as someone else.</br>
**Scenario #2:** </br>
If the application uses unsafe deserialization, the malicious user can embed code snippets into the object that gets executed during deserialization.</br>

>[!Note]
>The best way to learn serialization and deserialization is to learn how different programming languages implement both.</br>
## Insecure Deserialization in PHP
Also called **PHP object injection** vulnerabilities.</br>
### How PHP serializes and deserializes objects? </br>
   PHP uses `serialize()` and `unserialize()` functions to achieve this task, so for example we have this code snippent: </br>
   
       ```
       <?php
       class User{
       public $username;
       public $status;
       }
       $user = new User;
       $user->username='vikie';
       $user->status='not admin';
       echo serialize($user);
       ?>
       ```
   This will return this string `O:4:"User":2:{s:8:"username";s:6:"vickie";s:6:"status";s:9:"not admin";}` where 0 represents object instance, 4 represents length of object string ("User"), 2 represents number of properties of that object (in this case, username and status), s represents string value, and 8,6,9 represent the lenght of the strings; and you can refere to this image below for more info about how this string is created</br>
       ![image](https://github.com/user-attachments/assets/35626ca3-f540-4d51-8b2e-076b0baa79f3) </br>
       
   >[!Note] The general format is `datatype:data`.</br>
   
And to deserialize objects, PHP can use `unseralize()` function to deserialize objects, so instead of echoing the serialized object as in the previous example, we can save the object in a variable, and pass it to the `unserialize()` function to deserialize it and then use a function like `var_dump()` to display the value of that deserialized object.

```
   <?php
$serialized_string = serialize($user);
$unserialized_data = unserialize($serialized_string);
var_dump($unserialized_data);
?
```
The output will be:</br>![image](https://github.com/user-attachments/assets/d2b3521c-39ef-4f35-8b54-d19ca65f528e)</br>
>[!Note]
>If you used  `echo` to read the unserialized_data you will get an error, that tells you cannot convert object to string.</br>

### How to tamper serialized objects?</br>

One way the attacker can tamper serialized objects is when they are not encrypted or signed, so as in the previous example when echoing the serialized object the attacker can tamper that object to do something malicious. For example if the application is using this serialized object directly to sign (authenticate) users without encryption, then the attacker can change the status of 'Vikie' user to be admin by intercepting the traffic and sending a file with the new object values, or send the object in the request directly and see if we get admin privileges.</br>

![image](https://github.com/user-attachments/assets/e6eca483-6c72-4c51-a82c-792112752f8e) </br>

### How to get RCE?</br>
First we need to understand how PHP creates and destroys objects.</br>
Magic methods -> are methods in PHP that have special properties such as automatically run during specific point of execution or when certain conditions are met.</br>
Two of these functions are : `__wakeup()` and `__destory()`.</br>
`__wakeup()` -> is used during initialization when program creates an instance of a class in memory (which happens in the case of `unserialize()` function).
`__destroy()` -> cleans up the object when no references is made to it.</br>

So the process is as the following:</br>
1. `unserialize()` reconstructs the object from the stream of bytes (string/serialized object)
2. It looks for `__wakeup()` if it defined in the class of the object, to reconstruct any resources user has (e.g. database connections) and other reinitialization tasks
3. The program operates on the object & use it to perform other actions
4. When no references is made to the object, `__destroy()` cleans the object</br>

The role of the attacker comes here: bothe `__wakeup()` and `__destroy()` has code in it to execute, and destroy has code and files associated to the object that it deletes so **maybe able to mess with the integrity of the filesystem by controlling the input passed into those functions.** </br>




