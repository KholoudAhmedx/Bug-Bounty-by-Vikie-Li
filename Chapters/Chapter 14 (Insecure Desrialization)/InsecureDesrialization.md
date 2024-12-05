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

#### Example
Assume the server has this vulnerable code that takes the value of a data cookie and unserializes it as shown below:</br>
```
   <?php
   class Example2
   {
      private $hook;
      function __wakeup(){
         if(isset($this->hook)) eval($this->hook);
      }
   }

   $userdata = unserialize($_COOKIE['data']);
   ?>
```
Notice that this code has `__wakeup()` function that if the `hook` property is defined (not null), it will execute `eval()` function on it that takes the string passed to it and execute it as php code.</br>
Since the value of data cookie is contollable, the attacker can pass a malicious serialized object into the data cookie to achieve RCE.</br>
**HOW?**</br>
1. Attacker creates serialized object as the following:</br>
   ```
   <?php
   class Example2 {
      private $hook = "phpinfo();";
   }
   print urlencode(serialize(new Example2));
   ?>
   ```
   In this code, the attacker created a serialized object from class Example2 that initializes the value of `hook` property to be `phpinfo();`.Then this serialized object is being passed to `urlencode()` to encode it since it will be embeded inside a cookie value, so when this value is passed to the `unserialize()` function in the sever's code, this will happen:</br>
   1. `unserialize()` reconstructs the object from class Example2 form the data cookie value
   2. It then looks if there's `__wakeup()` method defined in the code and in this case it is, so it will excutes the code inside it that checks if hook is set then run eval, that will executes `phpinfo();` method and VOILA you've achieved RCE by running php code.</br>
   
#### Another magic methods
There are 4 magic methods that can particulary useful in exploiting `unserialize()` vulnerability: `__wakeup()`, `__destroy()`,  `__call()` and `__toString()`, and there are more.</br>
`__toString()` => is called when the object is treated as a string?</br>
   For example when the object is passed to a function that is expecting a string such as `echo` and `print`. </br>
   Example:</br>
   ```
   class Example{
   public $name;
   function __toString(){
   return $this->name;
   }
   }
   $user = new Example;
   $user->name = "hello";
   echo $user;
   ```
   Notice that if you don't explicitly defined the function `__toString()` you will get an error message that tells you could not be converted to string.</br>
`__call()` => invoked when an undefined method is called.</br>
Example of a vulerable code:</br>
```
class Demo{
function __call($name,$args){
eval($name.'();';
}

}
$func = new Demo;
$method = "phpinfo";
$func->$method();
```
This will execute `phpinfo()`, because we are called an undefined method called `$method` in the previous code, so the `__call()` magic method is invoked.</br>
>[!Note]
>The exploitability of this magic method varies widly, depending on how it is implemented.
> Sometimes attackers can exploit this magic method when the application’s code contains a mistake or when users are allowed to define a method name to call themselves.</br>
### Another Way to Get RCE: POP Chains
Why this method is introduced? because magic methods implemented in cod might not contain injection opporitunities.</br>
**Property-oriented programming (POP)** chain -> is an exploitation technique that can be used to acheive RCE by taking advantage of desrialization vulnerabilities in PHP. </br>
**Key idea**:
1. Attacker takes control of all properties of deserialized objects and uses existing methods (gadegts) to chain malicious behavior.</br>
**Gadgets:** are codesnippets that are borrowed from the codebase, meaning that it is not newly introduced.</br>
POP chains use magic methods as their starting point.</br>
**Example:**</br>
![image](https://github.com/user-attachments/assets/fdfdae90-1643-4b10-b922-bc4fa9b2656d)</br>
This code has deserialization vulnerability at line 5 , so the attacker can generate a code such as the following to exploit this vulnerability using POP chains.</br>
![image](https://github.com/user-attachments/assets/8d1aefd1-19e2-452e-b204-967706f96226)</br>
Notice that the attacker has created an Example class that has a construct function that initializes the `$obj` property with an instance of codesnippets class, that therefore, initializes the `$code` property to be the vulnerable php code `phpinfo();`;</br>
Notice that the attacker’s serialized object uses class and property names found elsewhere in the application’s source code.</br>

<mark>POP chains achieve RCE by chaining and reusing code found in the application’s codebase.</mark>








