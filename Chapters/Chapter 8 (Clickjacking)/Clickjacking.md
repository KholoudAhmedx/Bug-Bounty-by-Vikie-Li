# Clickjacking 
clickjacking is also called User-interace Redressing.</br>
Clickjacking is a vulerability that occurs when the attacker tricks the user into clicking a malicious button that looks legitimate.</br>

# Mechanism
Attacker uses HTML page-overlay techniques to hide one page over another.</br> How?</br>
Attackers use `<iframe>` tag to embed one page within another by specifiying the URL to frame's `src` attribute. 
>[!NOTE]
>If the iframe is blank, then website specified in the URL src attribute cannot be framed (embeded).
>If not blank, then it can be framed. (Depends on the security of the targeted website)

### Iframe benefits
1. Can be used to embed premade ads into your social media pages or blogs
2. Can be used to embed external resources like videos and audio

### Example Scenario
Imaginge the user has his banking account session open and the bank website has a page called `transfer_money` that requires the recipient account ID and the amount of money to transfer, to transfer money via a URL like this `www.example.com/transfer_money?recpid=123422 & amount=4000`.</br>![image](https://github.com/user-attachments/assets/ef1a761a-4347-49d1-8ee4-b4f8c45ae6b7) </br>
 The attacker creates a malicious webpage that for example asks the user to enter their email to subscribe for cybersecurity news and then clicks one button to sumbit their emails.</br>![image](https://github.com/user-attachments/assets/481fcdf8-011a-4619-80e9-078f292add39)</br>
The attacker then tricks the user to transfer money from the victim's banking account to the attacker account by taking advantage of clickjacking.</br>
1. Attacker adds an `iframe` tag into the malicious website that has the following url `https://www.example.com/transfer_money?recpid=attacker's account id & amount=5000`
2. Attacker overlay this page over the banking account page by changing the malicious website's CSS style to prevent it from being noticable to the victim (search for how iframe embeds pages, and you will understand why it is important to change css properties)
3. When the user clicks on submit email button (malicious button that looks legitimate), he's actually clicking on the transfer money in the background.
>[!Note]
>Finding an easy-to-prevent vulnerability on a critical functionality like clickjacking on a balance transfer page is an indication that this webpage doesn't follow the best practices of secure development, so this webapge is likely to contain another vulnerabilities that you should check for.

# Prevention
**For a clickjacking vulnerability to happen two conditions must be met:**
1. The target website allows itself to be framed in an `iframe` tag
2. The target website has the functionality to execute actions on the user's behalf like changing account settings or personal data (refer to the example above)

**Defensive methods**
1. `X-Frame-Options : Deny` or `X-Frame-Options:SAMEORIGIN` headers are one of the ways to prevent clickjacking by including one of these option headers in the HTTP response to indicate that the page's content cannot be rendered in an `iframe` tag; (The site should serve one of these options on all pages that contain state-changing actions)  
2.  `Content-Secuirty-Policy` response header's directive `frame ancestors` can also be used to prevent against clickjacking.</br>
</t></t>1. if `frame ancestors` is set to `none` -> will prevent any site from framing the page; `Content-Security-Policy: frame-ancestors 'none;'`;</br>
</t></t>2. if `frame ancestors` is set to `self` -> will allow sites from the same origin to frame the page; `Content-Security-Policy: frame-ancestors 'self';`;</br>
</t></t>3. if `frame ancestors` is set to a specific origin  -> will allow that origin to frame the content; `Content-Security-Policy: frame-ancestors 'self' *.example.com;`; (this will allow all subdomains from `example.com` to frame the content</br>
3. `Set-Cookie` response header's `SameSite`; Not only `Set-Cookies` is used to authenticate users by providing the cookie-name=cookie_value designation, it can also be used to prevent clickjacking attacks by setting `SameSite` value either to `Lax` or `Strict`. That prevents cookies from being sent in requests made within a third-party iframe so any clickjacking attack that requires the victim to be authenticated, wouldn't even work.</br>
4. Unreliable way: frame-busting, uses Javascript code to check if a page is in iframe, and if it's framed by a trusted site. 
***Example***</br>
</t></t>1. `Set-Cookie: PHPSESSID=UEhQU0VTU0lE; Max-Age=86400; Secure; HttpOnly; SameSite=Strict` </br>
</t></t>2. `Set-Cookie: PHPSESSID=UEhQU0VTU0lE; Max-Age=86400; Secure; HttpOnly; SameSite=Lax`</br>
# Hunting For Clickjacking
To hunt for clickjacking vulnerabilities, look for pages that:
1. Contains sensitive state-changing actions
2. Can be framed

**Steps**:
1. Look for pages that contains state-changing actions, otherwise, the attacker cannot cause damage to the user's account;</br>
</t></t> 1. Go through all the functionalities of the application, click all links, write down all actions along with the URL of the pages they are hosted on;; Example of state-changing request`Change password: bank.example.com/password_change`</br>
</t></t> 2. Checks if the action can be acheived by clicks only not via keyboard as well
2. Intercept the traffic using proxy and checks if the pages hosting the state-changing functionalities has `X-Frame-Options` or `Content-Security-Policy` response headers, if not, then the webpage is vulnerable to clickjacking; Also check for `SameSite` flag in the Set-Cookie header if the webpage requires user authenticatioin.
3. Check if the page if frameable or not by creating a simple template with `iframe` tag that specifies the URL of the taget in the iframe's `src` attribute
4. Confirm the vulnerability
# Bypassing Protection
If the website doesn't implement the complete clickjacking protections, you might bypass the protections; </br>
One of the methods is finding a loophole in the frame-busting code. Look for features that allows you to embed custom iframes, if you found one, you can bypass clickjacking protection by using the **double iframe** trick.</br>  ![image](https://github.com/user-attachments/assets/b5ba308e-0db6-43c4-8bad-e59787116360)</br>



