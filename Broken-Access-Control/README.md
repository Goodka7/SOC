# Broken Access Control

In this project, I will walk through the analysis of a SOC Alert, outlining typical steps I take to analyze and close alerts. I will not be re-visiting descriptions from the SOC overview.

## Initial Investigation
The first thing I do is open an alert that I have taken ownership of, in the Investigation Channel.

![image](https://github.com/user-attachments/assets/ca39d5ad-d183-4d6d-8f45-fd8a32dda9fb)

This allows me to review key pieces of information that I will use to conduct a thorough analysis. At this point, I always copy this information down and paste it into a notepad. This step is taken because I will be switching screens/tabs often and need to have all my gathered information available in one place so I don't need to switch back and forth between several screens/tabs.

This is also done because the playbook in this particular setup cannot be closed out of (or you'll have to start the playbook from scratch).

## Creating a Case
Now that I have all the basic information copied down, I will create a case.

<div align="center">
<img src="https://github.com/user-attachments/assets/bd2f790a-99ca-47cc-bb35-3f197d99d6f9" width="300"><br>
<img src="https://github.com/user-attachments/assets/3a96e674-bee7-4fa3-8c4d-1a9559bf5ac8" width="500">
</div>

This will take me to the **Incident Details** page, which provides some of the details from the first screen, but not as many. Here we can start the playbook.

<div align="center">
<img src="https://github.com/user-attachments/assets/2d0ab8d8-e81b-41dc-aa3b-516cebb80278" width="500">
</div>

I will open a new tab/screen at this point so that I can keep the playbook active.

<div align="center">
<img src="https://github.com/user-attachments/assets/06fa99e0-ce5d-402c-8f75-aa291ab543d2" width="500">
</div>

## Analyzing the Rule
Following the information in the playbook, we can start looking for artifacts. The playbook first suggests we look at the **rule name**, referring to why the alert was triggered:

**SOC235 - Atlassian Confluence Broken Access Control 0-Day CVE-2023-22515**

From this, I decide to look for some information regarding the CVE mentioned. Atlassian's own webpage has a write-up with a lot of good information on this CVE-2023-22515, including some threat hunting tips:

![image](https://github.com/user-attachments/assets/e0b51e20-8a0c-4611-891e-06f20c87e728)
![image](https://github.com/user-attachments/assets/5a3ba307-db00-4604-bdfa-ee2ccf68e2f1)

## Log Analysis
The playbook also suggests looking at traffic between the two devices (Destination IP Address: 172.16.17.234, Source IP Address: 43.130.1.222). I go to the **Log Management** page to look for that information.

![image](https://github.com/user-attachments/assets/d2490ac4-2a17-41dd-b60f-3bc4b604129d)


I focus on the Source IP Address, as it seems to be from outside the network, and can see that it has been communicating with our Destination IP. After putting the relevant information in my notepad, I move on with the playbook.

![image](https://github.com/user-attachments/assets/b90ed80a-f8cf-486e-bb24-603046510ba5)

![image](https://github.com/user-attachments/assets/400305ed-0ec3-40f3-a71f-ee98d27cd4df)

![image](https://github.com/user-attachments/assets/d70edf4a-1864-4795-968f-b49dff8025da)

![image](https://github.com/user-attachments/assets/7e230dae-ab70-4bec-ad01-7fc253e5ffe7)

## Endpoint Security Check

<div align="center">
<img src="https://github.com/user-attachments/assets/82b114eb-d1ae-4e6b-bafd-cd3ad182c222" width="500">
</div>

The next step of the playbook suggests some sources to collect data from. I navigate to the **Endpoint Security** tab and enter `43.130.1.222` to confirm if the device is in the network or not. No devices show up with that IP. I then enter the other IP, which seems to be an in-network device, and get a hit:

![image](https://github.com/user-attachments/assets/7ccc129a-ac24-42d4-8da5-2e1d1e64a72a)

## OSINT Analysis

Next, I perform some OSINT on the external IP address (43.130.1.222) to identify ownership and reputation. Using WhoisMind and VirusTotal, I gather information indicating that the IP is associated with Tencent, a company based in China, and that it is part of a cloud hosting pool. Although VirusTotal shows a low detection rate, this is likely due to it being a non-static, cloud-hosted IP address.

![image](https://github.com/user-attachments/assets/50dae793-edae-42b0-9a50-8ad1aac190d0)
![image](https://github.com/user-attachments/assets/219a6d74-095c-428b-979c-56aa77d119dc)
![image](https://github.com/user-attachments/assets/b131be1f-ce08-4da7-a175-42e11ad71d00)

Now we have enough information in our notepad to answer the questions in the playbook;

#### Ownership of the IP addresses and devices: 
172.16.17.234 - A device inside our enterprise network.<br> 
43.130.1.222 - Tencent a company based in CHINA.

#### If the traffic is coming from outside (Internet): 
Yes, 43.130.1.222 is outside the network, communicating over port 80(HTTP).

#### Ownership of IP address:

43.130.1.122 is a pool address, the indications from OSINT show that it's possibly cloud-hosted.

#### Reputation of IP Address: 
43.130.1.122 showed low hits on VirusTotal, but this is likely due to the IP address being non-static and web/cloud hosted.

#### Hostname of the device: 
Confluence Data Center v8.0.3

#### Who owns the device (username): 
LetsDefend

#### Last user logon time: 
Nov, 08, 2023, 12:00 PM

## Examining HTTP Traffic

<div align="center">
<img src="https://github.com/user-attachments/assets/7c6e0d1b-d75e-48ba-a597-f9a7865e16d9" width="500">
</div>

Since I already collected this data earlier, I move on to the next step.

## Determining Malicious Activity

<div align="center">
<img src="https://github.com/user-attachments/assets/d9c174f4-625e-4272-8a5d-96325a7d9d61" width="500">
</div>

Based on the data collected, I assess whether the traffic from the external IP is malicious. Given that the payload reflects variables that were shown in the OSINT and that the IP itself had been flagged, I determined that the traffic was malicious. 

## Attack Type

<div align="center">
<img src="https://github.com/user-attachments/assets/4e94d1de-5da2-40f2-9276-00fef4701116" width="500">
</div>

The playbook asks me to identify the attack type: In this case I chose "Other".

## Planned or Not?

<div align="center">
<img src="https://github.com/user-attachments/assets/f44a4503-3fd6-4c14-911d-a09b0d4bdaf0" width="500">
</div>

The playbook asks us to check E-mail logs so that I can see if this was a planned test or not, after searching through the E-mail logs, I could not find any indication that it was planned.

<div align="center">
<img src="https://github.com/user-attachments/assets/4ab05c5a-4316-48dd-aca1-455215279c11" width="500">
</div>

## Direction of Traffic

<div align="center">
<img src="https://github.com/user-attachments/assets/0a64e346-89dc-4fba-99a7-385e259250f3" width="500">
</div>

Following along with the playbook, it askes me to identify the direction that the network traffic was going. Based on the data: Internet > Company Network.

## Was the Attack Successful?

<div align="center">
<img src="https://github.com/user-attachments/assets/943951c7-14c0-4e17-95d9-9ddc1f1c0869" width="500">
</div>

The playbook wants me to make sure I verify if the attack was actually successful. Based on the Network activity data, the HTTP response code "200" in the logs indicates a successful execution of the attack, so I determine that the attack was indeed successful.

## Containment and Mitigation

<div align="center">
<img src="https://github.com/user-attachments/assets/2bb67ae5-44fc-4bd8-b61c-e4bc3679f97e" width="500">
<img src="https://github.com/user-attachments/assets/c94221b4-017d-4a52-bf1e-e1fc732aedf1" width="500">
</div>

I proceed to contain the compromised endpoint by isolating the Confluence Data Center (v8.0.3). This step is necessary to prevent any further exploitation or lateral movement within the network.

## Reporting and Escalation

<div align="center">
<img src="https://github.com/user-attachments/assets/9c24d32d-24b9-4aa6-a2d0-74a4de6d276d" width="500">
</div>

Given the confirmed successful exploitation, I initiate a Tier 2 escalation as per the playbook guidelines. I document key artifacts and details gathered during the investigation and submit them as part of the escalation report.

<div align="center">
![image](https://github.com/user-attachments/assets/3e7c76fe-ebb6-49f4-9e7c-8d69a70919bd)
</div>

## Finalizing the Case

<div align="center">
<img src="https://github.com/user-attachments/assets/fb3bddbc-de17-49ae-b36b-82c999b3c8da" width="500">
<img src="https://github.com/user-attachments/assets/c0aeb68a-242a-476c-a955-f955bdf8d7a5" width="300">
</div>

I complete the playbook and proceed to close the case. All investigation details and remediation steps are thoroughly documented to ensure accurate reporting and compliance with SOC protocols.

<div align="center">
<img src="https://github.com/user-attachments/assets/f302b86b-689b-4274-8b48-9e940cb08d98" width="500">
</div>
  
![image](https://github.com/user-attachments/assets/f0b9b8b2-a58b-4b93-be17-00337cd42575)

## Summary
The incident involved a successful exploitation of CVE-2023-22515 via a broken access control vulnerability. The attack originated from an external IP (43.130.1.222) and successfully created an unauthorized administrator account. Immediate containment and escalation steps were performed as per the SOC playbook.
