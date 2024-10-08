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
## Bypassing Open Redirect Protection
Sites prevents open redirects by validating the URL used to redirect users >> Therefore, making the root cause of this vulnerability is the failed URL validation which is extremely difficult to get right.</br>

**Why is it difficult to get URL validation right?**
> Browsers handle URL redirection based on how they interpret the different components of a URL and these components are:</br> ![image](https://github.com/user-attachments/assets/feb0f6d4-5730-4728-bee2-048601fb9753) </br>The URL validator needs to predict how the browser will redirect users and reject malicious URLs and since the browser redirects users to the location indicated by the hostname portion of the URL and URLs don't always follow that strict format given above. Therefore causes validators to not account all the edge cases that leads to causing the browser to behave unexpectedly.<br>

**What are other formats the URL can have?**</br>
Example:</br> ![image](https://github.com/user-attachments/assets/36b4524e-cb4c-42a5-a91e-847911a6d042)

### Bypassing Strategies:
- <mark> Using browser autocorrect </mark>
  - Most modern browsers autocorrects URLs that don't have the right components resulting from user typos.
    - For example:</br>Browser interprets the following URLs as this one `https://attacker.com` </br> ![image](https://github.com/user-attachments/assets/cdd694b5-7c5e-4b0a-8eda-4d7b151457b7)</br> In this case you can bypass URL validation based on a blocklist that prevents URLs with this pattern `http://` or `https://` where you can use one of the alternative formats above to redirect to the malicious website.</br>
  - Most modern browsers corrects backslashes (\\) to be forward slashes (/) which can cause errors if the URL validator doesn't recognize this
    - For example, if we have this URL `https://attacker.com\@example.com`, the URL validator interprets this in the following manner: </br> It treats the attacker.com as the username information (refer to the previous URL format), the backward slash as path separator, and considers the part after the (\\) to be the hostname poprtion, redirecting the user to the `exampl.com`. But since the browser autocorrects the (\\) to be (/) then the URL would be `https://attacker.com/@example.com`, and in this case, the URL validator interprets the URL in the following manner:</br> the `attacker.com` portion is the hostname portion of the URL and the `@example.com` portion is the path portion and therefore, redirects the user to the `attacker.com` page.</br>
- <mark> Exploiting Flawed Validator Logic </mark>
  - One common protection against open redirects that URL validators perform is checking whether the URL starts, contains, or ends with a legitimate website such as `https://example.com`. There are ways to bypass this: 
     - Include the legitimate site's name as a subdomin, for example `https://example.com/login?redirect=https://example.com.attacker.com`</br> ![image](https://github.com/user-attachments/assets/565d5f8f-b7e2-4d75-8a21-edd7533649e5) </br>
     - Include the legitimate site's name in the path poriton (at the end of the URL) `https://example.com/login?redirect=//attacker.com/example.com`</br>
     - Combine both methods: `https://example.com/login?redirect=//example.com.attacker.com/example.com` where the URL starts and ends with both a legitimate site's name(listed in the allowlist of the URL validator) and here the real redirection happens in the `attacker.com` part. (In case of restrictive URL validators)</br>![image](https://github.com/user-attachments/assets/3103abf9-5448-492c-ba69-6939275efc80)</br>
     - Include the '@' symbol, which tells the browser that anything before the '@' is just the username and can be ignored. The real domain comes after the '@'`https://example.com@attacker.com/example.com`</br>![image](https://github.com/user-attachments/assets/5e80a63a-92b7-4329-bcbc-96b75e96f0dd)</br>
- <mark> Using data urls </mark>
  - Data URLs is used to embed files in the URL and have the following format: `data:scheme` and that scheme is constucted in the following way:</br>![image](https://github.com/user-attachments/assets/7a155965-e7ca-4774-a4c7-84ccda349244) </br> We can manipulate that scheme portion to fool the validator, HOW?
    - We can send plaintext messages using this scheme `data:text/plain,hello!` or an encoded message using this scheme `data:text/plain;base64,aGVsG8h`
    - We can also use it to embed encoded URL in the data URL scheme</br> ![image](https://github.com/user-attachments/assets/c6381a8f-c81c-40b7-977a-99f493c79955)</br> Where this previous scheme is the base64 encoded version of the following js code that redirects users to the target URL:</br> ![image](https://github.com/user-attachments/assets/150787cc-5b82-4924-a805-fb9b9da40812)</br> <mark> You can insert that data URL into the redirect parameter to bypass blocklists:</mark></br> ![image](https://github.com/user-attachments/assets/163840e0-4121-4778-86a1-79849e2c00b1)</br>

- <mark> Exploiting URL decoding </mark>
  - Purpose of URL encoding: URls sent over the internet can only contains ASCII chars that includes few English chars along with a few special characters. But since URLs needs to include more chars than the ones in the ASCII, people needed to encode URLs
  -  URL encoding converts each character into a percentage sign (%) followed by 2 hex digits. Example, encoding of (/) is (%2F)
  -  For validators to validate the URL or for the browser to redirect users, they need to decode the URL first to know what does it contains, and if there's a consistency between how validators and browsers decode URLs we can exploit that.
  -  To Exploit that we can do:
      - Double Encoding -> the purpose of double encoding is when there's a mismatch between the ways the validator and the browser decode URLs this would come in handy to bypass the URL validator, HOW?</b> ![image](https://github.com/user-attachments/assets/26ac015f-ca06-4ffa-8650-1d61f0d4938a)</br>
      - Non-ASCII Character -> We can exploit the inconsistencies in the way the validator and browser decode non-ASCII characters</br> ![image](https://github.com/user-attachments/assets/2c660882-15c8-4c63-8698-0a0c385abf4f) </br>
- <mark> Combine exploit techniques </mark>
  - Example:</br>![image](https://github.com/user-attachments/assets/86d210ef-213b-488d-a00d-2f2e3df98dbd) </br>

## Escalating the attack
Open redirects can serve as a part of a bug chain to achieve a bigger impact;</br> <mark> Example: </mark> Phishing// steal OAuth tokens (via referer header).



   
