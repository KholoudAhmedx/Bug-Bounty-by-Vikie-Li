# Insecure Deserialization
**Insecure Deserialization** is a web vulnerability that happens when the application deserialize objects without proper precausions, therefore, allowing attackers to manipulate serialized objects and change the programs' behavior.</br>
Hard to find and exploit? because it tends to look different depending on the programming language & libraries used to build the application.</br>

# Mechanisms
| Serialization     |  Deserialization       |
|------|---------|
|Is the processs of converting data into a stream of bytes to save it in a file or transfer it over network      | Is the opposite process, where it reconstructs an object back from a stream of bytes |
|Example: Saving data in json format into a file|Example: Convert the json formated data back to my original object|
</br>
**Benefits of Serialization:** </br>
1. Some objects in programming languages are difficult to transfer across network or saved in a database without corrupution -> (complex data structures, need to be converted into a stream of bytes)</br>
   Example:</br>![image](https://github.com/user-attachments/assets/fa9637e7-7dca-4121-a9f2-ab09ca38ceb1)</br>
2. 
