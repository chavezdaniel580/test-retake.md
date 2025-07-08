![image](https://github.com/user-attachments/assets/27610c47-d782-4adc-90e5-7b361dc47e5b)
![image](https://github.com/user-attachments/assets/5dc39bb7-7d68-4011-8131-647af32eeb50)
![image](https://github.com/user-attachments/assets/bc5265b5-5c44-4e92-b0d8-5e9019e3ff52)
![image](https://github.com/user-attachments/assets/d128a02d-5819-4392-b987-7d9de1d049ae)


	3. Destination.ip: 104.53.222.103
	Search query form.exe
	
	4. Yamil file just input selections into kibana manually.
	Process.executable *java*
	Message.keyword *Retrieve*
	-if youre gonna do the script make sure spacing is correct. 
	-winlog.event_id: 1 and process.executable: *javaw* OR message.keyword: *Retrieve*
	-fields (agent.name) OR  (host.name)
	
	5. Move to \Users\trainee\Desktop and run script.
	-open script in ISE
	- .\QueryEvents.ps1
	
	6. Search oihgh.ps1
	Search 104.53.222.103 and 172.16.5.6
	Add fields (host.ip) (source.ip) (destination.ip)
	
	7. Dpkg -l
	(what was first package)
	
	8. 
	9. 
	10. 
	11. Type=PATH ……… name="/etc/passwd" (what directory was passed through the system?)
	12. sy
![image](https://github.com/user-attachments/assets/e4aab514-8ff3-43a1-92b1-baa6c1176bcf)

	13. Sudo su
	Cat may05_logs.log.1
	Event code: 200 bytes: 8030
 
	14. Sudo su
	Cd desktop
	Bash net.sh
	104.53.222.102:1337 - 104.53.222.103:47696 (client)
	Through which port does the .102 communicate
	
	15. Sudo su /usr/bin/rkhunter -c -sk
	Wait till end
	
	16. Netstat -anop | grep nc
	Ps -elf | grep nc 
	Systemctl status | grep -I nc
	17. Sudo su 
	Cd ./var/www
	Ls
	Cd html 
	ls



