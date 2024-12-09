<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

<h1>Inspecting Traffic Between Azure Virtual Machines</h1>
In this lab, I analyze various types of network traffic to and from Azure virtual machines using Wireshark and experiment with network security groups. <br />



<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop Connection
- Command-Line Tools
- Various Network Protocols (SSH, RDH, DNS, HTTP/S, ICMP)
- Wireshark (Protocol Analyzer)

<h2>Operating Systems Used </h2>

- Windows 10 Pro (21H2)
- Ubuntu Server 20.04

<h2>The Set-Up</h2>

Within Azure, I created two VMs within the same virtual network to ensure that they are able to communicate with each other. One VM will have Windows 10 Pro while the other uses Ubuntu. The Windows VM will connect to the other via the command line/PowerShell. 

![Screenshot 2024-12-04 222312](https://github.com/user-attachments/assets/841d8a0c-0735-4a3e-a263-75eacaeb26ba)

<h2>Actions and Observations</h2>

Using Remote Desktop Connection, I connect to the Windows VM using its public IP address. From there, I installed Wireshark in order to begin inspecting traffic. 

![Screenshot 2024-12-04 224146](https://github.com/user-attachments/assets/d3721bd8-39d7-42d3-a2f1-61c720888a25)

![Screenshot 2024-12-04 224302](https://github.com/user-attachments/assets/1c4ccb9d-2dc3-48b9-8027-c78e0ca185f8)

Within Wireshark, I filtered for ICMP (Internet Control Message Protocol) traffic and opened PowerShell to execute a command called ping. Ping utilizes ICMP, which is used by devices in a network to communicate problems within data transmition. I used ping to see if I can communicate with the Ubuntu VM using its private IP address and with google.com. Afterwards, I used a perpetual ping to the Ubuntu VM in order to see how network security groups work. I executed the perpetual ping with the command: ping -t (ip address).

![Screenshot 2024-12-04 224632](https://github.com/user-attachments/assets/074ee6fb-4e1d-4e2d-935c-32b858cca96a)

![Screenshot 2024-12-04 225353](https://github.com/user-attachments/assets/c212f1f1-5104-47f8-8444-cffb5d68437a)

Within the Azure portal, I opened the networking settings for the Ubuntu VM and added an inbound security rule to block ICMP traffic. I make sure to have the priority higher than SSH (300) to ensure the rule applies first. 

![Screenshot 2024-12-04 225636](https://github.com/user-attachments/assets/2be234f0-8908-4bdb-bb0b-053fcaeaede4)

Upon returning to the Windows VM, I notice that the ICMP traffic is blocked now that the inbound security rule is in place. After changing the rule to allow traffic again, the perpetual ping resolves without timing out. 

ICMP BLOCKED
![Screenshot 2024-12-04 225805](https://github.com/user-attachments/assets/0b058b5f-b0d2-4961-8243-0d15481332ee)

ICMP RE-ALLOWED
![Screenshot 2024-12-04 230133](https://github.com/user-attachments/assets/50ad544e-8069-4b05-a961-1d8835c8566b)

Next, I chose to examine SSH traffic. I logged in to the Ubuntu server via PowerShell with the ssh command. With Wireshark, I filtered the traffic with tcp.port == 22. While logged into the Ubuntu server, my session is logged in Wireshark with each command I use.

![Screenshot 2024-12-04 231107](https://github.com/user-attachments/assets/40ec2342-3b03-4b8c-b48e-39342bf41e0b)

![Screenshot 2024-12-04 231628](https://github.com/user-attachments/assets/662bc614-124d-40d9-a4b5-a91bd9565506)

After examining SSH traffic, I exited the Ubuntu server in order to filter for DHCP traffic. To see it in action, I decided to attempt to issue a new IP address from my VM. The command ipconfig /renew will attempt to issue the new IP address and will temporarily disconnect me for a few seconds. After reconnecting, the resulting traffic is shown in Wireshark.

![Screenshot 2024-12-04 233132](https://github.com/user-attachments/assets/4471ddfe-3ed4-4c9c-b54b-90adb112e0d4)

To observe DNS traffic, I used the filter udp.port == 53 and the command nslookup. I wanted to see the results that are from looking up google.com and disney.com, two very popular sites. 

![Screenshot 2024-12-04 233451](https://github.com/user-attachments/assets/dd10d9f9-8146-420d-ade1-505041c08952)

To finish my lab, I decided to observe RDP traffic. The filter for Wireshark is tcp.port == 3389. There is non-stop traffic because RDP is constantly showing me a live stream from one computer to another (in my case, my computer accessing the VM that is hosted on Azure) and thus traffic is always transmitted. 

![Screenshot 2024-12-04 233800](https://github.com/user-attachments/assets/4da05fea-4a55-40e7-86fe-da27cd95a47a)

<h2>Lessons Learned </h2>


The purpose of this lab is to observe how different protocols and ports are used in network communication between devices. While it doesn't directly involve troubleshooting, it helps gather valuable information. In troubleshooting, I rely on tools like Wireshark and the command line to analyze traffic flow through ports and protocols. Success requires both familiarity with these tools and an inquisitive mindset!
