
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
