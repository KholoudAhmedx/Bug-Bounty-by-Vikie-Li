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
