# Sever Side Template Injection (SSTI)
**Template engines ->** type of software that determines the appearance of a web page.</br>
SSTI can lead to RCE.</br>
# Mechanims
Template engines are used to create dynamic web pages by combining the application data (data returning from server) with web templates.</br>
Templates are predefined structure of layout and content. </br>

An Example of Template file:</br>
`<h1> Welcome, {{ name }} </h1>` where name is the dynamic content that is passed from the server.</br>
::Template engines along with web templates allow developers to separate server-side application logic from client-side presentation code during web development which help in modifying and maintaing code.</br>
::Template engines also make it rendering web pages more efficient. </br>
## Example
This is an exmaple Template file written in Jinja, a template language for Python.</br>
![image](https://github.com/user-attachments/assets/9e882e66-b4f8-4568-a667-79393f4970c6) </br>

</br> **Notes:** </br>
1. Code surrounded with {{ }} is an expression
2. Code surrounded with {{%  %}} is an statmenet </br>

**Difference between statements and expressions in progmramming languages:**
Statement -> is code that doesn't return anything. </br>
Expression -> is either a variable or a function that returns a value.</br>

Combining the template file with Python code to create the complete dynamic HTML page will look like this:</br>
![image](https://github.com/user-attachments/assets/54da8d50-8207-4820-9fe0-a0ea53be8e01) </br>
![image](https://github.com/user-attachments/assets/613b8cef-4232-458a-b65d-1d38aabbfe00) </br>

The template engine will combine the template page/file (in our case example.jinja) with the data provided in the Python script to create the following dynamic HTML page:</br>
![image](https://github.com/user-attachments/assets/04684d30-8299-4b15-9197-70cdf68f31c0) </br>



