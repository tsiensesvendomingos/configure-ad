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
<img src="https://github.com/tsiensesvendomingos/configure-ad/assets/138411730/ebc4950d-4024-4756-9c15-0bb373ff7b0c" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
We will want to set up a new forest to load users into that will allow them to sign into Client-1 once we join that machine to our newly created domain. Set the root domain name to anything that you can remember for the purpose of this project. I made up the name domintrio.com. 
</p>
<p>
<img src="https://github.com/tsiensesvendomingos/configure-ad/assets/138411730/bf9f3ff6-d649-46db-a9a6-ebb9ccdb47d1" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
At the end of the installation, the VM will restart. Due to us starting the domain, our login information will also change, adding our domain name to our login as a result. Because of this, our initial Remote Desktop Connection will close, and we will have to log back in using our domain credentials.  
</p>
<p>
<img src="https://github.com/tsiensesvendomingos/configure-ad/assets/138411730/3ad3d463-9418-424e-9fa9-206d5931cc97" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
In Active Directory Users and Computers, create an Organizational Unit called _EMPLOYEES and another one called _ADMINS.
</p>
<p>
<img src="https://github.com/tsiensesvendomingos/configure-ad/assets/138411730/5c18a7b5-0491-4228-9a94-91e77562fffe" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
<img src="https://github.com/tsiensesvendomingos/configure-ad/assets/138411730/14628138-8b45-4cea-9442-cf112aa7492c" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
<img src="https://github.com/tsiensesvendomingos/configure-ad/assets/138411730/0eca2912-1d6b-468f-a64a-696ded1b0dd2" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
Let's create a new employee named "Jane Doe" with the username of "jane.admin". 
</p>
<p>
<img src="https://github.com/tsiensesvendomingos/configure-ad/assets/138411730/a9447afc-9295-45c1-b926-73b16a70ff22" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
Be sure to set a password that you can remember. Also, make sure that for the purpose of this project, uncheck "User must change password at next logon" and check "Password never expires"
</p>
<p>
<img src="https://github.com/tsiensesvendomingos/configure-ad/assets/138411730/5b024b45-7a0c-4373-8b57-5c199fb495c3" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
Add jane.admin to the Domain Admins Security Group. 
</p>
<p>
<img src="https://github.com/tsiensesvendomingos/configure-ad/assets/138411730/9a8915b3-ac3c-4678-a31b-b5f6492e86fe" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
As we want to simulate working as an actual admin, we will now log out of the original user we created when creating the VM and sign in using Jane's domain admin credentials. 
</p>
<p>
<img src="https://github.com/tsiensesvendomingos/configure-ad/assets/138411730/c04675ff-dd94-4a9f-b619-cc16ff30530a" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
While we have that going, from the Azure Portal on our main PC, set Client-1's DNS settings to the DC's Private IP address.  
</p>
<p>
<img src="https://github.com/tsiensesvendomingos/configure-ad/assets/138411730/396f3bd2-2f96-4fdb-b11b-829cd0d53b19" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
We are now ready to add our Windows 10 VM to our domain. To start, let's restart the VM from the Azure Portal.
</p>
<p>
<img src="https://github.com/tsiensesvendomingos/configure-ad/assets/138411730/955f7985-6ed7-482a-af05-883f33a0b78c" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
Reestablish a Remote Desktop Connection to VM "Client-1" with the original logon credentials used to create the VM. (In our case, "labuser.") Once logged in, we can go to Settings -> About -> Rename this PC (advanced). Once there, hit "change..." and then toggle "Domain" under "Member of" and type in our created domain. (domintrio.com)
</p>
<p>
<img src="https://github.com/tsiensesvendomingos/configure-ad/assets/138411730/4861cecf-53fd-438d-99af-57e28e74c847" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
When clicking "OK," a Windows Security prompt will pop up asking for a domain account with permission to join our domain. Use Jane's domain credentials to sign in. This will cause the remote connection to disconnect briefly. Upon the remote connection being reestablished, you should see a pop-up window, welcoming you to the domain. You will then have to restart the VM to have the changes take effect. You can do so from the power menu in the Start menu.
</p>
<p>
<img src="https://github.com/tsiensesvendomingos/configure-ad/assets/138411730/8062bba1-a26d-48bd-9a79-75dcd07476e4" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
Login to the Domain Controller and verify Client-1 shows up in Active Directory Users and Computers inside the "Computers" container on the domain's root. Create a new Organizational Unit named _CLIENTS and drag Client-1 into there. 
</p>
<p>
<img src="https://github.com/tsiensesvendomingos/configure-ad/assets/138411730/e205395b-da40-4544-8355-092e4e1b64ba" height="80%" width="80%" alt="Disk Sanitization Steps"/>
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
