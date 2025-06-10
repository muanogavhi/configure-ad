<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>Deploying Active Directory with PowerShell Automation, Group Policy, and Secure File Sharing in Azure</h1>
This lab demonstrates the deployment of an Active Directory environment in Azure, including tasks such as user account creation, Group Policy configuration, and automating user provisioning with PowerShell. Additionally, we will explore setting up network file shares and assigning permissions. The lab also includes monitoring logs using Event Viewer to ensure troubleshooting and auditing capabilities are in place. <br />

<h2>Environments and Tools</h2>

- Microsoft Azure (Virtual Machines)
- Remote Desktop Protocol (RDP)
- Windows Server 2022 (Domain Controller)
- Windows 10 (Client)
- PowerShell ISE for automation
- Event Viewer for log monitoring

<h2>Step 1: Deploy Azure Resources</h2>

<p>
<img width="2056" alt="Screenshot 2024-10-17 at 12 48 31 PM" src="https://github.com/user-attachments/assets/69be9975-7170-4d72-beac-798495852c97">
</p>
<p>
Create a Windows Server 2022 VM for the Domain Controller (DC-1).

Create a Windows 10 VM for the Client machine (Client-1).

Ensure both machines are in the same Resource Group and Virtual Network (Vnet).
</p>
<br />

<p>
<img width="2056" alt="Screenshot 2024-10-17 at 12 54 33 PM" src="https://github.com/user-attachments/assets/219f8a01-4047-4aa9-819b-bb77b5a2adf2">
</p>
<p>
Set the Private IP address for DC-1 to static.
</p>
<br />

<p>
<img width="2056" alt="Screenshot 2024-10-17 at 1 14 36 PM" src="https://github.com/user-attachments/assets/338d0cd6-be64-4ed0-9578-de491d6bb6be">
</p>
<p>
Set Client-1's DNS settings to DC-1's now static Private IP address, then restart Client-1 VM so the new settings will be enabled.
</p>
<br />

<p>
<img width="2056" alt="Screenshot 2024-10-17 at 1 30 22 PM" src="https://github.com/user-attachments/assets/fdda1c54-a7bb-4042-99ca-5de758d83e0d">
</p>
<p>
Log into Client-1 through Remote Desktop and run the "ping" DC-1's Private IP address in PowerShell to confirm connectivity.
</p>
<br />

<p>
<img width="2056" alt="Screenshot 2024-10-17 at 1 31 28 PM" src="https://github.com/user-attachments/assets/274fc3fc-b96c-45fd-b1d5-494072552416">
</p>
<p>
Then, run "ipconfig /all" and verify that DC-1's Private IP shows up under "DNS Servers."
</p>
<br />

<h2>Step 2: Install Active Directory</h2>

<p>
<img width="2056" alt="Screenshot 2024-10-17 at 1 47 37 PM" src="https://github.com/user-attachments/assets/37512fdc-d011-4964-a210-29419f4695c0">
</p>
<p>
Open Server Manager

Click "Add roles and features" and install "Active Directory Domain Services" and let DC-1 restart.

Click on the flag icon on the top right and click "Promote this server to a Domain Controller," setting up a new forest with the domain name (e.g., mydomain.com). Then let it restart again.
</p>
<br />

<p>
<img width="2056" alt="Screenshot 2024-10-17 at 1 45 44 PM" src="https://github.com/user-attachments/assets/a4d48875-7934-438e-b5b3-bcfa29d7c484">
</p>
<p>
Now log into DC-1 again, but this time as mydomain.com\[whatever you set your username for the Domain Controller VM in Azure].
</p>
<br />

<h2>Step 3: Create Users and Organizational Units</h2>

<p>
<img width="2056" alt="Screenshot 2024-10-17 at 1 52 29 PM" src="https://github.com/user-attachments/assets/90c55542-6016-4cd3-9c98-e3495f84f72f">
</p>
<p>
Using Active Directory Users and Computers (ADUC), create three OUs: _EMPLOYEES, _ADMINS, and _CLIENTS.
</p>
<br />

<p>
<img width="2056" alt="Screenshot 2024-10-17 at 1 54 32 PM" src="https://github.com/user-attachments/assets/4e87cb11-9589-40fb-b589-e77add4a41a1">
</p>
<p>
Create a new user so we can make a Domain Admin account, "jane_admin."
<br />

<p>
<img width="2056" alt="Screenshot 2024-10-17 at 1 56 41 PM" src="https://github.com/user-attachments/assets/b945aa4d-879b-4b71-9061-88b14566e20f">
</p>
<p>
Now assign the user to the Domain Admins group.
</p>
<br />

