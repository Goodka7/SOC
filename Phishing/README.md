# Phishing Mail Detected

In this project, I will walk through the analysis of a SOC Alert, outlining typical steps I take to analyze and close alerts. To keep the walkthrough concise and resource-efficient, I will not screenshot every playbook screen nor go over menu options. For a detailed playbook breakdown, refer to my other walkthroughs like [Broken-Access-Controls](https://github.com/Goodka7/SOC/blob/main/Broken-Access-Control/README.md) or [QR Phishing](https://github.com/Goodka7/SOC/blob/main/QRPhishing/README.md), as they cover most of the prompts used in this environment. You may also go to the [SOC overview](https://github.com/Goodka7/SOC/blob/main/README.md) for a more detailed understanding of the environment I am working with.

## Initial Alert
The first step is to accept ownership of the alert:
<div align="center">
<img src="https://github.com/user-attachments/assets/36f59bcb-5acc-46dc-b225-134577db6aa5" width="300">
</div>

After accepting ownership, I move to the Investigations Channel to copy the alert details into my notepad. This helps me quickly review collected data while navigating different screens.
<div align="center">
<img src="https://github.com/user-attachments/assets/6a7bc585-b4f0-4f82-bbcf-27c9c648ff70" width="300">
</div>

After clicking "create case," I open the playbook and begin analyzing the gathered information which I have copied to my notepad, as always.

Since this is a Phishing alert, I will use the information in the alert to look for the email address in question: `mark@letsdefend.io`
I will further narrow down the scope of my search by date: `Aug, 29, 2020` as well as source e-mail address: `info@nexoiberica.com`

I have found the email:
![image](https://github.com/user-attachments/assets/e844a420-8c79-41f5-b377-11389aea72c3)

Several pieces of data stick out about this: 
- First the sender URL is from a suspicious top level domain, especially when considering this is supposed to be related to UPS Express.
- Second the email is very poorly structured, it is all on one line and does not have a "sign off" by the individual sending, which makes it seem very unprofessional.
- Next the attachment is untitled but when hovering over the attachment we see the name of the file is: 21b3a9b03027779dc3070481a468b211.zip. This sort of attachment doesn't fall in line with the context of the email.

Running the name of the zip file through threat intelligence we get this:

![image](https://github.com/user-attachments/assets/b0e629da-ea76-45e8-8072-c74ae62c5956)

`any.run` shows it as a known malicious file related to the `Emotet trojan`.

At this point I believe it's safe to assume this is a true-positive and malicious event, I go ahead and first delete the relevant e-mail from the users inbox: 

<div align="center">
<img src="https://github.com/user-attachments/assets/b13c0e8f-6de4-4a49-b0ee-1f79ada1ca11" width="300">
</div>


I then check the Network Traffic for the SMTP (`63.35.133.186`) traffic relating to this so we can get the IP for the EndPoint that could be infected:
![image](https://github.com/user-attachments/assets/ea5f4057-5803-4cbe-bb93-93ed7c519fa3)

Here I see one connection the date related to the SMTP server, but it does not point to `mark@letsdefend.io`, showing that he never opened the file.

At this point I am free to close the case and write my report.

<div align="center">
<img src="https://github.com/user-attachments/assets/4e39ef57-bfaf-4d99-926b-bbed509ab45c" width="500"><br>
<img src="https://github.com/user-attachments/assets/ea8b98d2-c88c-4b68-a37a-02d9c7015ee9" width="500">
</div>

