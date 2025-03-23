Today I will be going over a Phishing alert in the SOC enviroment.
![image](https://github.com/user-attachments/assets/65a1632b-0a59-431c-8278-64f10a4f1671)
![image](https://github.com/user-attachments/assets/3e95e234-bc52-4435-841c-41f220d09c9d)
![image](https://github.com/user-attachments/assets/ba03657b-e193-45b7-96f1-9f862d86323e)


After accepting ownership for the alert, I move to the Investigation Channel and create a case.
![image](https://github.com/user-attachments/assets/6687666e-c716-450a-9ed5-82342ff20bac)
![image](https://github.com/user-attachments/assets/e4a06d97-1e15-4e48-9da6-9b6c11ac6637)

This allows me to access the playbook.
As always I copy all the details from the Investigation Channel into a notepad so I can aggregate all the data I will find during analysis into one area.

![image](https://github.com/user-attachments/assets/6c5ecec0-dd5b-413a-b525-1df28aa59a4b)

The playbook gives us a little warning about verifying the cause of the alert before escalating it to Tier 2 Support.

![image](https://github.com/user-attachments/assets/57045edf-7328-49ca-b074-19ff43010b7c)
![image](https://github.com/user-attachments/assets/59a6d746-08ab-40c6-ac09-8cb2ec79bc50)

The playbook suggests moving to the Log Managment tab to look for relavent logs, entering: SMTP Address :
158.69.201.47, I am able to see one log. 
This log does not really give us any new information but does confirm the information given in the alert.

![image](https://github.com/user-attachments/assets/42ace2e9-6e6a-4e5d-af97-36d496adcb6b)
![image](https://github.com/user-attachments/assets/ffebd96b-fb54-43de-be61-9c7bb765e597)

![image](https://github.com/user-attachments/assets/add32fa7-ead6-4f7f-b306-e2d96da3f531)

The playbook directs me to make sure I look at these sources for suspicious data, one thing that sticks out is the domain name of the senders email: microsecmfa.com

![image](https://github.com/user-attachments/assets/4a28e31f-1419-4e24-96e2-ef3e2b0a9428)

After checking the logs and the EndPoint for Claire, I found no-suspicious activity.
