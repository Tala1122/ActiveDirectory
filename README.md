# Setup a Basic Home Lab Running Active Directory
What you'll need: 
- [Oracle Virtual Box](https://www.virtualbox.org/wiki/Downloads)
- [Windows Server 2019 ISO](https://www.microsoft.com/en-us/evalcenter/download-windows-server-2019)
- [Windows 10 ISO](https://www.microsoft.com/en-us/software-download/windows10ISO)
- Powershell Script

![image](https://github.com/Tala1122/ActiveDirectory/blob/main/image)
**Download Materials**\
First you will need to download the ISO images for the server and the client computer as well as download Virtual Box. 

**Create Server VM**\
Create a new VM in Virtual Box, we can name it: "DC", version: "Other Windows 64 bit". Then assign memory and create a hard disk. Go to Settings -> Network. Click on Adapter 1 and make sure it is attached to NAT. Click on Adapter 2 and make sure it is attached to Internal Network. 
Then create the VM and select the Server ISO image you downloaded and then start it. 
After you start the server VM, you can run through the setup and create a user and password. 
Open Settings -> Network -> Ethernet -> Change Adapter Options.
There should be two things that you can see, right click status on both of them to check which one is the Internal NIC and which one is connected to the Internet. Then you can rename them accordingly. For the Internal NIC, right click on it and select Properties. Double click IPv4 and fill it with the following information:\
IP: 172.16.0.1\
Mask: 255.255.255.0\
DNS: 127.0.0.1

**Add Active Directory Domain Services**
Click on Server Manager Dashboard -> Add roles & features and click next until you can select Active Directory Domain Services and then install it. 
After it's installed click on Deployment configuration -> Add a new forest and enter "mydomain.com". Then you can choose a password and install it, the VM will restart.

After it restarts, search for Windows Administrative tools in the Menu, and then select Active Directory users & computers. Right click on "mydomain.com" -> New -> Organizational Unit and name it "Admins". After that is created, right click on Admins and create a new user. You should fill it out with all the information. Then sign out and try to login again using the admin credentials you just created.

**Add RAS/NAT**
Click on Server Manager Dashboard -> Add roles & features and click next until you can select Remote Access and choose Routing and then install it. After that click on Tools -> Routing & Remote Access -> DC -> configure and enable -> pick NAT and pick the public interface INTERNET.

**Add DHCP**
Click on Server Manager Dashboard -> Add roles & features and click next until you can select DHCP and install it. After that click on Tools -> DHCP -> IPv4 -> view scope and enter the details below:
Start: 172.16.0.100\
End: 172.16.0.200\
Length: 24\
Mask: 255.255.255.0\
Select Yes, configure DHCP options and enter IP: 172.16.0.1 and click ADD. Then click Yes, activate scope and finish. After that you will need to right click the DHCP server and select Authorize then do it again and select Refresh.

**Run Powershell Script**
Now, you can open the powershell script that you got from this repository and run it in Powershell ISE. You will need both files names.txt and the script. After it is finished running you can see that all the new users are created and added to the USERS group in the Active Directory.

**Create Client VM**
Create a new VM in Virtual Box, name it "Client", version: "Windows 10 64 bit". Select Network -> Adapter1 and make sure it is attached to the Internal Network. Select the Windows 10 ISO you have downloaded and then start the VM. After going through the user config, you can open a command prompt and run ipconfig to see that the client is connected to the DC network and then you can also ping www.google.com and see that the VM is also connected to the Internet. 
Go to System -> Rename this PC (Advanced) -> Change and you can rename the machine to "Client" and under member of: "mydomain.com" You can then enter the domain admin account. 
Now the machine is added to the Domain we created.

If we go back to the server VM, we can see that there is a new lease added under DHCP for the client machine and it will also show up in the Active Directory users & computers.

You can now log into the client machine with any of the users that were created by the script! Our home network is set up!


