# XML External Entity (XXE)
XXEs are web security vulnerabilities that targets XML parsers of an application.XXEs are very impactful bugs but they are very difficult to understand and exploit.</br>
XXEs can lead to:</br>
- Information Disclosure
- DoS attacks
- SSRF attacks</br>

# Mechanisms
## What is XML?
XML is an exensible markup language that allows developers to represent arbitrary data structures in a tree-like format like that of HTML.</br>
XML is used in storing and transporting data.</br>
XML is used in multiple web functionalities such as:</br>
- File Transfers
- Image Uploads
- Authentication
- Transfering HTTP requests from client to server and back</br>

## Document Type Definition (DDT)
DTD defines the structure of XML document and the data it contains. </br>
Example of DTD: `<!DOCTYPE example[!ENTITY file "Hello!"]>` where example is the root element for which the DTD applies.</br>
`[..]` contains the internal DTD subset which defines entities, elements or attributes defined in the doc. </br> 
You can reference `file` entity by using this syntax `<example> &file; </example>` where `file` entity works as a programming variable that when called, loads the value of `file` in its place.</br>
XML docs can also use external entities to access either local or remote content with a URL.</br>
**Example of local content:** </br>
![image](https://github.com/user-attachments/assets/70cac180-8e0b-4656-a960-bec97d31d6b2)</br>
>[!NOTE]: if entity is preceeded by `SYSTEM` keywork -> then it is an external entitiy.</br>

**Example of remote content:** </br>
![image](https://github.com/user-attachments/assets/e31a0bd9-c227-4f21-8307-1ab788cbdb2f)</br>


