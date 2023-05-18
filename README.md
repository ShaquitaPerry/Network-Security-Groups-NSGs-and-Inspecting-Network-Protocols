<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

<h1>Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines</h1>
In this tutorial, we observe various network traffic to and from Azure Virtual Machines with Wireshark as well as experiment with Network Security Groups. <br />


<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Various Command-Line Tools
- Various Network Protocols (SSH, RDH, DNS, HTTP/S, ICMP)
- Wireshark (Protocol Analyzer)

<h2>Operating Systems Used </h2>

- Windows 10 (21H2)
- Ubuntu Server 20.04

<h2>High-Level Steps</h2>

- Create 2 VMs, one running Windows and one running Linux. Remote Desktop to VM-1
- Ping VM-2 from within VM-1. Observe network traffic.
- Alter VM-2 firewall to block incoming traffic via ICMP. Observe network traffic change. 
- Observe SSH, DNS, and RDP traffic on Network.

<h2>Actions and Observations</h2>

<p>
First we will create two Virtual Machines on Azure. One machine will be a Linux machine and the other will be a Windows 10 machine. Both will have two cpus and they must be on the same VNET. Below is a shot of the Network Topology.
</p>

![image](https://user-images.githubusercontent.com/111653930/236491025-53d59445-7bfc-4863-87dc-3e0c18e3cbc6.png)

<br />

<br>
<p>
To begin we will login to VM-1 and download and install <a href="https://www.wireshark.org">WireShark</a>. Wireshark is a free and open-source packet analyzer. It is used for network troubleshooting, analysis, software and communications protocol development, and education. We can then run WireShark and filter traffic by ICMP. ICMP is a network layer protocol that relays messages concerning network connection issues. Ping uses this protocol - ping tests connectivity between hosts. When we filter wirehsark to only capture ICMP packets and ping the private IP address of our VM-2 linux machine we can visually see the packets on wireshark.
</p>

![image](https://user-images.githubusercontent.com/111653930/236505185-929b1b70-892c-4df8-951f-85176c8af952.png)


<br>
<p>
If you expand the "Internet Control Message Protocol" filter and hit "Data" you can see what was actually sent in each ping. 
</p>

![image](https://user-images.githubusercontent.com/111653930/236506627-f1e31060-4045-4f06-8362-dea0d61e91b6.png)


<br>
<p>
Next we will set a perpetual ping from VM-1 to VM-2 using the command "ping -t". While this perpetual ping is happening from VM-1 to VM-2 we will change the firewall of VM-2 to BLOCK ICMP Traffic. To edit VM-2s firewall on Azure go to Network Security Groups -> VM2-nsg -> Inbound Security Rules. This displays a list of rules currently on VM-2. We will create a new rule that says ICMP traffic for ANY destination will be denied. 
</p>

![image](https://user-images.githubusercontent.com/111653930/236509131-f74ff6ae-4dc2-41de-a271-5d16fa7331ea.png)
![image](https://user-images.githubusercontent.com/111653930/236509697-2cfd9070-a3be-47e9-ad3a-1f41575aaaed.png)


<br>
<p>
Once the rule is added on Azure we can check back in on our VM-1 to see our perpetual ping of VM-2 has timed out as a result of blocking ICMP traffic. If we revert this rule to allow traffic we will see on VM-1 that replys come back through.
</p>

![image](https://user-images.githubusercontent.com/111653930/236510574-2dd95b40-983e-49d0-bb9d-6b5ab128a099.png)
![image](https://user-images.githubusercontent.com/111653930/236511359-f41131ab-2a84-4adc-8335-d3d3576c7ac1.png)


<p>
Next we will explore SSH traffic. SSH is essentially like remote desktop without the GUI. Instead of filtering by ICMP we will filter by SSH Traffic. Rather than pinging VM-2 we will SSH into it from VM-1. In Powershell type ssh username@ipaddress. You can see immediatley that WireShark starts to capture packets. 
</p>

![image](https://user-images.githubusercontent.com/111653930/236515356-366ec817-4e94-41e0-8ca0-fb038a5afa3c.png)


<p>
Now we will use wireshark to filter for DHCP. DHCP is the Dynamic Host Configuration Protocol this works on ports 67/68. It is used to assign IP addresses. We can force a renewal of an ip address with the command ipconnfig /renew. This will re-issue our ip address to us.  
</p>

![image](https://user-images.githubusercontent.com/111653930/236552057-ab563cbc-77c0-4113-8176-edecb855a819.png)


<p>
We can also observe DNS traffic. We can use the command nslookup www.google.com which will ask our DNS server what is google.coms IP Address. 
</p>

![image](https://user-images.githubusercontent.com/111653930/236552938-d5c861d0-f41c-46bf-aea9-2e49981d9bec.png)


<p>
Finally, if we sorted for RDP we would see a stream of traffic since we are actively connecting to a VM through Remote Desktop to perform the lab. </p>



