# Suspicious hh.exe Usage

In this project, I will walk through the analysis of a SOC Alert, outlining typical steps I take to analyze and close alerts. To keep the walkthrough concise and resource-efficient, I will not screenshot every playbook screen nor go over menu options. For a detailed playbook breakdown, refer to my other walkthroughs like [Broken-Access-Controls](https://github.com/Goodka7/SOC/blob/main/Broken-Access-Control/README.md) or [QR Phishing](https://github.com/Goodka7/SOC/blob/main/QRPhishing/README.md), as they cover most of the prompts used in this environment. You may also go to the [SOC overview](https://github.com/Goodka7/SOC/blob/main/README.md) for a more detailed understanding of the environment I am working with. 

**Please note that in an effort to reduce resource usage for Github, I have reused images from other walkthroughs, except where the information is specific/critical to this case.**

## Initial Alert
The first step is to accept ownership of the alert:
<div align="center">
<img src="https://github.com/user-attachments/assets/36f59bcb-5acc-46dc-b225-134577db6aa5" width="500">
</div>

After accepting ownership, I move to the Investigations Channel to copy the alert details into my notepad. This helps me quickly review collected data while navigating different screens.
<div align="center">
<img src="https://github.com/user-attachments/assets/6a7bc585-b4f0-4f82-bbcf-27c9c648ff70" width="500">
</div>

After clicking "create case," I open the playbook and begin analyzing the gathered information which I have copied to my notepad, as always.

Since this case is addressing a suspicious process on a Windows Machine, I go to EndPoint Security to look at the endpoint: `BillPRD`

![image](https://github.com/user-attachments/assets/9224b0bc-8bdf-4a98-a5fa-0ba28432f991)

The image above shows that `Bill01` executed the command `hh.exe c:/program files/winrar/winrar.chm` on `2021-JAN-31 16:43 UTC`

This opens the help file in WinRAR program, which is normally a legitimate process, however, someone could have tampered with the `.chm`, furthermore the fact that someone called it from a script seems very suspicious.

This seems to be the case as our SIEM has alerted us to suspicious usage, the alert gives us the name of the file as `winrar.chm` and the hash `07694464c25bac4ecdb365e928ffe1ff` which comes back clean in VirusTotal.

![image](https://github.com/user-attachments/assets/183ae3a8-63a3-43e3-b49d-190fafd8a082)

![image](https://github.com/user-attachments/assets/e07673f4-dad3-42fc-a928-7df2c0194ff6)

Looking at network traffic in the Log Management tab, the IP from `BillPRD` connected to this URL `67.199.248.10`, on `2021-JAN-02 16:33 UTC`, which redirected him to a "Rick Roll" youtube page, however the `67.199.248.10` has been flagged as malicious on Any.Run

![image](https://github.com/user-attachments/assets/b983b5a9-01fd-4747-913a-34ddfc4f9b6d)

The endpoint further shows that it connected to this SAME "Rick Roll" youtube video on `2021-JAN-31 16:33 UTC`

![image](https://github.com/user-attachments/assets/b99d0b64-1b9d-4ff1-b577-c7d71cf01828)

This process was also found running on the EndPoint, which is a malcicious process that had been initiated at a much earlier date of `2020-SEP-20 22:51 UTC`

![image](https://github.com/user-attachments/assets/ab3c09dd-a8d3-4ccc-9ae2-19a37b963e32)


Based on my observations:

Date (UTC)	Time	Event	Details
2020-09-20	22:51	Initial File Execution	User Bill01 opened Krankheitsmeldung_092020_07.xlsm from desktop. File is macro-enabled and likely dropped or staged follow-up components. File MD5: 14970ce0a3d03c46a4180db69866d0d1.
2021-01-02	16:33	First Rickroll Redirect	Host BillPRD (172.16.17.47) made an outbound HTTPS connection to malicious IP 67.199.248.10. Connection redirected to a YouTube Rick Astley video, likely used as a decoy. Suggests the malware was already resident and possibly beaconing or receiving staged commands.
2021-01-31	16:33	Second Rickroll Redirect	Same host again contacted 67.199.248.10, resulting in the same Rick Astley redirect. Indicates persistent callback behavior from the infected system.
2021-01-31	16:43	Suspicious CHM Execution	hh.exe executed with argument c:/program files/winrar/winrar.chm under user Bill01. This CHM file is typically benign, but execution via script is suspicious. File hash: 07694464c25bac4ecdb365e928ffe1ff, size: 306 KB.
2021-01-31	16:59	SOC Alert Triggered	Rule SOC113 - Suspicious hh.exe Usage triggered for the execution of winrar.chm. File identified as malicious. EventID: 44, source IP: 172.16.17.47 (BillPRD). Device action: Allowed. File available for download as 07694464c25bac4ecdb365e928ffe1ff.zip (password: infected).

At this point I have enough data to conclude my playbook and case, I isolate the machine and make my report to tier 2.

<div align="center">
<img src="https://github.com/user-attachments/assets/4e39ef57-bfaf-4d99-926b-bbed509ab45c" width="500"><br>
<img src="https://github.com/user-attachments/assets/ea8b98d2-c88c-4b68-a37a-02d9c7015ee9" width="500">
</div>
