## Details
During your shift as a tier-2 SOC analyst, you receive an escalation from a tier-1 analyst regarding a public-facing server. This server has been flagged for making outbound connections to multiple suspicious IPs. In response, you initiate the standard incident response protocol, which includes isolating the server from the network to prevent potential lateral movement or data exfiltration and obtaining a packet capture from the NSM utility for analysis. Your task is to analyze the pcap and assess for signs of malicious activity.
## Prepare
Dowdload file challenge.
prepare wireshark
### Q1:By identifying the C2 IP, we can block traffic to and from this IP, helping to contain the breach and prevent further data exfiltration or command execution. Can you provide the IP of the C2 server that communicated with our server?
Check all the connnections with ipv4 to our server (statistic -> ipv4)
![image](https://github.com/sulsur/HocTap/assets/93130840/03b67a48-ff69-4cc7-ab82-26f2a17bbbfb)
Flowing image we see 4 connections within 1 single connection (134.209.197.3) which is our server<br>
As you can see their is 4867 Packets (about 5 MiB )between our Server and external IP address Which is the C2 Server :
![image](https://github.com/sulsur/HocTap/assets/93130840/59266aba-556f-4ed4-ab44-0b3c232b594d)<br>
Ans:146.190.21.92
### Q2:Initial entry points are critical to trace back the attack vector. What is the port number of the service the adversary exploited?
I gusess adverary exploit with openwire
![image](https://github.com/sulsur/HocTap/assets/93130840/03dc695b-8087-4950-b790-287e25661717)<br>
From above the image i filter pro.openwire and i see 2 packages.Open the pack1 see dest.port==61616<br>
Ans:61616
### Q3:Following up on the previous question, what is the name of the service found to be vulnerable?
i search "how to exploit openwire" on my browser
![image](https://github.com/sulsur/HocTap/assets/93130840/2cc1e0a7-f889-45f8-83f5-37aaf1c77b23)<br>
Ans: Apache ActiveMQ
### Q4:The attacker's infrastructure often involves multiple components. What is the IP of the second C2 server?
Their are another 2 connection with our server (128.199.52.72) and (84.239.49.16) .
![image](https://github.com/sulsur/HocTap/assets/93130840/eba58388-67b7-4586-a390-68acae3392c1)
Filter pcap with ip = 128.199.52.72
![image](https://github.com/sulsur/HocTap/assets/93130840/e293892b-8762-4a4b-b00e-11e06f25f919)
![image](https://github.com/sulsur/HocTap/assets/93130840/24b71347-7091-4898-808e-365913a750ea)
As you can see our server is requesting a resource from that external address <br>
to make sure we can go to packet 34 and follow TCP stream<br>

![image](https://github.com/sulsur/HocTap/assets/93130840/4efac950-0251-4a55-8702-4ea41b791475)
as you can see ELF magic bytes , which represent an executable file , so i was sure this is are C2 server <br>
Filter pcap with ip =84.239.49.16
![image](https://github.com/sulsur/HocTap/assets/93130840/06bd7dd2-2174-4041-beaa-9c7f3b0d4744)
Ans:128.199.52.72
### Q5:Attackers usually leave traces on the disk. What is the name of the reverse shell executable dropped on the server?
go to packet 11,20,.. and follow TCP stream

![image](https://github.com/sulsur/HocTap/assets/93130840/b7fb41c9-3ea9-4357-8b8b-c5830ef92d4d)
Ans:docker
### Q6:What Java class was invoked by the XML file to run the exploit?

![image](https://github.com/sulsur/HocTap/assets/93130840/25a86171-3aa9-40b3-a6f8-99fd0d35e77d)
Ans:java.lang.ProcessBuilder
### Q7:To better understand the specific security flaw exploited, can you identify the CVE identifier associated with this vulnerability?
Search browser
Ans:CVE-2023-46604
### Q8:What is the vulnerable Java method and class that allows an attacker to run arbitrary code? (Format: Class.Method)
Ans:BaseDataStreamMarshaller.createThrowable
