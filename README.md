# EDR-Attack-and-Defense
This lab project is focused on simulating a realistic cyber attack and evaluating endpoint detection and response (EDR) capabilities. Following Eric Capuano’s online guide, I’ve set up virtual machines to represent both attacker and victim environments. The attacker machine uses the Sliver C2 framework to carry out the attack, while the target Windows endpoint is monitored using LimaCharlie as the EDR solution.
# Setup
The first step in this lab is to configure both virtual machines. The attack machine will run Ubuntu Server, while the target endpoint will be a Windows 11 system. To ensure smooth execution of the lab, Microsoft Defender and other relevant protections on the Windows machine should be temporarily disabled. On the Ubuntu machine, I’ll be installing Sliver, which will serve as the primary command and control (C2) framework for the simulated attacks. Meanwhile, the Windows endpoint will be equipped with LimaCharlie as the EDR solution. A LimaCharlie sensor will be deployed on the Windows machine and configured to ingest telemetry, including detailed Sysmon logs, for monitoring and response.
#
Windows 11 Machine (LimaCharlie setup)
![charlielimasetup](https://github.com/user-attachments/assets/1fc8b45c-4031-46ee-96a1-35d691885f67)
 Now we configure the sensors and add our rules.
![sensor creation](https://github.com/user-attachments/assets/3b4d7114-6d81-493c-aa12-4fa2ca19a983)
![artifact collection](https://github.com/user-attachments/assets/4dc17047-fc61-4c7a-b00d-14ba9cda5897)
#
Ubuntu Server Machine(Sliver C2)
We download Sliver and then run "Systemctl status Sliver" to make sure its live.
![sliver setup](https://github.com/user-attachments/assets/f2066c80-b2d9-4655-9c60-e52b505392f4)
![launch sliver](https://github.com/user-attachments/assets/a2186a1a-acc1-49de-939d-104bae5d21be)
Now we generate out payload.![payload creation](https://github.com/user-attachments/assets/5098f23b-e53f-42bf-b80a-ec9f5bc6798f)
