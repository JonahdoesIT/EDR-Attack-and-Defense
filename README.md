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

# The Attack
Now we generate our payload.![payload creation](https://github.com/user-attachments/assets/5098f23b-e53f-42bf-b80a-ec9f5bc6798f)
![silver http listener](https://github.com/user-attachments/assets/749a90e9-4901-40b8-9d33-58f79b061407)
Here we want to make sure to enable our listener on port 80 so we can host our webpage. This contains our payload to infect the Windows machine.
# The Infection
Switching back to our windows machine we will now open the phishing link to intentionally infect our own machine.
![run the implant](https://github.com/user-attachments/assets/3fe75ae5-aea6-49c7-b262-5b228b606c9c)

![run implant keep anyway](https://github.com/user-attachments/assets/e94be526-8170-471a-9281-0ff1a17975a2)

Looking back to our Attacking machine we can now see the session is "Live" which confirms our payload worked.
![session is alive](https://github.com/user-attachments/assets/0d93775d-8ef7-4a51-8292-25a73097137f)
#
Now that we have a live session we can begin peeking around , taking control and getting host information.
![get privs](https://github.com/user-attachments/assets/eb681827-856f-486b-9f20-0090e0622d6f)
![Ps-t 1](https://github.com/user-attachments/assets/d497e68c-7c43-45b1-9987-46eb4492d7c0)
![ps-t2](https://github.com/user-attachments/assets/d9fd95a5-4cba-4cb8-a10d-5b01d7b03504)
#

On the Windows host, we can monitor the attacker’s activity in real-time through the LimaCharlie SIEM. The telemetry allows us to identify the running payload and observe the IP address it’s communicating with.
![limacharliesiem proccess](https://github.com/user-attachments/assets/1c13d7b8-a711-4d96-8040-10788c136682)
![malware process siem](https://github.com/user-attachments/assets/6a645b48-71ba-4597-9130-eced605f5403)

![network connection for payload](https://github.com/user-attachments/assets/604bb875-27be-4651-aba4-82f04df9902f)
#
We can also leverage LimaCharlie to scan the payload hash using VirusTotal. While the scan will return clean results—since the payload was custom-built—it’s still a valuable step in validating unknown files during real-world investigations and adds an extra layer of threat intelligence to the detection process.
![file system check hash](https://github.com/user-attachments/assets/5213a8d1-a1c4-4574-917c-693cbf737ecc)

![hash for payload](https://github.com/user-attachments/assets/14f2647b-d069-42b0-911c-b13f9545cebf)

![hash not found](https://github.com/user-attachments/assets/2de3f09a-af4c-4a00-b34c-552f4d4743d0)
#
Next, on the attack machine, we simulate a credential theft attempt by dumping the LSASS memory. Using LimaCharlie, we can monitor sensor activity, review detailed telemetry from the endpoint, and begin crafting detection rules to identify and flag this type of sensitive behavior in real-time.
![simulate attack](https://github.com/user-attachments/assets/f3c8ec23-b3d7-4116-854d-519da684339d)
![lsass dump](https://github.com/user-attachments/assets/456941be-e4fb-446c-920c-cdf33717fde8)
#
Back in LimaCharlie we can see the dump was successful. Now lets build a detection rule from the event 
![successful dump](https://github.com/user-attachments/assets/bb9a91c2-8a0f-4792-bdcc-99bbc8b2b742)

![lsass detection](https://github.com/user-attachments/assets/4a9716e7-cf53-4eeb-a384-20a2b1f4627e)

![build detection rule from event](https://github.com/user-attachments/assets/e0093537-d7ae-4d4e-81b4-2a9893b287a5)

![detection rules](https://github.com/user-attachments/assets/acba9498-f77f-4ace-8151-b8617388138c)

Now we run a quick test on the event to make sure its firing.

![test detecion success](https://github.com/user-attachments/assets/13f5ba61-23b7-4d0e-acf0-46ba1daf3c1f)
#
Now when we run the payload again to see the if the new detection rule works
![detection success!](https://github.com/user-attachments/assets/81118b70-5bf3-4e11-8cef-cab4fc57258e)

![detection sucess 2](https://github.com/user-attachments/assets/66ac153c-c401-433f-8edc-de24ee29eec4)
#
Moving beyond basic detection, we can now use LimaCharlie to create a rule that both detects and blocks attacks originating from the Sliver server. On the Ubuntu machine, we simulate part of a ransomware attack by attempting to delete volume shadow copies — a common tactic used to prevent file recovery. LimaCharlie captures this behavior in its telemetry, allowing us to craft a defensive rule that blocks the attack in real time. Once deployed, the rule effectively stops the Ubuntu machine from executing the same attack again, demonstrating proactive threat prevention in action.
![CREATE SHELL](https://github.com/user-attachments/assets/03e8f37e-5cee-417c-91fb-742208f33676)
![detection final](https://github.com/user-attachments/assets/6320359f-8604-4898-9ae7-0df272411384)
![final detection rule](https://github.com/user-attachments/assets/a9023423-9d01-4994-8055-f8f7f79b0750)
![detection rule works!](https://github.com/user-attachments/assets/1ab3c496-c123-4661-ae0a-0ee2e215fdc4)
![DONE!](https://github.com/user-attachments/assets/e0a2bbb7-d566-4c59-8541-e1277058526e)
