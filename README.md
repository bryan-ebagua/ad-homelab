# ad-homelab
First Step: Setting up an isolated private network
This is done to prevent the DHCP(Dynamic Host Configuration Protocol) Server from assigning IP addresses to my phone, printer, or other devices on my home Wi-Fi.
To start, I downloaded the evaluation desktop version of Windows Server.  I created a virtual machine in VMware, set max disk size to 60 GB, then went to hardware, network adapter, and created a LAN segment named AD-Lab

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
Preferred DNS server: 127.0.0.1 (Note: This is the "loopback" address. Because this server is going to become the Domain Controller, it will also act as the primary DNS server for the whole lab
Verified successful configuration using command prompt

<img width="975" height="604" alt="image" src="https://github.com/user-attachments/assets/54cf0378-8824-4888-bb1a-e9bee7853427" />
