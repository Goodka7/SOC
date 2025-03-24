![image](https://github.com/user-attachments/assets/02058a76-8b7d-42ac-833d-24194a02fd63)
![image](https://github.com/user-attachments/assets/85a15c88-c3c8-4a65-b8e4-7e515d791cc9)
![image](https://github.com/user-attachments/assets/16cb5968-abb5-4f77-807a-f971bc3e1dbd)
![image](https://github.com/user-attachments/assets/9b2caba5-468f-423a-8e5b-7ac757e42fee)

As always the first step in the playbook is to find out more about the rule. This alert says "Forced Authentication Detected".
Next I need to figure out what EndPoint(s) triggered this alert.
    Source IP :
    120.48.36.175
    Destination IP :
    104.26.15.61

I can see this information in the alert details, so I move to the EndPoint Security tab to look into the EndPoint(s).

![image](https://github.com/user-attachments/assets/29324201-835d-4bcf-a208-0a1a9fd4dd19)

Only one of these IPs gets a hit on an EndPoint inside the enterprise network: 104.26.15.61.
As the image above shows, this EndPoint does not have the IP in question assigned to it, meaning I need to dig a little further to find why it's showing up in our search.

![image](https://github.com/user-attachments/assets/65212aef-deee-462b-a5d0-614538989712)

We can see some traffic on this EndPoint related to the IP address, but this is not related to our event based on the date of our event: Dec, 12, 2023, 02:15 PM
I decide to run this IP through VirusTotal to see if it gets any hits. AS 13335 ( CLOUDFLARENET ) uses this IP and it's shown as clean.
I then decide to run the other IP (120.48.36.175) through VirusTotal to see what I can see: 120.48.36.175 (120.48.0.0/16) AS 38365 ( Beijing Baidu Netcom Science and Technology Co., Ltd. ) This is very concerning since our Enterprise is not based in China, futhermore some big name vendors have flagged it for malicious activity: BitDefender: Phishing, G-Data: Phishing, alphaMountain.ai: Suspicious

![image](https://github.com/user-attachments/assets/be9b8dd6-06e8-4edc-b22a-71f549f8ca47)

After checking the network traffic via Log Management, I find that the IP 120.48.36.175 is enumrating ports on 104.26.15.61.


![image](https://github.com/user-attachments/assets/3dc78112-0436-43a4-b25c-ae754117d2a4)

Further inspection of the traffic shows a Brute Force attempt on port 80

![image](https://github.com/user-attachments/assets/4891af6e-fd18-46f5-973c-7ad41feb88ff)
![image](https://github.com/user-attachments/assets/0656ff9b-897b-4e85-b904-53a24e6f5208)

As we can see, they were successfully able to log in.

Unfortunetly 104.26.15.61 seems to be a proxy server that is being used to mask the IP of the EndPoint that the WebServer is on, therefor I am unable to quarintine the device and so that will need to be taken care of after escalation, I will include this in my report.

At this point we have enough information to close and escalate this alert.

![image](https://github.com/user-attachments/assets/34f14b2b-da2c-48c5-9df3-e3e91ac568e4)
![image](https://github.com/user-attachments/assets/a0e43229-2a31-4c7b-b902-1032b3997d95)
![image](https://github.com/user-attachments/assets/daaa86f5-212d-43b5-9885-dbcf67f91f94)

I submitted my findings and my report and closed the case.
