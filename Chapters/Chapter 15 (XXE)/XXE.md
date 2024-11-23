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

## Document Type Definition (DTD)
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
### What's the vulnerability in this?
For example, assume we have an application that allows users to upload their own xml document and if the application is using an older or misconfigured xml parser, that allows user-defined DTD or user-input within the DTD, malicious users can upload a document like the one below to read sensitive files on the server through external entities.</br>
![image](https://github.com/user-attachments/assets/53103ef4-5a14-4699-ab32-3df91281885b)</br>
>[!Note] External Entity is just a link to other resources such as file.</br>

**That means XXEs happen when:**
1. Application accepts user-supplied xml input and uses a misconficured or older xml parser.
2. Application passes user-input into DTDs which is then parsed by xml parser to read local or remotet file system files.</br>
# Prevention
Prevention mechanisms can follow multiple approaches:</br>
1. Limit XML parser capabilities
    1. Disable DTD processing on the XML parser if possible -> since DTD processing is a requirement for XXEs, then disabling it, prevents against this attack
    2. Disable external entities, parameter entities and inline DTDs(included in XML document)
    3. Limit XML parser's parse time and parse depth -> to prevent XXEs-Dos attacks 
>[!Note] Mechanisms for disabling DTD processing depends on the type of parser being used.</br>

2. Input validation
    1. Create an allowlist for user-supplied values that are passed in XML documents
    2. Sanitize hostile data within XML documents, headers or nodes
    3. Use less complex data formats like JSON whenever possible
3. Disallow outbount network traffic -> in case of blind XXEs because attacker can exfiltrate data by sending an outbound request to the attacker's server with the data stolen unlike in the case of classic XXEs, the data is returned in the HTTP response
4. Keep all dependencies in user by application or operating system up to date -> since many XXEs are introduced by the application's dependencies instead of its custom source code

