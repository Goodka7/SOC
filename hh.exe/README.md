# Suspicious hh.exe Usage

In this project, I will walk through the analysis of a SOC Alert, outlining typical steps I take to analyze and close alerts. To keep the walkthrough concise and resource-efficient, I will not screenshot every playbook screen nor go over menu options. For a detailed playbook breakdown, refer to my other walkthroughs like [Broken-Access-Controls](https://github.com/Goodka7/SOC/blob/main/Broken-Access-Control/README.md) or [QR Phishing](https://github.com/Goodka7/SOC/blob/main/QRPhishing/README.md), as they cover most of the prompts used in this environment. You may also go to the [SOC overview](https://github.com/Goodka7/SOC/blob/main/README.md) for a more detailed understanding of the environment I am working with. **Please note that in an effor to reduce resource usage for Github, I have reused images from other walkthroughs, except where the information is specific to this case.**

## Initial Alert
The first step is to accept ownership of the alert:
<div align="center">
<img src="https://github.com/user-attachments/assets/36f59bcb-5acc-46dc-b225-134577db6aa5" width="500">
</div>

After accepting ownership, I move to the Investigations Channel to copy the alert details into my notepad. This helps me quickly review collected data while navigating different screens.
<div align="center">
<img src="https://github.com/user-attachments/assets/6a7bc585-b4f0-4f82-bbcf-27c9c648ff70" width="500">
</div>

After clicking "create case," I open the playbook and begin analyzing the gathered information which I have copied to my notepad, as always.
