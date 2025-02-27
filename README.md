<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>Configure Active Directory within Azure virtual Machines</h1>
This lab demonstrates the deployment of an Active Directory environment in Azure, this including creating two VMs(Dc-1 & Client-1) then you join client-1 to the domain controller, login to DC-1 using domain user that was created once logged in create several user using PowerShell script . Additionally, we will explore assigning permissions to groups. <br />


<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

-Windows Server 2022

-Windows 10 (21H2)



# Step 1: creating and configuring VMs

Create two virtual machines, a Windows Server 2022 VM for the Domain Controller (DC-1) and create a Windows 10 VM for the Client machine (Client-1), ensure both machines are in the same Resource Group and Virtual Network (Vnet), once both virtual machines are deployed join client-1 to DC_1 by Set the Private IP address for DC-1 to static. Log into Client-1 through Remote Desktop and run the "ping" DC-1's Private IP address in PowerShell to confirm connectivity.


![Screenshot 2025-02-27 173302](https://github.com/user-attachments/assets/d3815fb2-3517-40ce-9685-5f2292cfa0db)


# Step 2: Install Active Directory

Log-into DC-1 and on the server manager click "Add Roles and Features", and hit next until you reach server roles. Check the Active Directory Domain Services box and click "Add Features" and install it. Once done, hit the flag on the top right next to "Manage" and click "promote this server to a domain controller. In the configuration wizard select "Add a new forest" and create your domain name. Click next and set your restore mode password.


<img width="2056" alt="Screenshot 2024-10-17 at 1 47 37 PM" src="https://github.com/user-attachments/assets/37512fdc-d011-4964-a210-29419f4695c0">


# Step 3: Create user and organizational units

Restart and log back into DC-1 as a normal user. Make sure to put @yourdomain.com after the username since we just promoted it to a domain controller. In server manager go to tools --> Active Directory Users and Computers and create a new organizational unit called _EMPLOYEES and one called _ADMINS

<p>
<img width="2056" alt="Screenshot 2024-10-17 at 3 18 47 PM" src="https://github.com/user-attachments/assets/b5cbfaa5-9fed-4be9-9815-ac046f7b6c91">
</p>


# Step 4: Join the Client Machine to the Domain

<p>In Client-1, go to your settings "About" section and click "Rename this PC (advanced)" then "Change" then add the name of your Domain to join this Client to the Domain. It will then ask for you to sign in as a Domain Admin to allow this. Then allow your VM to restart. In DC-1, verify the successful domain join in ADUC by confirming Client-1 appears in the Computers container.

  
<img width="2056" alt="Screenshot 2024-10-17 at 2 01 16 PM" src="https://github.com/user-attachments/assets/f7ad8cc4-cbb7-4c41-b2d5-7bcd7b2e3c37">


# Step 5: Automate User Account Creation with PowerShell

<p>Login to DC-1 as jane admin, Open PowerShell Ise as an administrator Create a new File and paste the contents of the script into it (https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1). Run the script and observe the accounts being created

  
<img width="2056" alt="Screenshot 2024-10-17 at 2 38 51 PM" src="https://github.com/user-attachments/assets/73d96625-1fd1-48df-a14b-3955517c2832">


# Step 5: Group Policy and Managing Accounts
<p>Type gpmc.msc in Search Bar to open the Group Policy Management Console and navigate to "Account Lockout Policy" so we can adjust some settings such as lockout duration and threshold of invalid login attempts. Then log into Client-1 as the Domain Admin run the command "gpupdate /force “Next we open Command Prompt as an Administrator and run the command "gpresult" to confirm we made our changes. Now we verify this new lockout policy has taken place by logging into one of our random users with the wrong password and seeing if we get locked out. DC-1 we will find the user in Active Directory Users and Computers and unlock that account. We will log into that account again but this time with the right password and verify that our account was unlocked



<img width="2056" alt="Screenshot 2024-10-17 at 3 07 21 PM" src="https://github.com/user-attachments/assets/0d730396-b70d-47a5-92af-479c447065ab">



# Congratulation you've now successfully setup and configured Active Directory  !!!

