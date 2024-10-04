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
3. <mark> Check the results </mark></br>
</t></t> 1. In regular SSRF, check if the server returns a reponse that reveals information such as a service banner or internal webpage. For example, if we were to send a request like this `user_id=1234&url=127.0.0.1:22` to the server, if the server responds with information about itself then it's prone to SSRF.</br> Example of how the reponse would look like: `Error: cannot upload image: SSH-2.0-OpenSSH_7.2p2 Ubuntu-4ubuntu2.4`</br>
</t></t> 2. In the case of Blind SSRF, the easiest way is to make the server sends a request to an external server that you control and check your server logs to monitor the request from the server.</br>
</t></t></t><strong> Possible ways to make this is to either use online hosting services (such as Godady or Hostinger) that allows you to host a website on a domin and then submit that domain in the SSRF testing payload or turn your own machine into a listener by using `Netcat` and then point your SSRF payload to your IP address along with the port number you are listening at and monitor any incoming traffic.</strong> </br>
>[!Note]
> Blind SSRF cannot be used to read internal files or access internal services, therefore, we need to confirm it's exploitability by trying to explore the internal network with SSRF.</br>

#### Exploring Internal Network with SSRF 
Make requests to different target ports (open and close) and monitor the behavior of the server, this helps the attackers determine whether or not they can access the internal network by using SSRF.</br>
**While monitoring the behavior of the server, focus on two things:** </br>
  - HTTP status codes in the reponse; if a port is open, the server returns status code of `200`</br>
  - Time difference between responses; if a port is closed the server usually responds faster</br>
# Bypassing SSRF Protections
## Bypassing Allowlists
Allowlists are stricter than blocklists by default, therefore, it is harder to bypass;</br>
**How to bypass allowlists?** </br>
Make use of Open Redirect vulnerability to redirect users to the url of the internal resource while requesting an allowedlist URL (refer to chapter 7 to know more about this vulnerability). </br>
<mark> Example: </mark> </br>
![image](https://github.com/user-attachments/assets/0fb076f7-4406-45a9-8a57-227cd9883655) </br>
We utilize an open redirect on `pics.example.com` to redirect the request to the IP address of the local host. </br>
Another scenario is when allowedlists are created using poorly designed regular expressions (regex).</br>
<mark> Example: </mark></br>
If you have an allowlist that accepts urls that has this pattern `.*example.com.*` , this regex can be easily bypassed since `.*` means that any number of characters, then a url like these two are allowed; `http://pics.example.com@127.0.0.1` (here `pics.example.com` is in the name portion of the url so the request  will be directed to 127.0.0.1) and `http://127.0.0.1/pic.example.com` is also accepted (here `pics.example.com` is in the directory portion of the url, so the request will be directed to `127.0.0.1` as well).

## Bypassing Blocklists
1. Fooling it with redirects -> you can make the server send requests to a server you control and then redirect the request to a blocklisted address by taking advantage of the Open Redirects vulnerability;</br>
You can trick the server into sending a request like this `https://public.example.com/proxy?url=https://attacker.com/ssrf` where the ssrf has a piece of code that redirects to the blocklisted IP of local address. This piece of code might look like something like this `<?php header("Location:http://127.0.0.0.1"); ?>`.</br>
2. Using IPv6 addresses -> sites that has prevention mechanisms against SSRF might be implemented against IPv4 addresses but not IPv6, so you can use the IPv6 address of the local host instead `::1` or the IPv6 of the first address of the network `fc00`.
3. Tricking the server with DNS -> you can trick the server to send a request to a blocklisted address by changing the DNS record of a non-blocked adress to make the hostnam maps to the IP address of the blocklisted address. For example we can make the server sends a request like this: ![image](https://github.com/user-attachments/assets/fff72368-672d-4546-9567-f4233c824ac9) </br>
where the `https://attacker.com` is mapped to the IP address of the localhost `127.0.0.1`.</br>
4. Switching out the Encoding -> To bypass blocklists you can use the blocklisted IPs encoded with different methods such as dword encoding, URL encoding, octal encoding .. etc, or a combination of all the methods. So if the URL parser can not process the different encoding methods appropriately, we can bypass it. Example of octal encoded version of the localhost `127.0.0.1` :</br>![image](https://github.com/user-attachments/assets/2d5fa378-a8c4-4812-a934-859c50292b0c) </br>

# Escalating the Attack
If we found an SSRF vulnerability, we can chain it with other with different bugs and have bigger impact.
>[!Note]
>What we can acheive with SSRF depends on the internal service found on the network. So we can use SSRF for different scenarios that are listed below.</br>

1. Perform network scanning</br>
</t></t> After finding an SSRF vulnerability that is exploitable, we can use this fact to scan the network for reachable hosts or open ports that helps us to construct futher attacks against the system. For example, we can use different IPv4 addresses of internal hosts such as `10.0.0.1` and mointor the server behavoir to see if any info is revealed. </br>
![image](https://github.com/user-attachments/assets/50f7d95d-7a4c-4896-bf34-2a3a1b70c64c) </br>
![image](https://github.com/user-attachments/assets/ab2a7899-20a9-4fb7-833d-8c4347d28935) </br>
This indicates that the host is reachable, since it reveals information about the service. Unlike this one below, that indicates the the host is not reachable.</br>
![image](https://github.com/user-attachments/assets/bc134912-91d9-436e-9753-b42eed67941a)</br>
![image](https://github.com/user-attachments/assets/77cd9837-673d-4b34-90c8-b0f00b3260c8)</br>
and same goes for port scanning.</br>


