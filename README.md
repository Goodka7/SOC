# Security Operations Center

#### This is a typical SOC set up, that you might find in an enterprise.
<img src="https://github.com/user-attachments/assets/f9c26a4d-13e7-4945-a526-ee048414803b" width="900"><br>
#### On the left we have our resources that we will use to analyze alerts.

## Monitoring
#### As we can see above in the image, on the Monitoring tab, we have 3 sections: Main Channel, Investigation Channel and Closed Alerts
#### The "Main Channel" will have all the alerts currently available to the SOC, anyone in the SOC can see these alerts, and take ownership of them.
#### The "Investigation Channel" is a place where you can get details about cases that you've taken ownership, here you can "create a case" and "close an alert" that you have ownership of.
<img src="https://github.com/user-attachments/assets/34551d4b-85c2-470e-bec8-5b783163382a" width="500">
<img src="https://github.com/user-attachments/assets/905e1187-28c6-4fb9-a9de-f40d3a202a51" width="300"><br>
**Note:** Clicking "Create a case" gives you access to the Playbook:<br>

<img src="https://github.com/user-attachments/assets/ad44f92e-530a-43b4-b009-7583e0e3123b" width="300">
<img src="https://github.com/user-attachments/assets/812d7a57-a775-4d28-bc00-abe414162d17" width="300">
<img src="https://github.com/user-attachments/assets/d2812e68-8056-4cec-a5ce-5e195f868b0b" width="300">

#### The "Closed Alerts" tab is the place where you can find a history of all your closed alerts and review them.
<img src="https://github.com/user-attachments/assets/89ae7a20-8e36-4a41-a614-7c4a92a6ceca" width="900">

## Log Management
<img src="https://github.com/user-attachments/assets/d21deaa3-4f7d-4bb4-be94-2d3231c8998f" width="900">

#### As we can see in the image, here we can filter by different criteria such as IP addresses to find network logs.
#### There is also a "pro" version that allows you to be more nuanced in filtering the network logs.

## Case Management
<img src="https://github.com/user-attachments/assets/77c1b642-6795-4d38-b0ce-f439a195fd82" width="900">

#### As we can see in the image, here we can see the open and closed cases that we have/had ownership of.

## EndPoint Security
<img src="https://github.com/user-attachments/assets/9ad16f51-7937-4d13-833f-de3bc28532d0" width="900">

#### As we can see in the image above, the EndPoint Security tab is a place where we can view EndPoints on the network and get details about them:

<img src="https://github.com/user-attachments/assets/e281e4be-3c8f-4bf1-8d74-de67638f5700" width="500">

#### Here we see the details of an EndPoint called Cooper, you can see here we can Contain/Isolate the EndPoint from the network from here if needed. You can also see that some of these EndPoints can give us remote access.

<img src="https://github.com/user-attachments/assets/3d2c8d05-dc2f-4ba5-bdc9-a149ac66b327" width="500">

#### We are also able to view details such as processes, network actions, terminal actions and broswer history to help during our analysis.

## Email Security
<img src="https://github.com/user-attachments/assets/86e5138b-338c-407a-8033-0fde0fc06f27" width="500">

#### Here we see that we can filter email addresses to view emails that have been sent to users in the network.

## Threat Intelligence
<img src="https://github.com/user-attachments/assets/140be73e-b5e5-4c0e-bfe2-6f0007a9322a" width="500">

#### In this tab we can use several different methods to search for known threats from sources like VirusTotal.

## Sandbox
<img src="https://github.com/user-attachments/assets/92098bf3-ab61-4ec7-b2b6-8711e1968380" width="500">

#### Here we can access VMs that are set up as Sandboxes with Malware Analysis tools to help us analyze malware both dynamically and statically.
