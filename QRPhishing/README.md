# SOC Phishing Alert Investigation Walkthrough

## Introduction
Today, I will be going over a phishing alert in the SOC environment. This walkthrough details how I investigated the alert, followed the playbook, and concluded the findings.

![image](https://github.com/user-attachments/assets/65a1632b-0a59-431c-8278-64f10a4f1671)
<div align="center">
<img src="https://github.com/user-attachments/assets/3e95e234-bc52-4435-841c-41f220d09c9d" width="300"><br>
<img src="https://github.com/user-attachments/assets/ba03657b-e193-45b7-96f1-9f862d86323e" width="500">
</div>

---

## Case Creation and Playbook Access
After accepting ownership for the alert, I moved to the **Investigation Channel** and created a case.

![image](https://github.com/user-attachments/assets/6687666e-c716-450a-9ed5-82342ff20bac)
<div align="center">
<img src="https://github.com/user-attachments/assets/e4a06d97-1e15-4e48-9da6-9b6c11ac6637" width="500" style="display: block; margin: auto;">
</div>

This allowed me to access the playbook. I copied all the details from the Investigation Channel into a **notepad** to aggregate the data I would find during analysis in one area.
<div align="center">
<img src="https://github.com/user-attachments/assets/6c5ecec0-dd5b-413a-b525-1df28aa59a4b" width="500">
</div>

The playbook issued a warning to **verify the cause of the alert** before escalating to Tier 2 Support.

<div align="center">
<img src="https://github.com/user-attachments/assets/57045edf-7328-49ca-b074-19ff43010b7c" width="500"><br>
<img src="https://github.com/user-attachments/assets/59a6d746-08ab-40c6-ac09-8cb2ec79bc50" width="500">
</div>

---

## Log Management and Analysis
The playbook directed me to the **Log Management** tab to search for relevant logs. Entering:
```
SMTP Address: 158.69.201.47
```
I was able to locate one log that confirmed the information given in the alert.

![image](https://github.com/user-attachments/assets/42ace2e9-6e6a-4e5d-af97-36d496adcb6b)
<div align="center">
<img src="https://github.com/user-attachments/assets/ffebd96b-fb54-43de-be61-9c7bb765e597" width="500">
</div>

---

## Phishing Indicators and IP Analysis
The email showed typical phishing characteristics:
- **Urgency and fear tactics** about MFA updates.
- **Suspicious domain** (`microsecmfa.com`) not matching official Microsoft domains.
- **QR code inclusion** likely leading to a malicious site.

The IP (`158.69.201.47`) was flagged as **malicious** by VirusTotal:
- **Behaviors:** HTTP Bruteforce / HTTP Crawl / HTTP DoS + 3 more.

## Investigating EndPoints and Email

![image](https://github.com/user-attachments/assets/69c8f71a-dc24-4644-829a-9495fe0f531c)
![image](https://github.com/user-attachments/assets/1b2d95e9-7ba8-415a-9141-da5523f96eab)
![image](https://github.com/user-attachments/assets/4a28e31f-1419-4e24-96e2-ef3e2b0a9428)

### Suspicious IP Findings
<div align="center">
  
| IP Address      | ASN     | Provider               | Country | Detection | Reason for Suspicion                                      |
| ---------------| ------- | ----------------------- | -------| ---------| ----------------------------------------------------------|
| 3.129.149.36    | AS16509 | AMAZON-02               | US     | 1/94     | Flagged as Malicious by Criminal IP.                        |
| 185.125.190.39  | AS41231 | Canonical Group Limited | GB     | 1/94     | Flagged as Malicious (Ubuntu-related).                      |
| 91.189.91.48    | AS41231 | Canonical Group Limited | US     | 1/94     | Flagged as Malicious (Ubuntu-related).                      |
| 52.15.107.13    | AS16509 | AMAZON-02               | US     | 1/94     | Flagged as Malicious by Criminal IP.                        |
| 54.247.62.1     | AS16509 | AMAZON-02               | IE     | 1/94     | Flagged as Malicious by Criminal IP.                        |

<img src="https://github.com/user-attachments/assets/160af96f-1b37-4f44-9fb5-86c2b0f6e531" width="500">
</div>

---

## Incident Assessment and Conclusion
Based on the email and endpoint activity, the most likely attack method was **"Phishing for Information"**. I concluded that the **attacker was external**, as indicated by the IP analysis.

<div align="center">
<img src="https://github.com/user-attachments/assets/8711289f-20ac-478a-bde9-f1eaed8538bc" width="500"><br>
<img src="https://github.com/user-attachments/assets/9347e285-f5fb-4c56-95c3-ded0e47a6abe" width="500">
</div>

Further investigation revealed no **internal IP involvement**, suggesting an **isolated incident**. However, I found two additional endpoints with similar suspicious traffic dating as far back as **November 2023**, warranting a broader investigation.

![image](https://github.com/user-attachments/assets/74a7c004-856c-4253-a74d-dbe02620f7e0)
![image](https://github.com/user-attachments/assets/fac11219-7e7b-43ee-bd69-6a0d407ccb60)

---

## Closing the Case
After completing the analysis and documenting findings, I finished the playbook and closed the case.

![image](https://github.com/user-attachments/assets/991b6db7-2732-430c-ab08-7577cfc18a10)
![image](https://github.com/user-attachments/assets/c09a4837-9efc-4a91-9cdc-2e234d5b1c31)

---

## Final Thoughts
This incident highlighted the importance of quickly detecting and analyzing phishing attempts, particularly those leveraging **QR codes and fake MFA notices**. Timely analysis helped isolate the threat and identify additional suspicious endpoints requiring further investigation.
