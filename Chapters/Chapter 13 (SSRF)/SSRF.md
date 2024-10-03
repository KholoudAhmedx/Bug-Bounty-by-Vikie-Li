# Server-Side Request Forgery (SSRF)
SSRF is a vulnerability that allows the attacker to send requests on a behalf of a sever, therefore allowing the attacker to access services that requires higher prrivleges, sensitive data they are not allowed to access.
# Mechanism
Imagine we have  a company/network that has a public server at `http://public.example.com` and this server has a proxy service that fetches web pages by defining their URL in a `url` query parameter and displays it to the user, `http://public.example.com/proxy?url=www.google.com` -> this fetches `google.com page` to the user.</br>
And imagine there is another internal service at `http://admin.example.com`, that can be accessed only by internal employess of the company (By checking the IPs of their hosts) and is restricted to be reached out via the internet. IF no SSRF protection is applied on the public server, the attacker can trick the public server into sending the request and access the internal services (in this case, fetch the admin panel) since the server is in the same network (trusted IP) by accessing this URL, `http://public.example.com/proxy?url=http://admin.example.com`.</br>
***Why this happened?*** Because the protection that hides admin panels from the internet do not apply to the requests sent between the server and the admin panel server.</br>
# Types of SSRF
SSRF has two types that both depends on the same mechanism of exploiting the trust between machines on the same network.</br>
#### Types:
  1. Regular SSRF
  2. Blind SSRF </br>
Blind differs from Regular SSRF in that it doesn't return a feedback to the attacker.</br>
#### <mark>Example:</mark> </br>
1. Regular SSRF -> as in the previous request `http://public.example.com/proxy?url=http://admin.example.com`, this returns the admin panel to the attacker, therefore knows that the attack has succeed
2. Blind SSRF -> as in this request, `https://public.example.com/send_request?url=https://admin.example.com/delete_user?user=1`, the user will be deleted but not feedback is returned to confirm the attack has succeeded.
# Prevention
To prevent SSRF from hapening, we need to prevent the server from sending requests to internal resources the same way it does with the external resources.</br>
**Example:** </br>
If you are about to post a link on a twitter, the server sends a request to that external server to fetch an image and creats a thumbnail, so if it does the same with internal resources, an SSRF orginates.</br>
**Another Example For Clarifications: **</br>
If we have a website that retrieves a profile picture of a user from a url and to upload it, the request may be something like this:</br>![image](https://github.com/user-attachments/assets/bfbceced-2f13-4ad5-98e2-050608285489) </br>
This is the safe and intended functionality. If the serve is not performing the correct restrictions, the attacker could send a request like this:</br>![image](https://github.com/user-attachments/assets/2b3425ff-b609-49d4-a52e-9f123f2ddf2e) </br> that causes the server to retrieve sensitive resources such as password file, and display it to the user. 
>[!NOTE]
> `localhost` points to the server that handles the request, not the client (your machine). So when the web server recieves a request that includes `http://localhost/`, it interprets that as a request to itself.</br>
## Prevention Methods
#### There are three ways to prevent against SSRFs:</mark>
- Blocklists -> companies blocklist internal network addresses and reject any requests that redirects to those addresses as there are way too many external addresses to allow:D"
- Allowlists -> servers allow requests that contain URLs found in a premade list and reject all other requests
- Servers also protect against SSRF by requiring special headers or secret tokens in internal requests. 
# Hunting for SSRFs
#### There are two ways to discover SSRF vulnerabilities: 
1. Check the application's source code to see if all user-provided URLs are validated
2. Check features that are prone to SSRF such as file uploads, document and image processors (such as pdf converter websites), proxy services, link expansion and thumbnails (such as the ones on Twitter and Facebook) and webhooks </br>
#### Example of Potential SSRFs endpoints:
![image](https://github.com/user-attachments/assets/b2017543-f372-4779-8be3-23b7df089771)

#### Steps: 
1. <mark> Spot features prone to SSRFs </mark> (features that require visiting and fetching external resources; mentioned previously) // Test any endpoints the processes user-provided URLs // Pay attention to URLs embedded in files that are processed by the application (such as XML files), hidden APIs that accepts URLs as input, and input that gets inserted into HTML tags.
2. <mark> Provide potentially vulnerable endpoints with Internal URLs </mark> , and based on the configuration of the network you might try different addresses before you find the one used in the network, but some of the common reserved IPs used in internal networks are `localhost`, `127.0.0.1`, `0.0.0.0`, `192.168.0.1`, and `10.0.0.1`.
3. <mark> Check the results </mark>
