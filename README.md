# Security Operations Center

#### This is a typical SOC set up, that you might find in an enterprise.
<img src="https://github.com/user-attachments/assets/f9c26a4d-13e7-4945-a526-ee048414803b" width="900"><br>
#### On the left we have our resources that we will use to analyze alerts.
- Monitoring - This is the screen shown in the image above.
- Log Management - This is where we can find network logs for the environment.
- Case Management - This is where we can review our current open and closed cases.
- Endpoint Security - This is where we can look at endpoints that are present in the network.
- Email Security - This is where we can look at emails that are sent across the network.
- Threat Intel - This is where we can cross-check threat intelligence by Domain, URL, Hash or IP
- Sandbox - This is where we can access a Windows and a Linux vm set up with tools to analyze malware.


## Monitoring
#### As we can see above in the image, on the Monitoring tab, we have 3 sections: Main Channel, Investigation Channel and Closed Alerts
#### The "Main Channel" will have all the alerts currently available to the SOC, anyone in the SOC can see these alerts, and take ownership of them.
#### The "Investigation Channel" is a place where you can get details about cases that you've taken ownership, here you can "create a case" and "close an alert" that you have ownership of.
<img src="https://github.com/user-attachments/assets/34551d4b-85c2-470e-bec8-5b783163382a" width="500">
<img src="https://github.com/user-attachments/assets/905e1187-28c6-4fb9-a9de-f40d3a202a51" width="300"><br>
**Note:** Clicking "Create a case" gives you access to the Playbook:<br>

<img src="https://github.com/user-attachments/assets/ad44f92e-530a-43b4-b009-7583e0e3123b" width="300">
<img src="https://github.com/user-attachments/assets/812d7a57-a775-4d28-bc00-abe414162d17" width="300">
<img src="https://github.com/user-attachments/assets/6e2d51c4-65d2-42c2-9582-30eff7768e73" width="300">

#### The "Closed Alerts" tab is the place where you can find a history of all your closed alerts and review them.
<img src="https://github.com/user-attachments/assets/89ae7a20-8e36-4a41-a614-7c4a92a6ceca" width="900">

## Log Management
<img src="https://github.com/user-attachments/assets/d21deaa3-4f7d-4bb4-be94-2d3231c8998f" width="900">

#### As we can see in the image, here we can filter by different criteria such as IP addresses to find network logs.
#### There is also a "pro" version that allows you to be more nuanced in filtering the network logs.

## Case Management
<img src="https://github.com/user-attachments/assets/77c1b642-6795-4d38-b0ce-f439a195fd82" width="900">

#### As we can see in the image, here we can see the open and closed cases that we have/had ownership of.
