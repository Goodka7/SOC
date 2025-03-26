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

This means we will be looking for some unknown or unexpected outgoing traffic on our endpoint.

Now I will navigate to the Network traffic logs in the "Log Management" tab to look up `172.16.17.173`.

In the logs we see some noteable traffic, all of it is relevant and related but I am going to share the two most useful pieces of traffic:
![image](https://github.com/user-attachments/assets/dbb354b8-a9db-4d7c-8fe7-7adb6ee3f95a)
![image](https://github.com/user-attachments/assets/5c7817a9-3566-4648-84a1-205d31d57660)

Both of these URLs have hits for being malicious on VirusTotal:

```
Forcepoint ThreatSeeker
information technology
 
Sophos
spyware and malware
 
Webroot
Phishing and Other Frauds
```
There is not much else to see in the Network Logs so I pivot to the EndPoint Security tab to look at the endpoint for `Samuel`.
![image](https://github.com/user-attachments/assets/48cf2646-3c2a-479b-885c-1bcbdce50a83)

As seen in the image above, this end point matches the criteria that i'm searching for.

![image](https://github.com/user-attachments/assets/3d5d81f3-a927-4351-9714-ba2907161367)

I look in the browser history and see the installation of the chrome extension here.

![image](https://github.com/user-attachments/assets/b0232f46-720a-4b9d-aa64-892289338b82)

The network traffic on the endpoint also confirms that it has communicated with both malicious IP addresses. `Domain: version.chatgpt4google.workers.dev` Indicating potential C2 communication.

![image](https://github.com/user-attachments/assets/066b4802-d52d-412c-98b0-944b852b5015)

The process history shows execution of the command:

```
"C:\Program Files\Google\Chrome\Application\chrome.exe" --single-argument C:\Users\LetsDefend\Desktop\hacfaophiklaeolhnmckojjjjbnappen.crx

```

Based on all these datapoints, we can see that the malware was successfully installed, connected to a c2 and was not automatically quarintined by the system.

I contained the endpoint (quarintined) and will note that the malware is still present on the system when I escalate the case to tier 2.

![image](https://github.com/user-attachments/assets/337895e3-3350-4746-9c2d-025b117cb653)
![image](https://github.com/user-attachments/assets/3c0555a3-0237-4a8b-a1b8-48835c78a367)


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


