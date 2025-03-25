# Winlogon Exploit Attempt Walkthrough

In this project, I will walk through the analysis of a SOC alert, outlining typical steps I take to analyze and close alerts. The goal is to keep it straightforward and easy to understand, even for non-technical readers.

![image](https://github.com/user-attachments/assets/fbf81965-b913-4d4a-9f0a-facd83b7ec56)

As always, accept ownership of the alert.
![image](https://github.com/user-attachments/assets/f8eb9457-1713-49fd-8d67-15d750e7f276)

Select to create case, this gives us access to the playbook to help keep direction during analysis, as always I copy the main details in this screen to notepad, this helps me keep all my collected data in one place.
![image](https://github.com/user-attachments/assets/e65757f6-4115-4bc6-a22e-6a42e2294940)
![image](https://github.com/user-attachments/assets/fa2d1098-8bb8-4474-8fc1-bdb5c00e90fb)
![image](https://github.com/user-attachments/assets/3d2193b5-aeaf-446a-8e10-2f1f9e59436a)

Note: For simplicity, know I will be following along with playbook steps, but in order to not use too many resources and to keep the walkthrough concise I will not be screenshotting all playbook screens.
If you're interested in seeing playbook screenshots you may refer to my other walkthroughs (Broken-Access-Controls, QR Phishing, as these cover the majority of playbook prompts in this environment.

As seen in the images above, the type of alert is categorizes as "LOLBin".
Living Off Land Binary: A LOLBin is any binary supplied by the operating system that is normally used for legitimate purposes but can also be abused by malicious actors. Several default system binaries have unexpected side effects, which may allow attackers to hide their activities post-exploitation.
(definition: talosintelligence.com)

As such, the first step is to Identify the Binary.

I navigate to the EndPoint Security tab and use the data in the notepad to find out which EndPoint(s) we are going to leverage.
From the data we can see the Host Name (Henry) that is populating the alert, so using this criterion I find an EndPoint.

![image](https://github.com/user-attachments/assets/eda44ee3-8ee6-4232-b218-b7c576532844)

As shown in the image above, the EndPoint for Henry gives us a bit more information to work with, it's a Win10 system, the IP matches with the IP in the alert and the Last Login shows:
Jun, 21, 2023, 12:24 PM which is just after the time our alert was triggered.

![image](https://github.com/user-attachments/assets/5808b607-4cc2-4c72-bf96-ec8c84e36830)

This EndPoint is full of useful data, like seen above, the Command Line history points to our exploit: 2023-06-21 10:07:12.580 copy cmd.exe utilman.exe
I can also see that they created another user and added them to the administration group for persistence and possibly lateral movement throughout the enterprise.

At this point I do some OSINT to better understand the utilman.exe lolbin exploit:

The Ulitman.exe LOLBin is a technique used by attackers to gain elevated privileges on Windows systems. It leverages the Accessibility Features of Windows, specifically the Ease of Access button on the login screen, to spawn a command prompt (cmd.exe) with SYSTEM privileges.

What is Utilman.exe?
Ulitman.exe is the Ease of Access utility manager located at:

C:\Windows\System32\Utilman.exe
It is responsible for launching accessibility tools like Narrator, Magnifier, or On-Screen Keyboard before a user logs in.

How the Attack Works:
Replace Utilman.exe with cmd.exe:

An attacker needs to replace the Utilman.exe file with a copy of cmd.exe.

This can be done using a bootable USB drive or by exploiting write permissions on the System32 folder.

Example command (from a bootable environment or with sufficient privileges):

copy C:\Windows\System32\cmd.exe C:\Windows\System32\Utilman.exe
Trigger the Ease of Access Menu:

After replacing the file, reboot the system.

At the login screen, click the Ease of Access button (bottom-right corner).

Gain SYSTEM Shell:

Instead of opening accessibility features, it spawns a command prompt as SYSTEM.

You now have full administrative control over the system.

Why Does This Work?
Windows allows the Utilman.exe to run as SYSTEM before a user logs in, as it is needed to assist users with disabilities.

Replacing it with cmd.exe tricks Windows into launching a SYSTEM-privileged shell.

I can already tell from the Command History that the exploit has been used: 2023-06-21 10:07:12.580 copy cmd.exe utilman.exe

I move to the process history to get a better understanding:
![image](https://github.com/user-attachments/assets/e9fcd2b5-959f-4c34-aba2-76492d688734)
![image](https://github.com/user-attachments/assets/0e28f32f-3a7d-4126-bb78-0f612762f177)


# Winlogon Exploit Attempt Walkthrough

In this project, I will walk through the analysis of a SOC alert, outlining typical steps I take to analyze and close alerts. The goal is to keep it straightforward and easy to understand, even for non-technical readers.

![image](https://github.com/user-attachments/assets/fbf81965-b913-4d4a-9f0a-facd83b7ec56)

## Initial Alert
As always, accept ownership of the alert.
![image](https://github.com/user-attachments/assets/f8eb9457-1713-49fd-8d67-15d750e7f276)

Select **Create Case** to gain access to the playbook, which helps maintain direction during analysis. I always copy the main details from this screen to **Notepad** to keep all collected data organized in one place.

![image](https://github.com/user-attachments/assets/e65757f6-4115-4bc6-a22e-6a42e2294940)
![image](https://github.com/user-attachments/assets/fa2d1098-8bb8-4474-8fc1-bdb5c00e90fb)
![image](https://github.com/user-attachments/assets/3d2193b5-aeaf-446a-8e10-2f1f9e59436a)

### Playbook Considerations
To keep the walkthrough concise and resource-efficient, I will not screenshot every playbook screen. For a detailed playbook breakdown, refer to my other walkthroughs like **Broken-Access-Controls** or **QR Phishing**, as they cover most of the prompts used in this environment.

## LOLBin Analysis
The alert categorizes this event as **LOLBin** (Living Off the Land Binary). A LOLBin is any binary supplied by the operating system that is normally used for legitimate purposes but can also be abused by malicious actors. Some default system binaries may have unexpected side effects, allowing attackers to hide post-exploitation activities.

> **Definition:** (Source: [Talos Intelligence](https://www.talosintelligence.com))

### Step 1: Identify the Binary
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

## How the Attack Works
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
After documenting the findings and submitting necessary artifacts, I escalate the alert for further investigation and close the case. The detailed report outlines:
- **Exploit Used:** Utilman.exe LOLBin
- **Successful Compromise:** SYSTEM privileges gained
- **Unclear Initial Access:** Requires further analysis by Tier 2

## Final Thoughts
Utilman.exe is a powerful LOLBin often exploited to escalate privileges. Proactively monitoring file integrity and employing **Least Privilege principles** can mitigate such risks. Early detection and rapid response are crucial to minimizing damage from such attacks.


Looking through the process history, we can see that they successfully logged in as their new administrator user and executed commands: C:\Windows\system32\cmd.exe /c ""C:\Users\superman\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\RunWallpaperSetupInit.cmd" "

This is enough information for us to know what type of exploit was used, and if it was succesful. At this point we do not understand how the user was compromised but it's not possible for us to understand that with our resources.
I will make mention of this in my notes to tier 2.

I then write up my report and submit artifacts to close the playbook, escalate the alert for further analysis and subsequently close the case.
