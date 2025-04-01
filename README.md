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
- Command Prompt 

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>Deployment and Configuration Steps</h2>

<p>
<br/>
  
<img width="950" alt="Image" src="https://github.com/user-attachments/assets/4f8a5b79-7078-4373-9226-472cac2b9987" />"/>
</p>
<p>
First, we will create two virtual machines: DC-1, which will run Windows Server 2022, and Client1, which will run Windows 10.  It is required that both virtual machines be linked to the same virtual network (VNet).  The domain controller's (DC-1) IP address will then be configured, moving from dynamic to static.  This guarantees that the client computer can use DC-1 as its DNS server and join the domain.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/95b405bb-18f7-41b9-a56d-a7d94f99f89e"/>
</p>
<p>
The domain, private, and public profiles' firewalls will then be disabled after we log in via RDP to the domain controller.  Right-click the Windows symbol, choose "Run," and then type wf.msc to accomplish this.  Make sure the firewall is disabled for every profile in the Windows Defender Firewall window.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/1c9f51d9-6d7f-49bf-8f58-ce9700c1e0c1"/>
</p>
<p>
Next, in the Azure portal's networking settings, we'll switch Client 1's DNS server to DC-1's static private IP address.  To implement the updated DNS configuration, restart the virtual machines after making this modification.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/36bc91b5-4252-41ae-a25d-e0a79a8822aa"/>
<img src="https://github.com/user-attachments/assets/ef474797-682d-4068-a5fd-3f467233cb9e"/>
</p>
<p>
After that, we'll use PowerShell to try to ping DC-1's IP address after logging in via RDP to the Client1 virtual machine.  Since we turned off DC-1's firewall, the computer should be able to respond to the ping, so this should work.  To verify that DC-1 is set up as the virtual machine's DNS server, we'll use Client 1's ipconfig /all command.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/898c419a-2778-4949-9826-c977efd6fda7"/>
</p>
<p>
Next, we'll use the domain controller's Server Manager to install Active Directory Domain Services (AD DS).
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/3703e78e-c4e0-4713-b5b2-a0f4873e8121"/>
  </p>
<p>
<img src="https://github.com/user-attachments/assets/12b95c24-6788-4358-9786-dc579691cd96"/>

</p>
<p>
A new forest will be created with the domain name mydomain.com, and DC-1 will be promoted to a Domain Controller.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/c1c152c0-e3da-4e97-844b-a6ebe3d36aae"/>
</p>
<img src="https://github.com/user-attachments/assets/1997441d-ab78-42b2-80db-0d09c54df25f"/>
</p>
<p>
Next, we'll open Active Directory Users and Computers and create two organizational units, _EMPLOYEES and _ADMINS, by right-clicking mydomain.com, selecting New, and then choosing Organizational Unit.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/8df4b224-a626-4422-b834-97e27d5b64aa"/>
</p>
<p>
In the Admins OU, we'll create a new user named Jane Doe with the username jane_admin. 
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/023fcd41-4a53-4215-acf3-d00396d16cf4"/>
</p>
  
<p>
Jane Doe will then be added as a Domain Administrator.  Click Add after right-clicking the user, choosing Properties, and then selecting the Member Of tab.  To finish, type Domain Admins in the "Enter object names" field and hit Enter.
</p>
<br />

<p>

<img src="https://github.com/user-attachments/assets/c2f6550e-8f59-490d-aa26-e13764b7d8c8"/> 
</p>
<p>
Now, exit DC-1 and re-enter via RDP using the given password and the credentials mydomain.com\jane_admin.  All upcoming DC-1 logins will be made using this account.
</p>
<br />

<p>

<img src="https://github.com/user-attachments/assets/b861b8af-ac6d-4376-a473-e4f9f6f327f0"/> 
</p>
<p>
We will now join the domain from the Client1 virtual machine.  Right-click the Windows logo, choose System, and then click Rename this PC (Advanced) after logging in via RDP.  After that, click Change, select Domain, and type mydomain.com as the domain name.  To implement the changes, the virtual machine will restart.
</p>
<br />

<p>
  

<img src="https://github.com/user-attachments/assets/f7ef8598-679a-4894-957d-698bcdeff1f2"/> 
</p>
<p>
Return to DC-1 and launch Users and Computers in Active Directory.  Under mydomain.com, create a new organizational unit called _CLIENTS.  Next, by dragging the client-1 computer over with the mouse, we will add it to the _CLIENTS folder in the computers section. 
</p>
<br />

<p>

<img src="https://github.com/user-attachments/assets/5d981cd5-d20c-47b3-8fe2-b5ddf1a0ad4d"/> 
</p>
<p>
Afterwards, log in as jane_admin to the Client1 virtual machine. Navigate t o Remote Desktop, right-click the Windows logo, select System, and then select Remote Desktop.  To grant Domain Users RDP access to the virtual machine, click Select Users who can access this PC remotely.
</p>
<br />

<p>

<img src="https://github.com/user-attachments/assets/486b990a-c025-4e8e-b0bf-1f7d9bd14d13"/> 
</p>
<p>
Next, we need to open PowerShell ISE as an administrator and log in to DC-1 as jane_admin.  To create thousands of new user accounts, we'll utilize a script.  'https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1' is the location of this script.  In order to run the script we must first create a new file, paste the script into it, and execute it. We can now  observe as the accounts are generated on their own. 
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/9647a5e3-d7ef-48f8-92c7-24aca5eec72d"/>
</p>
<p>
Upon executing the script, access Active Directory Users and Computers (ADUC) to confirm that the accounts have been established within the designated _EMPLOYEES organizational unit.
</p>
<br />

<p>
  <img src="https://github.com/user-attachments/assets/e86494bb-beb6-412c-a7eb-4b3851ec9af0"/> 
</p>
<img src="https://github.com/user-attachments/assets/255abd83-2bfe-4628-9558-6a4b3574b46a"/>
</p>
<p>
Finally, we will access the Client1 VM utilizing one of the user accounts established by the PowerShell script, employing the username and the default password "Password1". Upon logging in, launch Command Prompt to confirm that we are authenticated as one of the users generated by the script. 
</p>
<br />
