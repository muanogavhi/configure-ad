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

![image](https://github.com/user-attachments/assets/38cf4a62-29f2-421c-8e81-95fe7bb09b38)

</p>
<p>
Log into Client-1 through Remote Desktop and run the "ping" DC-1's Private IP address in PowerShell to confirm connectivity.
</p>
<br />


![image](https://github.com/user-attachments/assets/7578a826-c0d1-4460-a5b1-8bbe78875d3e)

</p>
<p>
Then, run "ipconfig /all" and verify that DC-1's Private IP shows up under "DNS Servers."
</p>
<br />

<h2>Step 2: Install Active Directory</h2>


![image](https://github.com/user-attachments/assets/cbfd14f0-88ab-458f-9694-c58f61e7d4e0)

</p>
<p>
Open Server Manager

Click "Add roles and features" and install "Active Directory Domain Services" and let DC-1 restart.

Click on the flag icon on the top right and click "Promote this server to a Domain Controller," setting up a new forest with the domain name (e.g., mydomain.com). Then let it restart again.
</p>
<br />

![image](https://github.com/user-attachments/assets/896a5f3b-9837-499d-bca0-8a21e633f2d8)


</p>
<p>
Now log into DC-1 again, but this time as mydomain.com\[whatever you set your username for the Domain Controller VM in Azure].
</p>
<br />

<h2>Step 3: Create Users and Organizational Units</h2>


![image](https://github.com/user-attachments/assets/822ac014-4922-456b-9487-5131d0eecd07)

</p>
<p>
Using Active Directory Users and Computers (ADUC), create three OUs: _EMPLOYEES, _ADMINS, and _CLIENTS.
</p>
<br />

![image](https://github.com/user-attachments/assets/fd0dd8fd-f121-418e-a242-a78005f160e2)

</p>
<p>
Create a new user so we can make a Domain Admin account, "jane_admin."
<br />


![image](https://github.com/user-attachments/assets/428cf98e-dffa-49a2-a312-7b32cf3e364b)

</p>
<p>
Now assign the user to the Domain Admins group.
</p>
<br />


![image](https://github.com/user-attachments/assets/b9b3bd65-514b-4c28-8780-0072dc9c50f2)

</p>
<p>
Log in as cyberlab.com\jane_admin for further administration.
</p>
<br />

<h2>Step 4: Join the Client Machine to the Domain</h2>


![image](https://github.com/user-attachments/assets/6a5e0083-0ba1-4c96-804e-dbc6edb80a54)

</p>
<p>
In Client-1, go to your settings "About" section and click "Rename this PC (advanced)" then "Change" then add the name of your Domain to join this Client to the Domain. 
</p>
<br />


![image](https://github.com/user-attachments/assets/bc884f35-9937-438c-9062-ebbd69788f35)

</p>
<p>
It will then ask for you to sign in as a Domain Admin to allow this. 

Then allow your VM to restart.
</p>
<br />


![image](https://github.com/user-attachments/assets/4f0902f0-fe74-4e2c-96f8-3ec56ef90b6e)

<p>
In DC-1, verify the successful domain join in ADUC by confirming Client-1 appears in the Computers container.
</p>
<br />

<h2>Step 5: Automate User Account Creation with PowerShell</h2>


![image](https://github.com/user-attachments/assets/1b4aa0bc-ea9f-499d-8f36-73e2dd2f3941)

</p>
<p>
Open PowerShell ISE as an administrator on DC-1.
</p>
<br />


![image](https://github.com/user-attachments/assets/cfca7c65-2ee1-44f4-a333-8724f9270390)

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


![image](https://github.com/user-attachments/assets/8eb1154d-1487-4654-85da-56a6063321cb)

</p>
<p>
Type gpmc.msc in Search Bar to open the Group Policy Management Console and navigate to "Account Lockout Policy" so we can adjust some settings such as lockout duration and threshold of invalid login attempts.
</p>
<br />


![image](https://github.com/user-attachments/assets/a2d356bc-9add-424d-a13d-83fbf08eef96)

</p>
<p>
Then log into Client-1 as the Domain Admin run the command "gpupdate /force"
</p>
<br />


![image](https://github.com/user-attachments/assets/8c4b37ab-db2d-4ecf-818c-b837a4bfea0a)

</p>
<p>
Next we open Command Prompt as an Administrator and run the command "gpresult" to confirm we made our changes.
</p>
<br />


![image](https://github.com/user-attachments/assets/cb73ca2d-234b-4a59-8d9a-e3a027ef83e2)

</p>
<p>
Now we verify this new lockout policy has taken place by logging into one of our random users with the wrong password and seeing if we get locked out.
</p>
<br />


![image](https://github.com/user-attachments/assets/7decb118-c89d-49c1-95e0-277f8524678e)

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


![image](https://github.com/user-attachments/assets/a6015d10-c31d-42a3-b90d-15392b5c7c61)

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

  
![image](https://github.com/user-attachments/assets/dee6f340-4b21-4a34-b18c-20126d52f47e)

</p>  
<p>
For read-access: assign Domain Users the Read permission.

For write-access: assign Domain Users the Read/Write permission.

For no-access: assign Domain Admins the Read/Write permission.
</p>
<br />


![image](https://github.com/user-attachments/assets/907bc127-f9f1-455d-a674-4f412bd1d518)

</p>
<p>
On Client-1, log in as a normal user (e.g., mydomain.com\john_employee), open the Run dialog and type \\DC-1 to access the shared folders.
</p>
<br />


![image](https://github.com/user-attachments/assets/66627edb-5427-482e-890f-f5540687fd80)

</p>
<p>
Now we can test access to each folder:

- read-access: User should be able to view files but not modify.
- write-access: User should be able to both view and modify files.
- no-access: User should be denied access.
</p>
<br />


![image](https://github.com/user-attachments/assets/7b22b099-1ea9-451c-9dc5-da83010a5a13)

</p>
<p>
On DC-1, create a Security Group named ACCOUNTANTS in ADUC.
</p>
<br />


![image](https://github.com/user-attachments/assets/6906af0f-f39f-4eb4-b6ec-4d28afbb775a)

</p>
<p>
Assign Read/Write permissions to the ACCOUNTANTS group on the accounting folder.
</p>
<br />


![image](https://github.com/user-attachments/assets/ebe894f8-e9dc-4317-942c-68e2380545da)

</p>
<p>
On Client-1, attempt to access the accounting folder as john_employee (should fail).
</p>
<br />


![image](https://github.com/user-attachments/assets/b27b813f-0154-451f-8b1e-24fe76b79076)

</p>
<p>
Add john_employee to the ACCOUNTANTS security group.
</p>
<br />


![image](https://github.com/user-attachments/assets/2c4b4a09-fd28-4590-8b4f-1ace4992022e)

</p>
<p>
Re-log into Client-1 and confirm access to the accounting folder now works.
</p>
<br />

<h2>Step 8: Viewing Logs in Event Viewer</h2>


![image](https://github.com/user-attachments/assets/c4d35bc4-3fb4-4f9d-ae36-9cd0f4a2c7eb)

</p>
<p>
Search for eventvwr.msc on Client-1 and run as administrator
</p>
<br />


![image](https://github.com/user-attachments/assets/99bebbb4-a394-4960-9ebe-7e1e230f6fb7)

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
