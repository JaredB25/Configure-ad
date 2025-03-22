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

<h2>High-Level Deployment and Configuration Steps</h2>

- Step 1
- Step 2
- Step 3
- Step 4

<h2>Deployment and Configuration Steps</h2>

<p>
<br/>
  
<img width="950" alt="Image" src="https://github.com/user-attachments/assets/4f8a5b79-7078-4373-9226-472cac2b9987" />"/>
</p>
<p>
We will create two virtual machines: one running Windows Server 2022, named DC-1, and the other running Windows 10, named Client1. Both VMs should be connected to the same virtual network (VNet). Next, we'll configure the IP address on the domain controller (DC-1), changing it from dynamic to static. This ensures the client machine can join the domain and use DC-1 as its DNS server.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/95b405bb-18f7-41b9-a56d-a7d94f99f89e"/>
</p>
<p>
Next, we'll RDP into the domain controller and disable the firewall for the domain, private, and public profiles. To do this, right-click the Windows symbol, select "Run," and type wf.msc. In the Windows Defender Firewall window, ensure the firewall is turned off for all profiles.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/1c9f51d9-6d7f-49bf-8f58-ce9700c1e0c1"/>
</p>
<p>
Next, we'll change the DNS server on Client1 to the static private IP address of DC-1 in the networking settings within the Azure portal. After making this change, restart the virtual machines to apply the new DNS configuration.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/36bc91b5-4252-41ae-a25d-e0a79a8822aa"/>
<img src="https://github.com/user-attachments/assets/ef474797-682d-4068-a5fd-3f467233cb9e"/>
</p>
<p>
Following that, we'll RDP into the Client1 virtual machine and attempt to ping DC-1's IP address using PowerShell. This should be successful since we disabled DC-1's firewall, allowing the machine to respond to the ping. We'll use the ipconfig /all command on Client1 to confirm that DC-1 is configured as the DNS server for the virtual machine.
</p>
<br />

<p>
<img src="https://i.imgur.com/oBYt50h.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Next, we'll install Active Directory Domain Services (AD DS) using the Server Manager on the domain controller.
</p>
<br />

<p>
<img src="https://i.imgur.com/1UY0lrq.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
We'll promote DC-1 to a Domain Controller and set up a new forest with the domain name mydomain.com.
</p>
<br />

<p>
<img src="https://i.imgur.com/lRyBbvA.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Next, we'll open Active Directory Users and Computers and create two organizational units, _EMPLOYEES and _ADMINS, by right-clicking mydomain.com, selecting New, and then choosing Organizational Unit.
</p>
<br />

<p>
<img src="https://i.imgur.com/lj5a6an.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
In the Admins OU, we'll create a new user named Ken Doe with the username ken_admin. 
</p>
<br />

<p>
<img src="https://i.imgur.com/lHZsq7U.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Next, we'll add Ken Doe as a Domain Admin. Right-click the user, select Properties, navigate to the Member Of tab, and click Add. In the "Enter object names" field, type Domain Admins and press Enter to complete the process.
</p>
<br />

<p>
<img src="https://i.imgur.com/x1tbaQ1.png height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Now, log out of DC-1 and reconnect using RDP with the credentials mydomain.com\ken_admin and the assigned password. This account will be used for all future logins to DC-1.
</p>
<br />

<p>
<img src="https://i.imgur.com/sWL89qz.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Now, we'll join the domain from the Client1 VM. RDP into the system, right-click the Windows logo, select System, then click Rename this PC (Advanced). Next, click Change, select Domain, and enter the domain name mydomain.com. The VM will restart to apply the changes.
</p>
<br />

<p>
<img src="https://i.imgur.com/wsOjP9T.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Go back to DC-1 and open Active Directory Users and Computers. Create another organizational unit named _CLIENTS under mydomain.com.
</p>
<br />

<p>
<img src="https://i.imgur.com/HgTs346.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Next, log into the Client1 VM as ken_admin. Right-click the Windows logo, select System, then choose Remote Desktop. Click Select users that can remotely access this PC and add Domain Users to allow them RDP access to the VM.
</p>
<br />

<p>
<img src="https://i.imgur.com/DoCgqOP.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1 Log in to DC-1 as ken_admin and open PowerShell ISE as an administrator. Create a new file, paste the script into it, and execute it. Observe as the accounts are created automatically.
</p>
<br />

<p>
<img src="https://i.imgur.com/Ca3ShWL.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
After running the script, open Active Directory Users and Computers (ADUC) and verify that the accounts have been created in the appropriate _EMPLOYEES organizational unit.
</p>
<br />

<p>
<img src="https://i.imgur.com/PWd6tW8.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Lastly, log in to the Client1 VM using one of the user accounts created by the PowerShell script, with the username and default password Password1. After logging in, open PowerShell to verify that you are logged in as one of the script-created users.
</p>
<br />
