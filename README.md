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