<p>
<img width="2056" alt="Screenshot 2024-10-17 at 1 59 09 PM" src="https://github.com/user-attachments/assets/9158095d-ad1e-4086-9121-e2af5cf86103">
</p>
<p>
Log in as cyberlab.com\jane_admin for further administration.
</p>
<br />

<h2>Step 4: Join the Client Machine to the Domain</h2>

<p>
<img width="2056" alt="Screenshot 2024-10-17 at 2 01 16 PM" src="https://github.com/user-attachments/assets/f7ad8cc4-cbb7-4c41-b2d5-7bcd7b2e3c37">
</p>
<p>
In Client-1, go to your settings "About" section and click "Rename this PC (advanced)" then "Change" then add the name of your Domain to join this Client to the Domain. 
</p>
<br />

<p>
<img width="2056" alt="Screenshot 2024-10-17 at 2 02 43 PM" src="https://github.com/user-attachments/assets/8b14a2eb-120f-4086-b401-68671b5f46f0">
</p>
<p>
It will then ask for you to sign in as a Domain Admin to allow this. 

Then allow your VM to restart.
</p>
<br />

<p>
</p><img width="2056" alt="Screenshot 2024-10-17 at 2 08 27 PM" src="https://github.com/user-attachments/assets/fc670165-b0c4-41f1-970f-76adb92f9f9e">
<p>
In DC-1, verify the successful domain join in ADUC by confirming Client-1 appears in the Computers container.
</p>
<br />

<h2>Step 5: Automate User Account Creation with PowerShell</h2>

<p>
<img width="2056" alt="Screenshot 2024-10-17 at 2 38 51 PM" src="https://github.com/user-attachments/assets/73d96625-1fd1-48df-a14b-3955517c2832">
</p>
<p>
Open PowerShell ISE as an administrator on DC-1.
</p>
<br />

<p>
<img width="2056" alt="Screenshot 2024-10-17 at 2 41 33 PM" src="https://github.com/user-attachments/assets/a6142041-808d-4290-bf8a-794e3e10bfc9">
</p>
<p>
Execute a PowerShell script to create 10 thousand user accounts in the _EMPLOYEES OU.
</p>
<br />

<p>
<img width="2056" alt="Screenshot 2024-10-17 at 2 48 45 PM" src="https://github.com/user-attachments/assets/3f738f13-e369-4eb0-9a1e-ca374b4344d5">
</p>
<p>
Test one of the newly created accounts by logging into Client-1 (mydomain.com\[randomly chosen username the script generated])
</p>
<br />

<h2>Step 6: Group Policy and Managing Accounts</h2>

<p>
<img width="2056" alt="Screenshot 2024-10-17 at 3 07 21 PM" src="https://github.com/user-attachments/assets/0d730396-b70d-47a5-92af-479c447065ab">
</p>
<p>
Type gpmc.msc in Search Bar to open the Group Policy Management Console and navigate to "Account Lockout Policy" so we can adjust some settings such as lockout duration and threshold of invalid login attempts.
</p>
<br />

<p>
<img width="2056" alt="Screenshot 2024-10-17 at 3 12 40 PM" src="https://github.com/user-attachments/assets/0484278e-1fa7-4461-b5d6-4444d5c0b331">
</p>
<p>
Then log into Client-1 as the Domain Admin run the command "gpupdate /force"
</p>
<br />

<p>
<img width="2056" alt="Screenshot 2024-10-17 at 3 14 05 PM" src="https://github.com/user-attachments/assets/30a7f0bc-40a3-48de-9c52-c00a05511f3b">
</p>
<p>
Next we open Command Prompt as an Administrator and run the command "gpresult" to confirm we made our changes.
</p>
<br />

<p>
<img width="2056" alt="Screenshot 2024-10-17 at 3 17 15 PM" src="https://github.com/user-attachments/assets/c436404c-4ff6-4c99-80e1-d6b432f303f3">
</p>
<p>
Now we verify this new lockout policy has taken place by logging into one of our random users with the wrong password and seeing if we get locked out.
</p>
<br />

<p>
<img width="2056" alt="Screenshot 2024-10-17 at 3 18 47 PM" src="https://github.com/user-attachments/assets/b5cbfaa5-9fed-4be9-9815-ac046f7b6c91">
</p>
<p>
In DC-1 we will find the user in Active Directory Users and Computers and unlock that account.
</p>
<br />

<p>
<img width="2056" alt="Screenshot 2024-10-17 at 3 19 20 PM" src="https://github.com/user-attachments/assets/2532a9f6-40ac-47f5-ac1c-1dd92f5b75ed">
</p>
<p>
We will log into that account again but this time with the right password and verify that our account was unlocked.
</p>
<br />


