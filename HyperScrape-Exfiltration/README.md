# HyperScrape Data Exfiltration Tool

In this project, I will walk through the analysis of a SOC Alert, outlining typical steps I take to analyze and close alerts. To keep the walkthrough concise and resource-efficient, I will not screenshot every playbook screen nor go over menu options. For a detailed playbook breakdown, refer to my other walkthroughs like [Broken-Access-Controls](https://github.com/Goodka7/SOC/blob/main/Broken-Access-Control/README.md) or [QR Phishing](https://github.com/Goodka7/SOC/blob/main/QRPhishing/README.md), as they cover most of the prompts used in this environment. You may also go to the [SOC overview](https://github.com/Goodka7/SOC/blob/main/README.md) for a more detailed understanding of the environment I am working with.

## Initial Alert
The first step is to accept ownership of the alert:
<div align="center">
<img src="https://github.com/user-attachments/assets/34d4d103-0994-4f93-836e-b099f99eb406" width="500">
</div>

After accepting ownership, I move to the Investigations Channel to copy the alert details into my notepad. This helps me quickly review collected data while navigating different screens.

![image](https://github.com/user-attachments/assets/69feafb2-0a71-4584-a402-370e297c2c5d)

After clicking "create case," I open the playbook and begin analyzing the gathered information which I have copied to my notepad, as always.

## Network Traffic Analysis
Next, I navigate to the Network Traffic Logs in the "Log Management" tab to investigate `172.16.17.72`.

Notable traffic included the following images:

<div align="center">
<img src="https://github.com/user-attachments/assets/4152b1cc-0dba-4857-b7ed-a904957cc78f" width="500"><br>
<img src="https://github.com/user-attachments/assets/9b0aa217-3a51-4776-86ca-f604effbd179" width="500"><br>
<img src="https://github.com/user-attachments/assets/326c975d-3338-4d77-9359-ebe9b79b5861" width="500">
</div>

This gives me verification of a few pieces of information: Host: `Arthur`, `172.16.17.72` and further gives me: `arthur@letsdefend.io`

I move to the Email Security tab and check for any emails from the address given above, but I find no results.

Next, I move to the EndPoint Security tab to see what other information I can find.

Notable data on the EndPoint for `Arthur`, `172.16.17.72`:

![image](https://github.com/user-attachments/assets/048de4f7-99ce-45ba-a71f-c6c529700f94)

Here I have found the suspected process, it has the following hash value: `cd2ba296828660ecd07a36e8931b851dda0802069ed926b3161745aae9aa6daa` which comes back as highly malicious on [VirusTotal](www.virustotal.com).

![image](https://github.com/user-attachments/assets/0c126aa2-d0ad-4824-8f42-c4fc65476e62)

Under the behavoir tab on VirusTotal, we get a little more information about this download:

![image](https://github.com/user-attachments/assets/4ed59e20-5437-4e5a-9f83-ad2b6feed67f)

After viewing the network connections on the EndPoint, I found several IPs flagged as malicious:

![image](https://github.com/user-attachments/assets/69b0955a-0e89-4a58-b2af-7f7ba91b2937)

```

136.243.108.14
173.209.51.54
185.125.190.39
3.129.149.36
34.243.160.129
49.12.80.40
52.15.107.13
54.247.62.1
91.189.91.46
91.189.91.48

```
At this point I have enough information based on the Alert and Data that I have found on the EndPoint and Network Logs.
I check to make sure no other systems have been infected by this process, and isolate the machine.

## Analysis Summary

- **Tool Identified**: HyperScrape (APT35)
- **Initial Vector**: Likely malicious download via browser (`Explorer.EXE` parent)
- **Execution**: `EmailDownloader.exe` executed manually from Downloads folder
- **Hash**: cd2ba296828660ecd07a36e8931b851dda0802069ed926b3161745aae9aa6daa (malicious on VirusTotal)
- **Exfiltration Activity**: Confirmed email downloads from `arthur@letsdefend.io` via OWA
- **Malicious IPs Contacted**: 
  - 136.243.108.14
  - 185.125.190.39
  - 49.12.80.40
  - etc.
- **Detection Rule**: SOC250 - APT35 HyperScrape Data Exfiltration Tool Detected

## Conclusion

On December 27, 2023, an alert (SOC250) was triggered for the detection of the APT35-associated HyperScrape data exfiltration tool on host **Arthur (172.16.17.72)**. The tool `EmailDownloader.exe` was executed by user `arthur@letsdefend.io`, resulting in the unauthorized download of mailbox contents via Outlook Web Access (OWA). The file hash was confirmed as malicious via VirusTotal.

Network traffic analysis revealed outbound connections to known APT35 command-and-control IPs, including **136.243.108.14** and **173.209.51.54**, both previously attributed to HyperScrape infrastructure. The activity represents external reconnaissance and data exfiltration using stolen credentials.

Immediate response actions should include isolating the host, revoking user credentials, blocking all related IPs, and conducting full endpoint and mailbox forensics.

At this point, I write my report for the Tier 2 escalation of a `True Positive` and close the alert.

