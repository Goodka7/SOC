# Walkthrough: Forced Authentication Detected

## Initial Alert
The first step in the playbook is to figure out what the alert means. In this case, the alert says **"Forced Authentication Detected"**. That usually points to some kind of brute force attack or repeated login attempts.

### Details from the Alert
- **Source IP:** 120.48.36.175
- **Destination IP:** 104.26.15.61

I checked out the alert details to get more context. Next, I moved to the **Endpoint Security** tab to investigate the endpoints related to this alert.

## Endpoint Analysis
There was only one endpoint that got a hit related to the destination IP (`104.26.15.61`). However, the endpoint itself didn't actually have that IP assigned to it. This means I had to dig deeper to figure out why it was showing up in the search.

### Traffic Investigation
After looking into network traffic and checking log management, I found some traffic tied to the IP address, but it wasn't related to the event based on the event date (Dec 12, 2023, 02:15 PM). To get more insight, I decided to check both IP addresses through VirusTotal.

### VirusTotal Results
- **104.26.15.61 (CloudflareNet):** Clean, belongs to **AS 13335 (CLOUDFLARENET)**.
- **120.48.36.175 (Beijing Baidu Netcom):** Concerning, flagged for malicious activity including phishing by BitDefender, G-Data, and alphaMountain.ai.

## Discovery of Malicious Activity
Looking at the logs, I found that the **source IP (120.48.36.175)** was **enumerating ports** on the **destination IP (104.26.15.61)**. Further inspection revealed a **brute force attack on port 80**. Unfortunately, it looks like the attack was successful, and the attacker managed to log in.

## Proxy Server Complication
After more investigation, it became clear that **104.26.15.61** is a **proxy server (Cloudflare)**, meaning it’s just masking the IP of the actual endpoint where the web server is hosted. Because of this, I couldn’t directly quarantine the device. Instead, I'll escalate the issue and include this info in the report.

## Case Closure
At this point, I had gathered enough information to **close and escalate the alert**. I submitted my findings along with the report and marked the case as closed.

## Final Thoughts
The key takeaway here is that proxy servers like Cloudflare can make it harder to trace the actual origin of attacks. Understanding how proxy traffic works is crucial when analyzing incidents like this.

