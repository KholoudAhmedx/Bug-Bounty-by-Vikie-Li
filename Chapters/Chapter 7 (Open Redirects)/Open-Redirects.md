# Open Redirects
Open Redirect is a web vulnerability that occurs when an attacker manipulates the value of HTTP or URL paramaters that websites often use to redirect users to another URL without user interaction. 
# Mechanisms 
1. **Using URL parameters** </br>
Websites often need to automatically redirecting users to save them time and improves their experience and a common scenario is when a user is trying to access a page that requires authentication.  </br> In the image below, the attacter tricks the user by providing them a URL from a legitimate site that redirects them to the malicious site,for example: `http://example.com/login?redirect=https://attacker.com`. After that, the attacker can launch a social engineering attack to steal users credientials. </br>
**Scenario 1:** 
![image](https://github.com/user-attachments/assets/e39c4e2d-52df-47ed-8217-374c742fa934) </br>

2. **Referer-based open redirects (HTTP Request)**\
Another common open-redirect technique is the referer-based open redirect.</br> In the image below, the user clicks on a link that redirects to the legitimate login page and the request is sent to `https://example.com/login`, that request contains a referer header which tells the location where your request comes from (in this case the attackers website `https://attacker.com`), then after logging in, it redirects the user back to the attacker's webpage.</br>
**Scenraio 2:**
![image](https://github.com/user-attachments/assets/b787ee1d-a9df-40a9-93a8-84a6773e947b) </br>
>[!Note]
>This happens when referer-based redirects are not validated and attackers exploit this behaviour by tricking users to start from their own page. </br>
# Prevention
Websites implement URL validators to ensure user-provided redirect points to a legitimate location.\
These validators uses either allowlist or blocklist.\
<mark>Allowlist</mark> -> checks whether the hostname portion of the URL matches allowed hostnames, then the redirect goes through.\
<mark>Blocklist</mark> -> checks whether the redirect URL contains malicious hostnames or special characters used in attacks, then it blocks the request.
>[!NOTE]
>Validators can hardly identify hostname portions of the URL as decoding and parsing URL is difficult to get right, which makes open redirect one of the most common web vulnerabilities. 

# Hunting for Open Redirects
- Look for redirect parameters
  - Open proxy while browsing the website
  - Check http history and look for any parameters contains relative or absolute URLs
    - <mark> Absolute URL </mark> -> URL is complete and contains all information neccessary to locate a resource `https://example.com/login`
    - <mark>  Relative URL </mark> -> Contains only the path component and must be concatenated to a URL by the server `/login` and some omits the (/) .
  - Example on redirect parameters: ![image](https://github.com/user-attachments/assets/a9a133ff-c27f-455d-a024-78565ae9d098) </br>
  - >[!Note]
    > Not all redirect parameters have straightforward names such as `redirect` and `redir`.\
    > Pages that do not have redirect parameters but still redirects users automatically are candidates for referer-based open redirects and to find them, focus on 3XX status codes such as 301         and 302 codes.
- Use google dorks to find additional redirect params
  
  - >[!NOTE]
    >Google Dorks are advanced search techniques that use special operators to find specific and often hidden information which makes it suitable for finding redirect parameters.\
    >Special Operators are commands such as `site` , `inurl`, `intext` and more.\
    >`Site` -> finds result on a specific website.\
    >`Inurl` -> searches for a keyword in a URL.
  - Example:
    - Use `site : example.com` to define your target website and then you can use `inurl` to search for specific terms in the URL
    - To look for pages that contain Relative URls in their URL parameter, make use of `inurl: %3D%2F site: example.com` where `%3D` is the URL encoded version of (=) and `%2F` is the URL encoded version of (/), so you are looking for the terms `=/` in the URL given.\
      - Example results are: ![image](https://github.com/user-attachments/assets/ffc59d96-28ea-4049-bac4-0b6b17942cb8)
    - To look for pages that contain Absolute URLs in their URL parameter, make use of `inurl: %3Dhttp site:example.com` or `inurl: %3Dhttps site:example.com`.
      - Example results are: ![image](https://github.com/user-attachments/assets/07d96a3c-bde6-4945-a794-076f213892a6)
    - Search with common redirect parameters such as the following:
        ```
          inurl:redirecturi site:example.com
          inurl:redirect_uri site:example.com
          inurl:redirecturl site:example.com
          inurl:redirect_uri site:example.com
          inurl:return site:example.com
          inurl:returnurl site:example.com
          inurl:relaystate site:example.com
          inurl:forward site:example.com
          inurl:forwardurl site:example.com
          inurl:forward_url site:example.com
          inurl:url site:example.com
          inurl:uri site:example.com
          inurl:dest site:example.com
          inurl:destination site:example.com
          inurl:next site:example.com
        ```
- Test for parameter-based open redirects
  - Insert a random hostname into the redirect parameter, then see if the site redirects you automatically to the specified hostname.
    ![image](https://github.com/user-attachments/assets/f4b44b6b-fa33-4698-a887-1e87b1063d41)
  
- Test for referer-based open redirects
  - Set up a page on a domain you own and contain an anchor tag inside it that refer to the login page of your target site `<a href="http://example.com">Click link to login to example.com</a>` and then refreshes the page, click on the link to log in and wait to see if you are redirected to your site.       

    
   
