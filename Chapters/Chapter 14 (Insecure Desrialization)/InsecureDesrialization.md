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
