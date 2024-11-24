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
# Hunting for XXE
Look for functionalities where:
1. the application receives direct XML input
2. the application receives input that is inserted into XML documents that the app parses</br>

#### 1. Find XML data entry points
Applications may use XML data to transfer information within HTTP messages as show below.</br>
![image](https://github.com/user-attachments/assets/81099945-d2c1-4cca-b77c-a0d659836311)</br>
1. Find XML-like documents in HTTP messages as the prevoius example or look for XML signatures such as  `<?xml`.
    1. AND look for encoded XML data in the application (might be URL/base-64 encoded) by decoding any blocks of data that looks suspicious
2. Look for file-uploads features because XML forms the basis of many common file types (XML, HTML, DOCXPPTX, XLSX, GPX, PDF, SVG,..) + metadata embedded within images like GIF, PNG and JPEG are all based on XML>> if you are allowed to upload these file types, you can smuggle XML input to these files and if the application parses XML, you might be able to access sensitive data 
3. Instead of looking for locations where the application accepts XML data by default, force the application to parse XML data
   On endpoints that takes other formats of input, you can modify the content-type header to be one of the following and then try to include XML data in your request body:</br>![image](https://github.com/user-attachments/assets/96fd5264-61b6-4d0f-83ff-75cf2c10d407)</br>
4. Look for any user-submitted data that the application embeds into an XML-document on the server</br>
#### 2. Test for classic XXE
Try sending trial-and-error XXE payloads, and if you are getting results from the parser, then you maybe able to launch classic XXE attacks,which means you can read the leaked files directly from the server's response.</br>
1. Check whether XML entities are interpreted by inserting XML entities into the XML input and see if it loads properly as shown below</br>![image](https://github.com/user-attachments/assets/19338ddd-7c14-4657-922a-227c6e57dbc3)</br>
2. Test whether `SYSTEM` keyword is usable by trying to load local file as shown below</br>![image](https://github.com/user-attachments/assets/4fe9cdd7-6527-40c7-a65c-7182d6de4628)</br>
3. Test with `PUBLIC` keyword if system does not work, but you need additional id (The parser uses this to generate an alternate URL for the value of the entity) </br>![image](https://github.com/user-attachments/assets/6fe85528-25f1-4acf-a7c6-bf11538b8eea)</br>
4. Try to extract some common system files
#### 3. Test for blind XXE
If the server takes XML input but does not return results in the response, you can test for blink XXE by making the server send a request to the attacker's server with the exfiltrated data.</br>
1. Make sure the server can make outbound connections by making a request to your server (you can make a callback listener)
2. Try make the external entity load a resource on your machine
3. Test with ports 80 and 443 to bypass firewall detection on target because it might not allow outbound connections on other ports and then You can then search the access logs of your server and look for a request
to that particular file (typically a GET request of the requested file)</br>![image](https://github.com/user-attachments/assets/184051f7-beab-435f-8b56-81f60f8cbf4a)</br>
4. Once confirmed that you can extract files, you can then try to extract files by using the following techniques
#### 4. Embed XXE payloads in different file types
**Example 1: Insert XXE payload into SVG image** </br>
1. Open SVG image as text file
2. Insert the payload as shown below
    ![image](https://github.com/user-attachments/assets/97239e30-b628-431c-ad6f-5ac857d6ca9e)</br>
    
**Example 2: Insert XXE payload into file types .docx, .xlxs, and .pptx** </br>
Microsoft docs (.docx), PowerPoint presentations (.pptx), and Excel worksheets (.xlxs) are archive files that contain XML files, so we can insert XXE payloads into them as well.</br>
1. Unzip the document file, you should see few folders containing xml files as shown below</br>
![image](https://github.com/user-attachments/assets/5dd43569-c984-4111-8e59-acf77ed1102e) </br>
2. Insert your payload inside any of the unzipped xml files, for example inside `/word/document.xml`
3. Repack (zip) the archives into thte .docs, .xlxs, or .pptx formats again, and you can do this by running a `zip` command as shown below</br>
![image](https://github.com/user-attachments/assets/296dbc25-ba62-4abe-8c90-403a3ab9352a)</br>
#### 5. Use XInclude Attacks
If the application is taking a user-input and insert it into an XML document, you can use XInclude attacks to access sensitive files.</br>
`XInclude` is a XML feature that builds a separate XML document form a single xml tag named `xi:include`. </br>
To test for XInclude attack, insert the following payload to see if the request file is returned in the response.</br>
![image](https://github.com/user-attachments/assets/720107b6-aab8-4f62-9953-8963d6fce56d)</br>

# Escalating the Attack 
You can use XXEs to:
1. Access and exfiltrate system files, source code and list directories on local machines (READ FILES)// To read local files use this scheme `file://` followed by the path of the file you are reading
2. Perform SSRF attacks to port-scan target's network, read files on the network & access resourced hidden behind firewall
    Example 1:</br>
    ![image](https://github.com/user-attachments/assets/4f9ca310-459c-4db4-a244-4ecf75b6dd85)</br>  
    Example 2: (returns AWS metadata)</br>
    ![image](https://github.com/user-attachments/assets/42b1337e-94a9-4c91-8dfd-c4f2530898c1)</br>
3. Lauanch DoS attacks
>[!Note]
>When trying to exfiltrate unintended data, check the page source or the http repsonse directly rather than the HTML page rendered by the browser as it might not be rendered correctly.</br>



