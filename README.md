<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />


<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)


<h2>Deployment and Configuration Steps</h2>
<p>
For this project, we'll need two virtual machines. A Windows server that will be the domain controller, and a regular Win 10 machine that we'll add to our domain being created. First, let's create the Domain Controller VM (Windows Server 2022) named DC-1.
</p>
<p>
<img src="https://github.com/tsiensesvendomingos/configure-ad/assets/138411730/12585222-c642-4efb-b73e-28a6f3a74957" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
Next, we'll create the Client VM (Windows 10) named Client-1. Again this will be the machine used to demonstrate that the multiple users we create can sign in to the same PC. Also, be sure to use the same Resource Group and Vnet that was created in the previous step. 
</p>
<p>
<img src="https://github.com/tsiensesvendomingos/configure-ad/assets/138411730/261f6ff8-2b05-4c3f-a69a-c4a5e1a941de" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
For the Client PC to find the Domain every time without fail, we have to set the Private IP address to remain the same as leaving it on a "dynamic" IP address will mean that the private IP address will change every so often for security. To ensure it stays the same, we will set the Domain Controller's NIC Private IP address to be static.
</p>
<p>
<img src="https://github.com/tsiensesvendomingos/configure-ad/assets/138411730/c05a2a50-8634-4bcb-b8f9-c060319b51f2" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
Be sure that both VMs are in the same Vnet. We can ensure this by checking the topology with Network Watcher within Azure.
</p>
<p>
<img src="https://i.imgur.com/qoa6Ogg.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
Login to Client-1 with Remote Desktop and open a command prompt line to attempt to ping DC-1's private IP address using the command "ping -t (private IP address)" The -t makes this ping command perpetual, meaning it'll ping continuously. We should see it fail upon attempt due to the default firewall settings on the server machine.
</p>
<p>
<img src="https://i.imgur.com/7WiZnXa.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
Let's change that by logging in to the Domain Controller VM (DC-1) and enabling ICMPv4 in on the local Windows defender firewall.  
</p>
<p>
<img src="https://i.imgur.com/5zjqHgk.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
<img src="https://i.imgur.com/sGYvdEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
Back at the Client-1 VM, we should see that if enabled correctly, the pings succeeded.  
</p>
<p>
<img src="https://i.imgur.com/aina7fg.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
Let's go back to DC-1 to install Active Directory Domain Services and start the promotion of this system to it being a Domain Controller. 
</p>
<p>
<img src="https://i.imgur.com/v96ghkg.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
Once Active Directory Domain Services is installed successfully, we should see an exclamation mark next to the flag on the top right of the Server Manager window. There we should see a post-deployment configuration task allowing us to promote this server to a Domain Controller. 
</p>
<p>
<img src="https://i.imgur.com/sKzZmQi.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
Setup a new forest as anything that you can remember. I did avyaktrout.com. 
</p>
<p>
<img src="https://i.imgur.com/DDR3J40.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
Restart and then log back into DC-1 as user.  
</p>
<p>
<img src="https://i.imgur.com/T7E8uTc.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
In Active Directory Users and Computers, create an Organizational Unit called _EMPLOYEES and another one called _ADMINS.  
</p>
<p>
<img src="https://i.imgur.com/WCdOsvN.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
<img src="https://i.imgur.com/gQIWMqA.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
Create a new employee named "Jane Doe" with the username of "jane_admin". 
</p>
<p>
<img src="https://i.imgur.com/dXpSmHd.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
Add jane_admin to the Domain Admins Security Group. 
</p>
<p>
<img src="https://i.imgur.com/EBqKWdA.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
Log out/close the Remote Desktop connection to DC-1 and log back in as “avyaktrout.com\jane_admin”. Use jane_admin as your admin account from now on. 
</p>
<p>
<img src="https://i.imgur.com/phaYZHS.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
From the Azure Portal, set Client-1's DNS settings to the DC's Private IP address.  
</p>
<p>
<img src="https://i.imgur.com/FgY17Ww.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
From the Azure Portal restart Client-1. Login to Client-1 as the original local admin (labuser) and join it to the domain. The computer will restart.  
</p>
<p>
<img src="https://i.imgur.com/DAZm8Sq.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
Login to the Domain Controller and verify Client-1 shows up in Active Directory Users and Computers inside the "Computers" container on the root of the domain. Create a new Organizational Unit named _CLIENTS and drag Client-1 into there. 
</p>
<p>
<img src="https://i.imgur.com/3RWEI9S.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
Log into Client-1 as avyaktrout.com\jane_admin and open system properties. Click Remote Desktop. Allow "domain users" access to remote desktop. You can now log into Client-1 as a normal, non-administrative user now. Normally you'd want to do this with Group Policy that allows you to change MANY systems at once (maybe a future lab).  
</p>
<p>
<img src="https://i.imgur.com/lOHuuw4.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
Login to DC-1 as jane_admin. Open PowerShell_ise as an administrator. Create a new File and paste the contents of this script (https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1) into it. 
</p>
<p>
<img src="https://i.imgur.com/FFgc5or.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
Run the script and observe the account being created. 
</p>
<p>
<img src="https://i.imgur.com/Mwwce0e.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
When finished, open Active Directory Users and Computers and observe the accounts in the appropriate OU and attempt to log into Client-1 with one of the accounts (take note of the password in the script). 
</p>
<p>
<img src="https://i.imgur.com/gynz8gn.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
<img src="https://i.imgur.com/VZp5YQZ.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
<img src="https://i.imgur.com/CO7scI3.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
And now that we're done don't forget to clean up your Azure environment so that you don't incur unnecessary charges.  
</p>
<br />
