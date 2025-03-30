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
