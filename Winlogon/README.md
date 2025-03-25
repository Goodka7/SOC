# Winlogon Exploit Attempt Walkthrough

In this project, I will walk through the analysis of a SOC alert, outlining typical steps I take to analyze and close alerts. The goal is to keep it straightforward and easy to understand, even for non-technical readers.

<div align="center">
<img src="https://github.com/user-attachments/assets/fbf81965-b913-4d4a-9f0a-facd83b7ec56" width="500"><br>

</div>
As always, accept ownership of the alert.

## Initial Alert
![image](https://github.com/user-attachments/assets/f8eb9457-1713-49fd-8d67-15d750e7f276)

Select **Create Case** to gain access to the playbook, which helps maintain direction during analysis. I always copy the main details from this screen to **Notepad** to keep all collected data organized in one place.

<div align="center">
<img src="https://github.com/user-attachments/assets/e65757f6-4115-4bc6-a22e-6a42e2294940" width="500"><br>
<img src="https://github.com/user-attachments/assets/fa2d1098-8bb8-4474-8fc1-bdb5c00e90fb" width="500"><br>
<img src="https://github.com/user-attachments/assets/3d2193b5-aeaf-446a-8e10-2f1f9e59436a" width="500">
</div>

### Playbook Considerations
To keep the walkthrough concise and resource-efficient, I will not screenshot every playbook screen. For a detailed playbook breakdown, refer to my other walkthroughs like **Broken-Access-Controls** or **QR Phishing**, as they cover most of the prompts used in this environment.

## LOLBin Analysis
The alert categorizes this event as **LOLBin** (Living Off the Land Binary). A LOLBin is any binary supplied by the operating system that is normally used for legitimate purposes but can also be abused by malicious actors. Some default system binaries may have unexpected side effects, allowing attackers to hide post-exploitation activities.

> **Definition:** (Source: [Talos Intelligence](https://www.talosintelligence.com))

### Step 1: Identify the EndPoint
Navigate to the **Endpoint Security** tab and use the data stored in Notepad to identify the endpoint(s) involved. In this case, the hostname **Henry** is associated with the alert, so I locate the endpoint using this criterion.

![image](https://github.com/user-attachments/assets/eda44ee3-8ee6-4232-b218-b7c576532844)

The endpoint details reveal useful information:
- **Operating System:** Windows 10
- **IP Address:** Matches the IP from the alert
- **Last Login:** June 21, 2023, 12:24 PM (shortly after the alert was triggered)

### Step 2: Investigate Command History
The command line history on the endpoint shows the following suspicious command:
```
2023-06-21 10:07:12.580 copy cmd.exe utilman.exe
```
This indicates the **Utilman.exe LOLBin exploit** was used. Attackers often replace **Utilman.exe** with **cmd.exe** to gain SYSTEM privileges via the **Ease of Access** button at the login screen.

## How the Attack Works(low-level)
- **Replace Utilman.exe with cmd.exe:**
  - An attacker copies **cmd.exe** to **Utilman.exe** to launch a privileged shell.
- **Trigger Ease of Access Menu:**
  - Click the **Ease of Access** button at the login screen to open a SYSTEM-privileged shell.
- **Gain SYSTEM Shell:**
  - Instead of accessibility features, a **command prompt** with **SYSTEM privileges** opens, granting full administrative control.

## Step 3: Review Process History
To gain further context, I examine the **process history** on the compromised endpoint.

![image](https://github.com/user-attachments/assets/e9fcd2b5-959f-4c34-aba2-76492d688734)
![image](https://github.com/user-attachments/assets/0e28f32f-3a7d-4126-bb78-0f612762f177)

Here, I observe the attacker successfully logging in as the newly created admin user and executing commands like:
```
C:\Windows\system32\cmd.exe /c "C:\Users\superman\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\RunWallpaperSetupInit.cmd"
```

This confirms the exploit succeeded. However, the initial compromise vector remains unknown with the available resources. I make a note of this for **Tier 2 analysis**.

## Case Closure
![image](https://github.com/user-attachments/assets/6dac9fbc-4c47-4172-9b99-517148f7700c)
After documenting the findings and submitting necessary artifacts, I quarantine the EndPoint, then escalate the alert for further investigation and close the case. The detailed report outlines:
- **Exploit Used:** Utilman.exe LOLBin
- **Successful Compromise:** SYSTEM privileges gained
- **Unclear Initial Access:** Requires further analysis by Tier 2
![image](https://github.com/user-attachments/assets/85999d3a-4c2f-4182-b2be-db273372420b)
![image](https://github.com/user-attachments/assets/2b08cae7-9f29-4188-b180-21cb7e1058b5)

## Final Thoughts
Utilman.exe is a powerful LOLBin often exploited to escalate privileges. Proactively monitoring file integrity and employing **Least Privilege principles** can mitigate such risks. Early detection and rapid response are crucial to minimizing damage from such attacks.
