Today I will be going over a Phishing alert in the SOC enviroment.
![image](https://github.com/user-attachments/assets/65a1632b-0a59-431c-8278-64f10a4f1671)
![image](https://github.com/user-attachments/assets/3e95e234-bc52-4435-841c-41f220d09c9d)
![image](https://github.com/user-attachments/assets/ba03657b-e193-45b7-96f1-9f862d86323e)


After accepting ownership for the alert, I move to the Investigation Channel and create a case.
![image](https://github.com/user-attachments/assets/6687666e-c716-450a-9ed5-82342ff20bac)
![image](https://github.com/user-attachments/assets/e4a06d97-1e15-4e48-9da6-9b6c11ac6637)

This allows me to access the playbook.
As always I copy all the details from the Investigation Channel into a notepad so I can aggregate all the data I will find during analysis into one area.

![image](https://github.com/user-attachments/assets/6c5ecec0-dd5b-413a-b525-1df28aa59a4b)

The playbook gives us a little warning about verifying the cause of the alert before escalating it to Tier 2 Support.

![image](https://github.com/user-attachments/assets/57045edf-7328-49ca-b074-19ff43010b7c)
![image](https://github.com/user-attachments/assets/59a6d746-08ab-40c6-ac09-8cb2ec79bc50)

The playbook suggests moving to the Log Managment tab to look for relavent logs, entering: SMTP Address :
158.69.201.47, I am able to see one log. 
This log does not really give us any new information but does confirm the information given in the alert.

![image](https://github.com/user-attachments/assets/42ace2e9-6e6a-4e5d-af97-36d496adcb6b)
![image](https://github.com/user-attachments/assets/ffebd96b-fb54-43de-be61-9c7bb765e597)

![image](https://github.com/user-attachments/assets/add32fa7-ead6-4f7f-b306-e2d96da3f531)

The playbook directs me to make sure I look at these sources for suspicious data:

The email uses urgency and fear tactics, stating that failure to update MFA will result in loss of email privileges.

The sender's domain microsecmfa.com appears suspicious and does not match official Microsoft domains.

The email encourages scanning a QR code, which could lead to a malicious site or credential-stealing process.

SMTP Address: 158.69.201.47 has been flagged as malicious by VirusTotal(https://www.virustotal.com/gui/ip-address/158.69.201.47)
    Behaviors: HTTP Bruteforce / HTTP Crawl / HTTP DoS + 3 more. Full details on CrowdSec CTI

![image](https://github.com/user-attachments/assets/69c8f71a-dc24-4644-829a-9495fe0f531c)
![image](https://github.com/user-attachments/assets/1b2d95e9-7ba8-415a-9141-da5523f96eab)
![image](https://github.com/user-attachments/assets/4a28e31f-1419-4e24-96e2-ef3e2b0a9428)

After checking the Email and the EndPoint(s), I found the following suspicious activity:

IP Address	ASN	Provider	Country	Detection	Reason for Suspicion
3.129.149.36	AS16509	AMAZON-02	US	1/94	Flagged as Malicious by Criminal IP.
185.125.190.39	AS41231	Canonical Group Limited	GB	1/94	Flagged as Malicious by Criminal IP (Ubuntu-related).
91.189.91.48	AS41231	Canonical Group Limited	US	1/94	Flagged as Malicious by Criminal IP (Ubuntu-related).
52.15.107.13	AS16509	AMAZON-02	US	1/94	Flagged as Malicious by Criminal IP.
54.247.62.1	AS16509	AMAZON-02	IE	1/94	Flagged as Malicious by Criminal IP.

![image](https://github.com/user-attachments/assets/160af96f-1b37-4f44-9fb5-86c2b0f6e531)

Based on the E-mail and the activity on the EndPoint, I concluded that "Phishing for Information" is the most likely.

![image](https://github.com/user-attachments/assets/8711289f-20ac-478a-bde9-f1eaed8538bc)

I already know it's external based on the data.

![image](https://github.com/user-attachments/assets/8a9212ad-d8ed-4b97-be4f-ad6b7e4d5e3f)

I have already performed external analysis on the IPs and found data regarding them.

![image](https://github.com/user-attachments/assets/a0681412-6b36-43e1-bde4-10c5d39f874a)

No local (in network) IPs were listed in the Network Logs so I am assuming this is an isolated incident.
Note: I did find 2 more Endpoints with similiar traffic that is going to warrant a broader investigation, the other EndPoints show traffic as early as Nov 2023.

![image](https://github.com/user-attachments/assets/74a7c004-856c-4253-a74d-dbe02620f7e0)
![image](https://github.com/user-attachments/assets/fac11219-7e7b-43ee-bd69-6a0d407ccb60)

I finish the playbook with some quick notes.

![image](https://github.com/user-attachments/assets/991b6db7-2732-430c-ab08-7577cfc18a10)

And then I close the case out.
![image](https://github.com/user-attachments/assets/c09a4837-9efc-4a91-9cdc-2e234d5b1c31)


