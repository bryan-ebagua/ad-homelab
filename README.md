# ad-homelab
First Step: Setting up an isolated private network
This is done to prevent the DHCP(Dynamic Host Configuration Protocol) Server from assigning IP addresses to my phone, printer, or other devices on my home Wi-Fi.
To start, I downloaded the evaluation desktop version of Windows Server. I'm not super experienced with powershell so I wanted to hold off on using pure CLI for now.  I created a virtual machine in VMware, set max disk size to 60 GB, then went to hardware, network adapter, and created a LAN segment named AD-Lab

<img width="975" height="988" alt="image" src="https://github.com/user-attachments/assets/3d638dc0-3dcf-453b-a961-e2b243de38a7" />

I also assigned it 4 GB of RAM. 
Got this error message while trying to spin up Windows Server

<img width="975" height="754" alt="image" src="https://github.com/user-attachments/assets/edb7d856-3a72-40ff-ab0d-28734029b449" />

Fixed by disabling VMware easy install (removed Ghost Floppy Drive)

<img width="975" height="997" alt="image" src="https://github.com/user-attachments/assets/e4641695-42ae-49ee-a690-a5ea365c512e" />

Installing Windows Server

<img width="975" height="699" alt="image" src="https://github.com/user-attachments/assets/f5090313-b45c-4aaa-9e71-8c0d99d4e1ca" />

Set up  static IP address so clients can find the server 
Server Manager > Local Server > Ethernet0 > IPv4 > right click > properties, 

<img width="975" height="1046" alt="image" src="https://github.com/user-attachments/assets/98948e79-5b35-4a44-b4c9-413ee1814e44" />

Subnet mask = first three octets identify the network, last octet specifies the device. 
Preferred DNS server: 127.0.0.1 (Note: This is the "loopback" address. Because this server is going to become the Domain Controller, it will also act as the primary DNS server for the whole lab)
Verified successful configuration using command prompt

<img width="975" height="604" alt="image" src="https://github.com/user-attachments/assets/54cf0378-8824-4888-bb1a-e9bee7853427" />

Next I installed Active Directory services and promoted the VM to a domain controller

<img width="975" height="794" alt="image" src="https://github.com/user-attachments/assets/7eba5a8f-ddaf-4d8b-aa83-dabf39ad2c5c" />

VM has successfully been promoted!  

<img width="975" height="745" alt="image" src="https://github.com/user-attachments/assets/4a83f5d1-163d-4c1e-8315-c4f09091812b" />

Next install DHCP role via server manager

<img width="975" height="707" alt="image" src="https://github.com/user-attachments/assets/500765bd-5f3c-493b-a7aa-a0f4490bef2c" />

Creating DHCP scope

<img width="830" height="683" alt="image" src="https://github.com/user-attachments/assets/8352da31-2e67-4226-923a-671a81e631bf" />

Domain Controller is now fully functional. It is running Active Directory, hosting DNS, and has an active DHCP server waiting to hand out IP addresses to new machines.
Next step is to spin up a Client VM. I’m using Windows 11. Set the LAN segment to the same as the Server VM

<img width="975" height="1008" alt="image" src="https://github.com/user-attachments/assets/479eddad-c7a1-402b-8769-2e146f53b60e" />

This command is inside the windows 11 client vm showing that it received an IP address from the DHCP server 
Tested connection by pinging the server

<img width="858" height="367" alt="image" src="https://github.com/user-attachments/assets/8b193b6c-176d-4190-b043-1af903275373" />

Go to advanced system settings and under computer name change the domain to the root domain name created earlier

<img width="975" height="771" alt="image" src="https://github.com/user-attachments/assets/dd54e0b6-26f4-4583-8581-ec854bc55338" />

Verifying that my client has been added to the domain 

<img width="975" height="686" alt="image" src="https://github.com/user-attachments/assets/d1b5cb93-b2a5-4b91-8f5f-c3d89671a1fd" />

Next on the server VM, I create a CSV file simulating a list of new hires from an HR department (named after basketball players for fun). I then wrote a powershell ISE script to automate the creation of new users and their accounts in Active Directory

<img width="975" height="778" alt="image" src="https://github.com/user-attachments/assets/f8b4c805-385c-49de-a0b2-8d95af6fd039" />

After running this script, the “employees” are now in my default users folder. I created a new Organizational Unit called NBA Employees and moved the players into it.

<img width="975" height="628" alt="image" src="https://github.com/user-attachments/assets/825fc849-97cc-41f7-a3d6-a4a77fa90557" />

Created a Group Policy that restricts users from accessing the Control Panel and applied it to the Organizational Unit. Tested by logging into Lebron James’ account and trying to open settings and control panel. Both were blocked. 

<img width="975" height="747" alt="image" src="https://github.com/user-attachments/assets/d032cb20-f657-4fbd-bd41-146c95a18fd8" />

In a real life scenario, this would be done before adding users to the group. Next step is to build an osTicket helpdesk server. I used the newest release of Ubuntu Server, 26.04. Because this VM needs to access the Internet, it needs two network adapters. The first one is NAT, which allows it to use my laptop’s IP to connect to the internet. The second is the same AD-lab LAN segment from before. This is what the CLI looks like after setup. I also installed openssh server, allowing me to enter commands to the Ubuntu Server VM from my laptop’s terminal instead of the one inside VMware.

