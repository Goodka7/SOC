# Brute Force Attack Walkthrough

In this project, I will walk through the analysis of a SOC alert, outlining typical steps I take to analyze and close alerts. The goal is to keep it straightforward and easy to understand, even for non-technical readers.

## Initial Alert
The first step in the playbook is to find out more about the alert. This one says **"Forced Authentication Detected"**, which typically indicates brute force activity or repeated login attempts.

![image](https://github.com/user-attachments/assets/02058a76-8b7d-42ac-833d-24194a02fd63)
![image](https://github.com/user-attachments/assets/85a15c88-c3c8-4a65-b8e4-7e515d791cc9)
![image](https://github.com/user-attachments/assets/16cb5968-abb5-4f77-807a-f971bc3e1dbd)
![image](https://github.com/user-attachments/assets/9b2caba5-468f-423a-8e5b-7ac757e42fee)

The alert details give me some key information:
- **Source IP:** 120.48.36.175
- **Destination IP:** 104.26.15.61

Next, I move to the **Endpoint Security** tab to investigate the endpoint(s) that triggered the alert.

## Endpoint Analysis
There was only one endpoint that got a hit related to the destination IP (`104.26.15.61`). However, the endpoint itself didn't actually have that IP assigned, which means I had to dig deeper to understand why it showed up.

![image](https://github.com/user-attachments/assets/29324201-835d-4bcf-a208-0a1a9fd4dd19)

After checking some traffic logs, I noticed that there was some traffic related to the IP, but it wasn’t from the relevant event date (**Dec 12, 2023, 02:15 PM**). To dig deeper, I ran both IP addresses through **VirusTotal**.

## VirusTotal Results
- **104.26.15.61 (CloudflareNet):** Clean, associated with **AS 13335 (CLOUDFLARENET)**.
- **120.48.36.175 (Beijing Baidu Netcom):** Flagged as malicious by multiple vendors (e.g., BitDefender, G-Data), associated with phishing activities.

![image](https://github.com/user-attachments/assets/65212aef-deee-462b-a5d0-614538989712)
![image](https://github.com/user-attachments/assets/be9b8dd6-06e8-4edc-b22a-71f549f8ca47)

## Discovery of Malicious Activity
Checking network traffic logs revealed that the source IP (`120.48.36.175`) was **enumerating ports** on the destination IP (`104.26.15.61`). Further analysis showed a **brute force attack on port 80**, and unfortunately, it was successful — the attacker managed to log in.

![image](https://github.com/user-attachments/assets/3dc78112-0436-43a4-b25c-ae754117d2a4)
![image](https://github.com/user-attachments/assets/4891af6e-fd18-46f5-973c-7ad41feb88ff)
![image](https://github.com/user-attachments/assets/0656ff9b-897b-4e85-b904-53a24e6f5208)

## Proxy Server Complication
It turns out that **104.26.15.61** is a **proxy server (Cloudflare)**, which masks the IP of the actual endpoint where the web server is hosted. Because of this setup, I couldn't directly quarantine the device, so I will need to escalate the issue.

## Case Closure
At this point, I had gathered enough information to close and escalate the alert. I submitted my findings and the report, then marked the case as closed.

![image](https://github.com/user-attachments/assets/34f14b2b-da2c-48c5-9df3-e3e91ac568e4)
![image](https://github.com/user-attachments/assets/a0e43229-2a31-4c7b-b902-1032b3997d95)
![image](https://github.com/user-attachments/assets/daaa86f5-212d-43b5-9885-dbcf67f91f94)

## Final Thoughts
Proxy servers like Cloudflare can make it difficult to trace the origin of attacks, which is why it's crucial to understand how proxy traffic works when analyzing incidents like this. Proper investigation and thorough documentation are key to handling such cases.

