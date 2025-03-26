# Malicious Chrome Extension

In this project, I will walk through the analysis of a SOC Alert, outlining typical steps I take to analyze and close alerts. To keep the walkthrough concise and resource-efficient, I will not screenshot every playbook screen nor going over menu options. For a detailed playbook breakdown, refer to my other walkthroughs like [Broken-Access-Controls](https://github.com/Goodka7/SOC/blob/main/Broken-Access-Control/README.md) or [QR Phishing](https://github.com/Goodka7/SOC/blob/main/QRPhishing/README.md), as they cover most of the prompts used in this environment, you may also go to the [SOC overview](https://github.com/Goodka7/SOC/blob/main/README.md) if you would like a more detailed understanding of the environment I am working with.

As always the first step is to accept the ownership of the Alert:
![image](https://github.com/user-attachments/assets/c3e46af9-e809-41a5-b94c-2855e41f2913)

After that I move to the Investigations Channel so I can copy the details of the alert to my notepad as this allows me to quickly review the data I have collected so far as I am going through different screens.

![image](https://github.com/user-attachments/assets/a05ea02e-a376-42e7-bf91-2e7c75e8a8a7)

After clicking "create case" I open up the playbook and begin analyzing the information I have so far.
Since we already have a file hash and a file name, I am going to check OSINT to get a better picture of what I may be looking at/for.

VirusTotal came back with some negative community scores based on the hash, after searching through google I was able to piece together a few things about this exploit:

```
At a high level, the malicious Chrome extension "FakeGPT" is disguised as a legitimate ChatGPT integration. It hijacks Facebook session cookies right after installation by leveraging the Chrome extension API to access cookies related to Facebook domains.

The extension encrypts the stolen cookies using AES with the key "chatgpt4google" and sends them to a Command and Control (C2) server via an HTTP GET request. The attackers use a custom HTTP header (X-Cached-Key) to conceal the encrypted data and bypass security measures like DPI (Deep Packet Inspection).

The extension propagates through sponsored Google search results that lead users to download the fake extension from the Chrome Web Store. After installation, the attacker can hijack Facebook accounts and use them for various malicious activities, including spreading propaganda and creating bot accounts.
```