<img width="975" height="848" alt="image" src="https://github.com/user-attachments/assets/1211a261-b90f-4fa6-b7f4-908047baa0f7" />

the bash command ip a lists my network interfaces. The 1st one is the loopback interface, the 2nd one is VMware’s NAT, and the 3rd one is an IP address from the windows server I  configured before. Using the first IPv4 address under ens33, I can use ssh to connect to the server from my laptop’s terminal.

<img width="975" height="591" alt="image" src="https://github.com/user-attachments/assets/942fff38-507b-4839-85f7-ebc98cdae347" />

I then install Apache, MariaDB, and PHP. Apache is the web server that will host the osTicket webpage. MariaDB is an open source database that will store the helpdesk tickets, user accounts, and system settings. PHP is the language that osTicket is written in. I have to install it and some other extensions that osTicket depends on. After each install, I enter the command 
sudo systemctl enable applicationName
This command makes it so the service will automatically start when the server boots.
By entering the IP address from earlier in my Brave browser search bar, I can verify that the Apache server is live. 

<img width="975" height="521" alt="image" src="https://github.com/user-attachments/assets/50b3447d-045f-440b-bf90-a3b6ae8d4ed6" />
Next step is to create the database and a user account that I will use to read and write data. I use “sudo mysql” to open mariaDB, and then run the following SQL queries:
	CREATE DATABASE osticket_db;
This, shockingly enough, creates a new database called osticket_db;
	CREATE USER 'osticket_user'@'localhost' IDENTIFIED BY 'LabPassword123!';
This creates a new user account to access the database. Localhost means the user has access only from the machine where the MySQL server is running. In this case, that means that the user can access the database from the Ubuntu Server VM, or by using SSH to log into the Ubuntu Server.
	GRANT ALL PRIVILEGES ON osticket_db.* TO 'osticket_user'@'localhost';
This grants the new user all privileges on the current level, including data manipulation, structure changes, advanced features, and administration. The only privileges not included are the ability to grant their privileges to others, and the ability to impersonate, or proxy as another user.
	FLUSH PRIVILEGES;
This makes the server reload the user permissions from the system database into memory. It ensures the server discards the old cache and reads the current state of the database, so any manual changes will be applied immediately
	EXIT;
This exits the database and returns me to the Ubuntu CLI. When I get there I run sudo apt install wget unzip -y. This installs tools to download and unzip files. I can then use wget to download the osTicket installation zip file, and then use unzip to extract the zip file into a new folder called osticket. Folllowing this, I move the osticket files into a specific folder  called /var/www/html/osticket so that Apache can read them. I can then delete the deault “It works!” page. osTicket also requires a specific configuration file to connect to the database I created. They provide a template for the configuration file, so I can copy  it and rename it to ost-config.php using the following command:
	sudo cp /var/www/html/osticket/include/ost-sampleconfig.php /var/www/html/osticket/include/ost-config.php
Finally I can grant the web server full ownership of the osTicket files so it can make changes when I configure it in the browser. I do so using these commands
sudo chown -R www-data:www-data /var/www/html/osticket
sudo chmod -R 755 /var/www/html/osticket
The 755 is equal to rwxr-xr-x
After all these commands I open my browser again and go to https:// 192.168.12.129/osticket, which should lead me to the osTicket graphical installer page, but instead takes me to a page full of code

<img width="975" height="809" alt="image" src="https://github.com/user-attachments/assets/5918f417-b5b3-482e-924d-6152c52c8080" />

It seems this is because Apache can’t interpret the PHP code that osTicket is written in natively. I have to install a module that translates PHP into a form that is readable to Apache.

<img width="747" height="84" alt="image" src="https://github.com/user-attachments/assets/8faeebfe-81cf-4e9d-a329-fa2e23de1cb6" />

Then I restart the apache service, which forces Apache to load the new module

<img width="975" height="45" alt="image" src="https://github.com/user-attachments/assets/28c88063-1d28-45b2-8a6f-7a93590d2a1d" />
<img width="975" height="808" alt="image" src="https://github.com/user-attachments/assets/ceb64dae-552b-40ca-8c52-486d7dfe34a8" />

Now I got this error. This would indicate that apache is down, but when I check its status (sudo systemctl status apache2), it says active

<img width="975" height="465" alt="image" src="https://github.com/user-attachments/assets/2b93f50a-0af5-4cc6-8170-8515270782c9" />

The issue was that I haven’t installed an SSL certificate on my linux server, so apache is only listening for HTTP, not HTTPS. When I switched to HTTP, I was hit by the wall of code again. 
After doing some research, Apache on the newest version of Ubuntu uses a traffic-handing system called mpm_event that isn’t compatible with the PHP translator from before. I have to swap apache’s traffic handler to the older mpm_prefork, which is compatible with PHP. I do that with the following commands:
	sudo a2dismod mpm_event
Disables the current traffic handler that is having conflicts
	sudo a2enmod mpm_prefork
Enables the older traffic handler that should work
	sudo a2enmod php*
Enable the PHP translator
After restarting apache once more and reloading the browser page I can finally see the graphical installer
<img width="975" height="754" alt="image" src="https://github.com/user-attachments/assets/8f67d7a3-76e5-4e1e-89a2-08f331a80ab6" />
