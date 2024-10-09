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
 