<h2>Step 7: File Shares and Permissions</h2>

<p>
<img width="2056" alt="Screenshot 2024-10-17 at 3 41 53 PM" src="https://github.com/user-attachments/assets/f4b59ba1-f617-4fc8-95ab-6e867c7e8f9f">
</p>
<p>
Log into DC-1 as jane_admin and create the following folders on the C: drive:
  
- read-access
- write-access
- no-access
- accounting
</p>
<br />

<p>
Set Permissions on Folders:
<img width="2056" alt="Screenshot 2024-10-17 at 3 43 10 PM" src="https://github.com/user-attachments/assets/4aa5e84c-badb-4860-aef6-796802ba3f89">
</p>  
<p>
For read-access: assign Domain Users the Read permission.

For write-access: assign Domain Users the Read/Write permission.

For no-access: assign Domain Admins the Read/Write permission.
</p>
<br />

<p>
<img width="2056" alt="Screenshot 2024-10-17 at 3 45 59 PM" src="https://github.com/user-attachments/assets/bf446b8b-c257-462f-845b-006ba4ac11fa">
</p>
<p>
On Client-1, log in as a normal user (e.g., mydomain.com\john_employee), open the Run dialog and type \\DC-1 to access the shared folders.
</p>
<br />

<p>
<img width="2056" alt="Screenshot 2024-10-17 at 3 47 53 PM" src="https://github.com/user-attachments/assets/cc69b911-e144-4ec3-854d-8430c913c06d">
</p>
<p>
Now we can test access to each folder:

- read-access: User should be able to view files but not modify.
- write-access: User should be able to both view and modify files.
- no-access: User should be denied access.
</p>
<br />

<p>
<img width="2056" alt="Screenshot 2024-10-17 at 3 50 15 PM" src="https://github.com/user-attachments/assets/4eb4eb5f-f206-4048-a330-d85c3f0ce178">
</p>
<p>
On DC-1, create a Security Group named ACCOUNTANTS in ADUC.
</p>
<br />

<p>
<img width="2056" alt="Screenshot 2024-10-17 at 3 51 42 PM" src="https://github.com/user-attachments/assets/f2993713-dd08-4c26-804d-ffebfe3ca24d">
</p>
<p>
Assign Read/Write permissions to the ACCOUNTANTS group on the accounting folder.
</p>
<br />

<p>
<img width="2056" alt="Screenshot 2024-10-17 at 3 52 33 PM" src="https://github.com/user-attachments/assets/c51f377a-8ae6-4613-88b0-8753c8cbc57d">
</p>
<p>
On Client-1, attempt to access the accounting folder as john_employee (should fail).
</p>
<br />

<p>
<img width="2056" alt="Screenshot 2024-10-17 at 3 53 05 PM" src="https://github.com/user-attachments/assets/385504ce-02b4-47f0-8dc9-8d5253fecdcb">
</p>
<p>
Add john_employee to the ACCOUNTANTS security group.
</p>
<br />

<p>
<img width="2056" alt="Screenshot 2024-10-17 at 3 54 47 PM" src="https://github.com/user-attachments/assets/c7f94971-d415-485b-9612-c3ff0d24133b">
</p>
<p>
Re-log into Client-1 and confirm access to the accounting folder now works.
</p>
<br />

<h2>Step 8: Viewing Logs in Event Viewer</h2>

<p>
<img width="2056" alt="Screenshot 2024-10-17 at 3 27 14 PM" src="https://github.com/user-attachments/assets/b90bfe3e-9f1a-49f6-8515-5ca484a7e074">
</p>
<p>
Search for eventvwr.msc on Client-1 and run as administrator
</p>
<br />

<p>
<img width="2056" alt="Screenshot 2024-10-17 at 3 28 31 PM" src="https://github.com/user-attachments/assets/0c144f09-1ade-49fe-b1d2-ed3c715fa07e">
</p>
<p>
Navigate to Windows Logs > Security.

Here, we can view logs for:

- Successful/failed logon attempts.
- Group Policy application events.
- Network logon attempts.

Use Event IDs (e.g., 4624 for logon and 4625 for failed logon) to filter and identify specific events.
</p>
<br />

<h2>Conclusion</h2>

<p>
This lab provided comprehensive hands-on experience with deploying and managing Active Directory in an Azure environment. We configured key aspects such as user accounts, Group Policy, and file shares, alongside setting and testing access permissions. Automation with PowerShell enabled bulk user creation, and Event Viewer was utilized to monitor security and troubleshooting logs. Overall, this lab demonstrated practical skills essential for managing an Active Directory infrastructure and ensuring secure, organized user access.
</p>
<br />
