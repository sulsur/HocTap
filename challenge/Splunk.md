Write up
Level 1: Finding Attack Servers (20 pts + 15 extra)
BOTSv1 1.1: Scanner Name (5 pts)
Find the brand name of the vulnerability scanner, covered by a green box in the image above.

Search: index="botsv1" sourcetype="stream:http" imreallynotbatman.com
![image](https://github.com/sulsur/HocTap/assets/93130840/bc0a6f89-de3a-4737-b830-c6e0ed020272)

Ans: Acunetix/10.0

BOTSv1 1.2: Attacker IP (5 pts)
Find the attacker's IP address.
![image](https://github.com/sulsur/HocTap/assets/93130840/98efa3bc-307c-467d-b454-76913ac8a896)

Ans: 40.80.148.42

BOTSv1 1.3: Web Server IP (5 pts)
Find the IP address of the web server serving "imreallynotbatman.com".
![image](https://github.com/sulsur/HocTap/assets/93130840/994727ec-c4ea-4114-8e74-dd65a52f9a4b)

Ans: 192.168.250.70

BOTSv1 1.4: Defacement Filename (10 pts)
Find the name of the file used to deface the web server serving "imreallynotbatman.com".
Hints: It was downloaded by the Web server, so the server's IP is a client address, not a destination address.
Remove the filter to see all 9 such events. Examine the uri values.

Phân tích dựa trên client ip
sourcetype="stream:http" imreallynotbatman.com c_ip="40.80.148.42"
Có 2 client ip (Thử nhưng khả năng cao là 40.80.148.42)
![image](https://github.com/sulsur/HocTap/assets/93130840/37852a27-cb2a-41d1-8562-a4fc11c7e822)

Nhưng không may chúng ta đã sai khi phân tích.
Search: sourcetype="stream:http" c_ip="192.168.250.70"
![image](https://github.com/sulsur/HocTap/assets/93130840/7fd0dc4b-b707-4e4e-a171-0fa641e8d5ce)
Ans: /poisonivy-is-coming-for-you-batman.jpeg

BOTSv1 1.5: Domain Name (10 pts)
Find the fully qualified domain name (FQDN) used by the staging server hosting the defacement file.
Hints: Examine the 9 events from the previous challenge. Look at the url values
![image](https://github.com/sulsur/HocTap/assets/93130840/36fe71a8-a71f-4522-87e9-539a5b3ad43c)


Ans: prankglassinebracket.jumpingcrab.com

Level 2: Identifying Threat Actors (20 pts + 30 extra)****
BOTSv1 2.1: Staging Server IP (10 pts)
In Level 1, you found the staging server domain name (used to host the defacement file). Find that server's IP adddress.
Hints: Search for HTTP GET events containing the target FQDN.

Search: index="botsv1" sourcetype="stream:http" jumpingcrab
![image](https://github.com/sulsur/HocTap/assets/93130840/12234e06-8d92-40b3-9ffe-0bda51ec1bc2)

Tìm gói request và chứa chuỗi jumpingcrab
Ans: 23.22.63.114
BOTSv1 2.2: Leetspeak Domain (10 pts)
Use a search engine (outside Splunk) to find other domains on the staging server. Search for that IP address. Find a domain with an name in Leetspeak (like "1337sp33k.com").
Vào dogpile search
![image](https://github.com/sulsur/HocTap/assets/93130840/2850b2ff-7389-4086-b61b-d285aa175079)


![image](https://github.com/sulsur/HocTap/assets/93130840/73c35442-87d9-4a73-afb5-6e95dbd567b5)

Có thể dùng virustotal để kiểm tra
![image](https://github.com/sulsur/HocTap/assets/93130840/1d85b174-929c-4673-84a3-ef8b8c59e7d4)


Ans: po1s0n1vy.com
BOTSv1 2.3: Brute Force Attack (15 pts)
Find the IP address performing a brute force attack against "imreallynotbatman.com".
Hints:Find the 15,570 HTTP events using the POST method.
Exclude the events from the vulnerability scanner.
Examine the form_data of the remaining 441 events.
To make a useful table, add this to your query:
| table _time, form_data

Search: index=botsv1 sourcetype=stream:http dest_ip=192.168.250.70 http_method=POST | stats count by src_ip, form_data, uri
image
Chúng ta có thể thấy ip 23.22.65.114 đang thực hiện gửi POST đến 192.168.250.70 rất nhiều .

Search: index=botsv1 sourcetype=stream:http dest_ip=192.168.250.70 http_method=POST 
| stats count by src_ip, form_data, uri
| stats sum(count) by src_ip
Nhưng khi ta count thì 40.80.148.42 có tới 10341

Search: index=botsv1 sourcetype=stream:http dest_ip=192.168.250.70 http_method=POST src_ip=40.80.148.42
| table form_data
image
Không có events nhưng qua statistics cần lưu ý
image
Search: index=botsv1 imreallynotbatman.com sourcetype="stream:http" src_ip="23.22.63.114" dest_ip="192.168.250.70" http_method="POST" username passwd

image
Trong fied form_data có user, passwd .Có thể khẳng định 23.22.63.114 đang brute force

Ans: 23.22.63.114
BOTSv1 2.4: Uploaded Executable File Name (15 pts)
Find the name of the executable file the attacker uploaded to the server.
Hints: Find the 15,570 HTTP events using the POST method.
Exclude the events from the vulnerability scanner.
Search for common Windows executable filename extensions.

Ta đoán file thực thi có đuôi exe
Search: index=botsv1 sourcetype=stream:http dest_ip="192.168.250.70" http_method=POST form-data *.exe
image
Tìm được 2 file nhưng nghi ngờ 3791.exe hơn
Ans: 3791.exe

Level 3: Using Sysmon and Stream (20 pts + 30 extra)
BOTSv1 3.1: MD5 (10 pts)
In Level 2, you found the name of an executable file the attackers uploaded to the server.
Find that file's MD5 hash.
Hints: Read about Sysmon Event IDs
Find events from Sysmon for process creation.
Examine cmdline to find the correct event.
image

Ans: AAE3F5A29935E6ABCC2C2754D12A9AF0
BOTSv1 3.2: Brute Force (10 pts)
What was the first brute force password used?
Hints: Start with 1:10 sampling.
Find events containing "login".
Find top values of "url".
Examine the "form_data" values to identify the brute force attack.
image

Ans: 12345678

BOTSv1 3.3: Correct Password (10 pts)
What was the correct password found in the brute force attack?
Hints: Find the events with the "form_data" values indicating a login attempt.
There are two different "http_user_agent" values.

Search:

index=botsv1 imreallynotbatman.com sourcetype="stream:http" dest_ip="192.168.250.70" http_method="POST" username passwd 
| rex field=form_data "passwd=(?<passwd>\w+)" 
| stats count by passwd
image

Ans: batman

BOTSv1 3.4: Time Interval (10 pts)
How many seconds elapsed between the time the brute force password scan identified the correct password and the compromised login? Round to 2 decimal places.
Hints: HINT: Find the two events with the correct password in the "form_data" field.

Search: index=botsv1 imreallynotbatman.com sourcetype="stream:http" dest_ip="192.168.250.70" http_method="POST" username passwd  | rex field=form_data "passwd=(?<passwd>\w+)"  | search passwd=batman 
| transaction passwd 
| eval dur=round(duration, 2)
| table dur
image
Ans: 92.17
BOTSv1 3.5: Number of Passwords (10 pts)
How many unique passwords were attempted in the brute force attack?
Hints: Examine http_user_agent values.

image
Ans: 412

Level 4: Analyzing a Ransomware Attack (20 pts + 160 extra)
BOTSv1 4.1: IP Address (5 pts)
What was the most likely IP address of we8105desk on 24AUG2016?
Hints: Search for we8105desk – you find 181,012 events.
Examine the source field – there are 10 values.
Explore stream sources with protocols used in Active Directory logins.
Find events on that day and look at their IP addresses.
image
Ans: 192.168.250.100

BOTSv1 4.2: Signature ID (5 pts)
Amongst the Suricata signatures that detected the Cerber malware, which one alerted the fewest number of times? Submit ONLY the signature ID value as the answer. (No punctuation, just 7 integers.)
Hints: Search for Cerber – you find 21,596 events.
Examine the source field – there are 4 values.
Explore the source type associated with Suricata.
Search: index=botsv1 sourcetype=suricata cerber
image
Ans: 2816763
BOTSv1 4.3: FQDN (15 pts)
What fully qualified domain name (FQDN) does the Cerber ransomware attempt to direct the user to at the end of its encryption phase?
Hints:New process: Aug 2, 2021:
Find events with a sourcetype of suricata
Restrict the search to events with "alert signature" containing "Onion domain lookup"
Restrict the time to within one second of those events
Look in the "stream:dns" events, in the "query" field
Old process:
Examine the five Suricata alerts about Cerber. View them as "raw text" in time order.
Find a time delay and the domain lookup events after it. Note the latest time of those events.
Use the "Date time range" option of Search to narrow the time range to just a few seconds before the Suricata alert. Check to make sure you can still find the Suricata alerts. You may have to adjust the time by am hour or two to compensate for time zone differences.
Search all events in that small time range. Examine Suricata events. Look at dns-related fields.
Search: index=botsv1 src_ip="192.168.250.100" source="stream:dns" NOT query=*.arpa AND NOT query=*.microsoft.com AND NOT query=*.msn.com AND NOT query=*.info AND NOT query=*.local AND query=*.*
image

ip 192.168.250.100 đang query lên 192.168.250.25 bản ghi A dể hỏi ip của cerberhhyed5frqa.xmfir0.win
Ans: cerberhhyed5frqa.xmfir0.win
BOTSv1 4.4: Suspicious Domain (15 pts)
What was the first suspicious domain visited by we8105desk on 24AUG2016?
Hints: Find the Suricata events on that day. There are 86,579 of them.
Examine the src_ip field. Restrict your query to the desired value.
Examine the event_type field. Restrict your query to events that load Web pages. There are 38 of them.
Examine the hostnames visited. There are ten of them. Investigate them with Google and find the one that's known to be malicious.
Search: index=botsv1 src_ip="192.168.250.100" source="stream:dns" NOT query=*.arpa AND NOT query=*.microsoft.com AND NOT query=*.msn.com AND NOT query=*.info AND NOT query=*.local AND query=*.*
image
Kiểm tra các gói ngày 24/8/2016
image
image
Event này ghi lại đang query dns.msftncsi.com (Tên miền "dns.msftncsi.com" thường được sử dụng trong hệ thống Windows để kiểm tra kết nối internet và kiểm tra tính khả dụng của mạng.)
Kiểm tra lần lượt ta thấy
image
Ans: solidaritedeproximite.org
BOTSv1 4.5: VB Script (15 pts)
During the initial Cerber infection a VB script is run. The entire script from this execution, pre-pended by the name of the launching .exe, can be found in a field in Splunk. What is name of the first function defined in the VB script?
Hints: Search for events with both a filename extension for VB script and an .exe extension. There are 16 of them.
Examine the body field. Find the malicious one.
*Search: index=botsv1 sourcetype="xmlwineventlog:microsoft-windows-sysmon/operational" .vbs
| table CommandLine

image
Ans: GNbiPp(Pt5SZ1)
BOTSv1 4.6: Field Length (15 pts)
During the initial Cerber infection a VB script is run. The entire script from this execution, pre-pended by the name of the launching .exe, can be found in a field in Splunk. What is the length in characters of the value of this field?
Hint: Find the length of the Splunk field, not the length of the script itself. This may be helpful.
There are three events with fields that match this description. Use the longest length of those three fields. The longer ones contain the same script, but ampersands are HTML-encoded which makes the field longer.

Search: index=botsv1 sourcetype="xmlwineventlog:microsoft-windows-sysmon/operational" vbs
| eval lencmd=len(CommandLine)
| table _time CommandLine, lencmd
| sort - lencmd
image
Ans: 4490
BOTSv1 4.7: USB key (15 pts)
What is the name of the USB key inserted by Bob Smith?
Hints: Find events with SourceType of WinRegistry
Search for "FriendlyName", as shown here.

Search: index=botsv1 sourcetype="winregistry"host=we8105desk USBSTOR  
| dedup registry_value_data
image
Ans: MIRANDA_PRI
BOTSv1 4.8: Server Name (5 pts)
Bob Smith's workstation (we8105desk) was connected to a file server during the ransomware outbreak. What is the domain name of the file server?
Hints: Examine SMB stream data for Bob's workstation during the outbreak
Find events with a "path" by adding path="*" to the query
The server name resembles Bob's workstation name.
Search:index="botsv1" sourcetype="stream:dns" src_ip="192.168.250.100" dest_ip="192.168.250.20"

image

Ans: we9041srv.waynecorpinc.local
BOTSv1 4.9: IP Address (15 pts)
Bob Smith's workstation (we8105desk) was connected to a file server during the ransomware outbreak. What is the IP address of the file server?
Hints: Search for the server's name. Examine the source of those events. Look for source types that record raw network data and would therefore include IP addresses.
Search: index=botsv1 sourcetype="stream:smb" src_ip="192.168.250.100"
image
Khả năng cao là 192.168.250.20
Kiểm ra dữ liệu out và in của we8105desk(192.168.250.20)
image
Ans: 192.168.250.20
BOTSv1 4.10: PDFs (20 pts)
How many distinct PDFs did the ransomware encrypt on the remote file server?
Hints: Search for .pdf
Restrict your search for the "unknown" app
Find all unique filenames. Remove filenames outside the time range of the attack.
Tổng các file pdf bị ransomeware mã hóa
image
Liệt kê 1 số file pdf bị mã hóa
image

BOTSv1 4.11: Process ID (15 pts)
The VBscript found above launches 121214.tmp. What is the ParentProcessId of this initial launch?
Hints: Search for 121214.tmp – you find 190 events.
Examine the EventDescription field and focus on the one most closely related to the question.
Examine the CommandLine field.

image
Ans: 3968
BOTSv1 4.12: Text Files (15 pts)
The Cerber ransomware encrypts files located in Bob Smith's Windows profile. How many .txt files does it encrypt?
Hints: Find all events including.txt and find the path to Bob's Windows profile.
Add Bob's Windows profile path to the search. To search for a backslash, enter two backslashes.
Examine the file paths and remove the ones outside Bob's profile.

Search: index=botsv1 bob.smith sourcetype="XmlWinEventLog:Microsoft-Windows-Sysmon/Operational" TargetFilename="C:\\Users\\bob.smith.WAYNECORPINC\\*.txt" 
| stats dc(TargetFilename)
image
Ans: 406
BOTSv1 4.13: File Name (15 pts)
The malware downloads a file that contains the Cerber ransomware cryptor code. What is the name of that file?
Hints: Search for HTTP downloads from the Cerber-related domain you found above.
The filename has a surprising extension. Research that filename outside Splunk to verify that it's related to Cerber.

image
Ans: /mhtr.jpg
BOTSv1 4.14: Obfuscation (10 pts)
Now that you know the name of the ransomware's encryptor file, what obfuscation technique does it likely use?
Hints:

Research the file using online resources, outside Splunk, to find this.
Ans: steganography

Select a repo
