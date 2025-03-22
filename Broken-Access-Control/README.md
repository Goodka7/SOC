# Broken Access Control

In this project, I will walkthrough the analysis of a SOC Alert, outlining typical steps I take to analyze and close alerts.
I will not be re-visiting descriptions from the SOC overview.

The first thing I do is open an alert that I have taken ownership of in the Investigation Channel.
![image](https://github.com/user-attachments/assets/ca39d5ad-d183-4d6d-8f45-fd8a32dda9fb)

This allows me to review key pieces of information that I will use to conduct a thorough analysis.
At this point, I always copy this information down and paste it in a notepad. This step is taken because I will be switching screens/tabs often and need to have all my gathered information available in one place so I dont need to switch back and forth between several screens/tabs.
This is also done because the playbook in this particular set up cannot be closed out of (or you'll have to start the playbook from scratch).

Now that I have all the basic information copied down, I will create a case.
![image](https://github.com/user-attachments/assets/bd2f790a-99ca-47cc-bb35-3f197d99d6f9)
![image](https://github.com/user-attachments/assets/3a96e674-bee7-4fa3-8c4d-1a9559bf5ac8)

This will take me to the Incident Details page, which provides some of the details from the first screen, but not as many, here we can start the playbook.
![image](https://github.com/user-attachments/assets/2d0ab8d8-e81b-41dc-aa3b-516cebb80278)

I will open a new tab/screen at this point so that I can keep the playbook active.

![image](https://github.com/user-attachments/assets/06fa99e0-ce5d-402c-8f75-aa291ab543d2)

Following the information in the playbook, we can start looking for artifacts.
This playbook first suggest we look at the "rule" name, this is referring to why the alert was triggered:

SOC235 - Atlassian Confluence Broken Access Control 0-Day CVE-2023-22515

From this, I decide to go look for some information regarding the CVE mentioned.
Atlassian's own webpage has a write-up with a lot of good information on this CVE-2023-22515 including some threat hunting tips:

![image](https://github.com/user-attachments/assets/5a3ba307-db00-4604-bdfa-ee2ccf68e2f1)

The playbook also suggests looking at traffic between the two devices (Destination IP Address : 172.16.17.234, Source IP Address : 43.130.1.222).
I go to the Log Management page to look for that information.

![image](https://github.com/user-attachments/assets/14b5feac-5fd3-4d15-8d51-25081a4c30a6)

I focus on the Source IP Address, as it's (seemingly) one from outside the network and can see that it has been communicating with our Dest. IP.
After putting the relevant information in my notepad (screenshots below), I move on with the playbook.

![image](https://github.com/user-attachments/assets/b90ed80a-f8cf-486e-bb24-603046510ba5)
![image](https://github.com/user-attachments/assets/400305ed-0ec3-40f3-a71f-ee98d27cd4df)
![image](https://github.com/user-attachments/assets/d70edf4a-1864-4795-968f-b49dff8025da)
![image](https://github.com/user-attachments/assets/7e230dae-ab70-4bec-ad01-7fc253e5ffe7)

![image](https://github.com/user-attachments/assets/82b114eb-d1ae-4e6b-bafd-cd3ad182c222)
The next step of the playbook suggests some sources to collect data from

I navigate over to the EndPoint Security tab and enter 43.130.1.222 to confirm if the device is in the network or not. No devices show up with that IP.
I then enter the other IP which seems to be an in network device and get a hit:

![image](https://github.com/user-attachments/assets/7ccc129a-ac24-42d4-8da5-2e1d1e64a72a)

So we have found the EndPoint that has triggered the alert, but I am going to do some digging on the other IP first, namely I am going to find out who owns it and if OSINT has any hits on it.

Whoismind.com has this:
![image](https://github.com/user-attachments/assets/50dae793-edae-42b0-9a50-8ad1aac190d0)
![image](https://github.com/user-attachments/assets/219a6d74-095c-428b-979c-56aa77d119dc)
VirusTotal.com has this:
![image](https://github.com/user-attachments/assets/b131be1f-ce08-4da7-a175-42e11ad71d00)

Now we have enough information in our notepad to answer the questions in the playbook;

Ownership of the IP addresses and devices:
      172.16.17.234 - A device inside our enterprise network.
      43.130.1.222 - Tencent a company based in CHINA.
      
If the traffic is coming from outside (Internet): 
      Yes, 43.130.1.222 is outside the network, communicating over port 80(HTTP).
      
Ownership of IP address:
      43.130.1.122 is a pool address, the indications from OSINT show that it's possibly cloud-hosted.
      
Reputation of IP Address:
      43.130.1.122 showed low hits on VirusTotal, but this is likely due to the IP address being non-static and web/cloud hosted.

If the traffic is coming from company network:
Hostname of the device:
      Confluence Data Center v8.0.3

![image](https://github.com/user-attachments/assets/10eba1ee-9f78-4a01-95ce-128e9cede028)

Who owns the device (username): 
      LetsDefend

Last user logon time:
      Nov, 08, 2023, 12:00 PM

We can now move on with the playbook.

![image](https://github.com/user-attachments/assets/0c8c3a62-273a-4aa7-84e4-10b52100efaf)

Since I already took the initiative, I already recorded the relevant information in my notepad, and we will move to the next step of the playbook.

![image](https://github.com/user-attachments/assets/536212c0-52d9-4f0c-9760-a23531fc1053)

The playbook now wants us to use the information we've gathered so far to make a decision about the traffic, was it malicious or not?

My personal opinion, based on the data I have obtained, it is in fact malicious.

![image](https://github.com/user-attachments/assets/3ce3f09e-ce7d-4144-b638-8f806157be05)

The playbook now wants us to indentify the attack type, this will depend on your enterprises definitions but for my analysis I will choose "Other".

![image](https://github.com/user-attachments/assets/f68e6290-3d53-49d9-b36d-d9a93564127b)

The playbook now suggest we look at email logs to see if it was a planned test of the system, since the EndPoint did not have a email address associated with it, we can use a few key words to query the email logs, at the very least we can narrow it down by date. We can see that the activities took place Nov,8 2023 and Nov,9 2023 so we will look for emails a few days before and after this.

![image](https://github.com/user-attachments/assets/bfa1adb4-ecb5-4da3-b4f5-17a3f64fbc61)

As we can see from the image above, there are no emails for this week, meaning it's very unlikely that there was a test.

![image](https://github.com/user-attachments/assets/914c2432-ba11-44c4-b1b8-12e69c50a3a3)

Now the playbook is asking for us to select which direction the traffic was coming from (likely so they can refine firewall rules if needed), we can see from the network logs earlier that the attack was coming from the internet over port 80. Internet > Company is the correct choice.

![image](https://github.com/user-attachments/assets/836b3478-d50e-4968-9a42-e1888d782abc)

Next the playbook is asking to verify if the attack was actually successful, this is something that we can possibly verify on the EndPoint.
![image](https://github.com/user-attachments/assets/1fad3e42-f7be-400e-88e5-8f3efe196801)

After looking over the EndPoint, there was no suspicious processes running, and the Terminal (Command) history and browser history were blank. However, we can see from the earlier network logs (above) that the HTTP Requests from the Malicious IP were given a response code of "200" meaning that the server accepted the commands. This means the attack was in fact successful.

![image](https://github.com/user-attachments/assets/068776cc-7259-4a62-8e21-cb0fd7bc824d)

I then navigate back to the compromised EndPoint: Confluence Data Center v8.0.3 and contain (isolate) it.
![image](https://github.com/user-attachments/assets/1b6f0bb9-3add-4cb7-86b4-b99929e14ded)


![image](https://github.com/user-attachments/assets/7e8e212e-eecc-4f70-b573-f767f40c871c)
The playbook next gives us a chance to add key artifacts that we used in our analysis.

![image](https://github.com/user-attachments/assets/d7808960-70bc-48c8-b125-814ed6c1b208)

Now the playbook asks if I need a tier2 escalation. Since we did find indicators of successful attack, we should definitely escalate.

![image](https://github.com/user-attachments/assets/3e7c76fe-ebb6-49f4-9e7c-8d69a70919bd)

I make a brief report for the playbook giving some key data points.

![image](https://github.com/user-attachments/assets/5880d4a2-0741-4c59-b051-95359aa7e7d0)
![image](https://github.com/user-attachments/assets/f630c2e1-4678-4c47-9a0e-b2cdcc72577e)

Finally I finish the playbook, but we're not done yet, we need to close the case.

![image](https://github.com/user-attachments/assets/076cd78a-0ee7-43e7-a32e-130823aeb47a)
![image](https://github.com/user-attachments/assets/ea8dd866-aec2-4395-bda4-3c4e0ceb4881)

# Broken Access Control

In this project, I will walk through the analysis of a SOC Alert, outlining typical steps I take to analyze and close alerts. I will not be re-visiting descriptions from the SOC overview.

## Initial Investigation
The first thing I do is open an alert that I have taken ownership of in the Investigation Channel.

![image](https://github.com/user-attachments/assets/ca39d5ad-d183-4d6d-8f45-fd8a32dda9fb)

This allows me to review key pieces of information that I will use to conduct a thorough analysis. At this point, I always copy this information down and paste it into a notepad. This step is taken because I will be switching screens/tabs often and need to have all my gathered information available in one place so I don't need to switch back and forth between several screens/tabs.

This is also done because the playbook in this particular setup cannot be closed out of (or you'll have to start the playbook from scratch).

## Creating a Case
Now that I have all the basic information copied down, I will create a case.

![image](https://github.com/user-attachments/assets/bd2f790a-99ca-47cc-bb35-3f197d99d6f9)

![image](https://github.com/user-attachments/assets/3a96e674-bee7-4fa3-8c4d-1a9559bf5ac8)

This will take me to the **Incident Details** page, which provides some of the details from the first screen, but not as many. Here we can start the playbook.

![image](https://github.com/user-attachments/assets/2d0ab8d8-e81b-41dc-aa3b-516cebb80278)

I will open a new tab/screen at this point so that I can keep the playbook active.

![image](https://github.com/user-attachments/assets/06fa99e0-ce5d-402c-8f75-aa291ab543d2)

## Analyzing the Rule
Following the information in the playbook, we can start looking for artifacts. The playbook first suggests we look at the **rule name**, referring to why the alert was triggered:

**SOC235 - Atlassian Confluence Broken Access Control 0-Day CVE-2023-22515**

From this, I decide to look for some information regarding the CVE mentioned. Atlassian's own webpage has a write-up with a lot of good information on this CVE-2023-22515, including some threat hunting tips:

![image](https://github.com/user-attachments/assets/5a3ba307-db00-4604-bdfa-ee2ccf68e2f1)

## Log Analysis
The playbook also suggests looking at traffic between the two devices (Destination IP Address: 172.16.17.234, Source IP Address: 43.130.1.222). I go to the **Log Management** page to look for that information.

![image](https://github.com/user-attachments/assets/14b5feac-5fd3-4fa3-8c4d-1a9559bf5ac8)

I focus on the Source IP Address, as it seems to be from outside the network, and can see that it has been communicating with our Destination IP. After putting the relevant information in my notepad, I move on with the playbook.

![image](https://github.com/user-attachments/assets/b90ed80a-f8cf-486e-bb24-603046510ba5)

![image](https://github.com/user-attachments/assets/400305ed-0ec3-40f3-a71f-ee98d27cd4df)

![image](https://github.com/user-attachments/assets/d70edf4a-1864-4795-968f-b49dff8025da)

![image](https://github.com/user-attachments/assets/7e230dae-ab01-7fc253e5ffe7)

## Endpoint Security Check
The next step of the playbook suggests some sources to collect data from. I navigate to the **Endpoint Security** tab and enter `43.130.1.222` to confirm if the device is in the network or not. No devices show up with that IP. I then enter the other IP, which seems to be an in-network device, and get a hit:

![image](https://github.com/user-attachments/assets/7ccc129a-ac24-42d4-8da5-2e1d1e64a72a)

## OSINT and External Analysis

Next, I perform some OSINT on the external IP address (43.130.1.222) to identify ownership and reputation. Using WhoisMind and VirusTotal, I gather information indicating that the IP is associated with Tencent, a company based in China, and that it is part of a cloud hosting pool. Although VirusTotal shows a low detection rate, this is likely due to it being a non-static, cloud-hosted IP address.

![image](https://github.com/user-attachments/assets/50dae793-edae-42b0-9a50-8ad1aac190d0)
![image](https://github.com/user-attachments/assets/219a6d74-095c-428b-979c-56aa77d119dc)
![image](https://github.com/user-attachments/assets/b131be1f-ce08-4da7-a175-42e11ad71d00)

## Determining Malicious Activity
Based on the data collected, I assess whether the traffic from the external IP is malicious. Given the HTTP response code "200" in the logs, indicating successful execution of the attack, I determine that the attack was successful.

## Containment and Mitigation
Since the attack was deemed successful, I proceed to contain the compromised endpoint by isolating the Confluence server (v8.0.3). This step is necessary to prevent any further exploitation or lateral movement within the network.

![image](https://github.com/user-attachments/assets/1b6f0bb9-3add-4cb7-86b4-b99929e14ded)

## Reporting and Escalation
Given the confirmed successful exploitation, I initiate a Tier 2 escalation as per the playbook guidelines. I document key artifacts and details gathered during the investigation and submit them as part of the escalation report.

![image](https://github.com/user-attachments/assets/3e7c76fe-ebb6-49f4-9e7c-8d69a70919bd)

## Finalizing the Case
I complete the playbook and proceed to close the case. All investigation details and remediation steps are thoroughly documented to ensure accurate reporting and compliance with SOC protocols.

![image](https://github.com/user-attachments/assets/5880d4a2-0741-4c59-b051-95359aa7e7d0)
![image](https://github.com/user-attachments/assets/f630c2e1-4678-4f47-9a0e-b2cdcc72577e)

## Summary
The incident involved a successful exploitation of CVE-2023-22515 via a broken access control vulnerability. The attack originated from an external IP (43.130.1.222) and successfully created an unauthorized administrator account. Immediate containment and escalation steps were performed as per the SOC playbook.

