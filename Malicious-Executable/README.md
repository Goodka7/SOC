# Malicious Chrome Extension

In this project, I will walk through the analysis of a SOC Alert, outlining typical steps I take to analyze and close alerts. To keep the walkthrough concise and resource-efficient, I will not screenshot every playbook screen nor go over menu options. For a detailed playbook breakdown, refer to my other walkthroughs like [Broken-Access-Controls](https://github.com/Goodka7/SOC/blob/main/Broken-Access-Control/README.md) or [QR Phishing](https://github.com/Goodka7/SOC/blob/main/QRPhishing/README.md), as they cover most of the prompts used in this environment. You may also go to the [SOC overview](https://github.com/Goodka7/SOC/blob/main/README.md) for a more detailed understanding of the environment I am working with.

## Initial Alert
The first step is to accept ownership of the alert:
![image](https://github.com/user-attachments/assets/c3e46af9-e809-41a5-b94c-2855e41f2913)

After accepting ownership, I move to the Investigations Channel to copy the alert details into my notepad. This helps me quickly review collected data while navigating different screens.

![image](https://github.com/user-attachments/assets/a05ea02e-a376-42e7-bf91-2e7c75e8a8a7)

After clicking "create case," I open the playbook and begin analyzing the gathered information which I have copied to my notepad, as always.

## OSINT Analysis
Since we already have a file hash and file name, I use OSINT to understand what we may be dealing with. VirusTotal returned negative community scores for the hash. After further research, I identified the following characteristics:

```
At a high level, the malicious Chrome extension "FakeGPT" masquerades as a legitimate ChatGPT integration. It hijacks Facebook session cookies immediately upon installation by using the Chrome extension API to access cookies related to Facebook domains.

The extension encrypts stolen cookies with AES using the key "chatgpt4google" and sends them to a Command and Control (C2) server via an HTTP GET request. The attackers use a custom HTTP header (X-Cached-Key) to disguise the encrypted data and evade DPI (Deep Packet Inspection).

The extension spreads through sponsored Google search results that trick users into downloading it from the Chrome Web Store. After installation, attackers can hijack Facebook accounts for malicious activities, including creating bots and promoting propaganda.
```

This means we will be looking for unknown or unexpected outgoing traffic from the endpoint.

## Network Traffic Analysis
Next, I navigate to the Network Traffic Logs in the "Log Management" tab to investigate `172.16.17.173`.

Notable traffic includes the following URLs flagged as malicious by VirusTotal:

![image](https://github.com/user-attachments/assets/dbb354b8-a9db-4d7c-8fe7-7adb6ee3f95a)
![image](https://github.com/user-attachments/assets/5c7817a9-3566-4648-84a1-205d31d57660)

The URLs were flagged by:
- **Forcepoint ThreatSeeker:** Information Technology
- **Sophos:** Spyware and Malware
- **Webroot:** Phishing and Other Frauds

Since the network logs did not yield further critical insights, I pivot to the **Endpoint Security** tab to inspect the endpoint "Samuel" (IP: `172.16.17.173`).

![image](https://github.com/user-attachments/assets/48cf2646-3c2a-479b-885c-1bcbdce50a83)

## Endpoint Analysis
![image](https://github.com/user-attachments/assets/1faf780f-cad4-4212-96cb-21e7865f85c0)
I check the browser history and observe the installation of the Chrome extension:

![image](https://github.com/user-attachments/assets/b0232f46-720a-4b9d-aa64-892289338b82)

The network traffic on the endpoint also confirms communication with both malicious IP addresses, indicating potential C2 communication.

![image](https://github.com/user-attachments/assets/066b4802-d52d-412c-98b0-944b852b5015)

The process history shows the following execution command:

```
"C:\Program Files\Google\Chrome\Application\chrome.exe" --single-argument C:\Users\LetsDefend\Desktop\hacfaophiklaeolhnmckojjjjbnappen.crx
```

## Conclusion
Based on the evidence gathered, the malware was successfully installed and connected to a C2 server without being automatically quarantined. The endpoint was contained (quarantined), and the case was escalated to Tier 2 as the malware remains present.

![image](https://github.com/user-attachments/assets/337895e3-3350-4746-9c2d-025b117cb653)
![image](https://github.com/user-attachments/assets/3c0555a3-0237-4a8b-a1b8-48835c78a367)


