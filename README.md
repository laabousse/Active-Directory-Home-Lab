# Active-Directory-Home-Lab
This repository offers step-by-step instructions for setting up a home lab with Active Directory using Oracle VirtualBox. It covers the installation of Windows Server 2019 as a Domain Controller and the configuration of Windows 10 clients to join the domain. Perfect for IT students and enthusiasts looking to explore AD in a virtual environment.


# Active Directory Home Lab Setup

This repository offers step-by-step instructions for setting up a home lab with Active Directory using Oracle VirtualBox.

## Prerequisites
- Oracle VirtualBox installed
- Windows Server 2019 ISO
- Windows 10 Enterprise ISO
- Basic knowledge of networking and Windows Server

## Overview
1. [Install VirtualBox](#step-1-install-virtualbox)
2. [Create Windows Server 2019 VM](#step-2-create-windows-server-2019-vm)
3. [Configure the Network Interface Controller](#step-3-configure-the-network-interface-controller)
4. [Configure Windows Server as Domain Controller](#step-4-configure-windows-server-as-domain-controller)
5. [Install RAS/NAT for Internet Access](#step-5-install-rasnat-for-internet-access)
6. [Install the DHCP Server Role](#step-6-install-the-dhcp-server-role)
7. [Generate Sample Users Automatically Using PowerShell](#step-7-generate-sample-users-automatically-using-powershell)
8. [Create Windows 10 Enterprise VM](#step-8-create-windows-10-enterprise-vm)
9. [Join Windows 10 Client to the Domain](#step-9-join-windows-10-client-to-the-domain)

## Instructions

### Step 1: Install VirtualBox
   1. Download and install VirtualBox from [Oracle's website](https://www.virtualbox.org/wiki/Downloads).
   2. Follow the installation instructions.
      
      ![VirtualBox installation](https://github.com/user-attachments/assets/0bf3d0c0-06bf-48d6-a3e3-b8deec0c2444)

### Step 2: Create Windows Server 2019 VM
   1. [Download Windows Server 2019 ISO Image](https://www.microsoft.com/en-us/evalcenter/download-windows-server-2019)
   2. Open VirtualBox and click on "New".
   3. Name the VM, choose Windows Server 2019 ISO image then check Skip Unattended Installation".

      ![Name the VM](https://github.com/user-attachments/assets/7216e671-7c0d-4dcd-b093-6948bb80968a)
   
   4. Allocate memory and Processors (e.g., 4GB).
      
      ![Allocate Memory](https://github.com/user-attachments/assets/410144cf-ba5f-43a8-a385-0b0a0b4b92ef)

   5. Create a virtual hard disk (e.g., 50GB).
      
      ![Create a virtual hard disk](https://github.com/user-attachments/assets/917c2c6f-05fb-4a73-b92a-bb1ce0cb7750)

   6. Change Windows Server 2019 VM Settings:
      
      In the General -> Advanced tab, select Bidirectional for both Shared Clipboard and Drag'n'Drop. This configuration facilitates file sharing and enables copy-paste functionality between the host machine and the virtual machine.
      
      ![Settings General Advenced](https://github.com/user-attachments/assets/71b9c2fc-51ba-49a1-a2a8-027433d875c8)

      In the Network -> Adapter1 tab, set the Attached to option to NAT. This configuration allows the virtual machine to access external networks while utilizing the host's IP address for network connectivity.
      
      ![Settings Network Adapter1](https://github.com/user-attachments/assets/270fcf4f-dd5c-4cfd-a3a5-380c41067355)
   
      In the Network -> Adapter 2 tab, check the Enable Network Adapter option, then set the Attached to option to Internal Network. This configuration allows communication between virtual machines on the internal network while ensuring isolation from external networks.
      
      ![Settings Network Adapter2](https://github.com/user-attachments/assets/8b98a743-37be-4009-a2cd-7d13fa48c94f)

   7. Start the Windows Server 2019 VM to install.
      
      ![StartWSInstallation](https://github.com/user-attachments/assets/03297cf8-f3b8-43e6-b6e1-71220a97fe41)
   
      Select Windows Server 2019 (Desktop Experience). It is essential to choose a version with the Desktop Experience, as selecting one without it will result in a command-line interface only, lacking a graphical user interface (GUI). Proceed by selecting Standard with Desktop Experience, then click Next and accept the license terms.
      
      ![SelectWSVersion](https://github.com/user-attachments/assets/33593199-ed95-455d-b865-a2084c93ddfc)

      You will be presented with a built-in administrator account. For the purposes of this tutorial, we will set a simple, easy-to-remember password. However, in practice, it is advisable to use a strong password for security. Once you've entered the password, click Finish to complete the setup.
      
      ![Built-in Admin Account](https://github.com/user-attachments/assets/6adc300d-14ce-4bf2-a489-c342e15c167e)

      To log in using the built-in administrator account, navigate to the menu bar and select Input > Keyboard, then choose Insert Ctrl + Alt + Delete.
      
      ![Insert CtrlAltDel](https://github.com/user-attachments/assets/4ed026e2-ea93-4599-9bd8-7a81ae9a4463)

### Step 3: Configure the Network interface controller:
   1. Identifying Network Adapters:
      
      You will notice that there are two network adapters present. It is important to identify each one and name them appropriately, as they will be used later for routing configuration. To determine their functions, check the first adapter's status and details. If it displays a valid home IP address (e.g., 10.x.x.x), it is likely the adapter connected to the internet.
      
   ![check interface](https://github.com/user-attachments/assets/ed5ff002-ae8e-4825-b241-b5005bea3742)

   2. Renaming Network Adapters:
      
      After identifying the adapters, rename them accordingly as **\__INTERNET\_** and **\_INTERNAL\_**. To do this, right-click on each adapter and select the **Rename** option.
      
      ![Name Adabpters](https://github.com/user-attachments/assets/09467a23-73f1-4f66-9d09-2c96e214dd89)

   3. Configuring the Internal Network Interface:

         We will assign the IP address 172.16.0.1 to our internal network interface, with a subnet mask of 255.255.255.0. Since the domain controller will function as the default gateway, we will not specify a default gateway for this particular network interface. The domain controller has two network interface controllers (NICs), one for internet access and one for internal communication.
      
      For the DNS server, when Active Directory is installed, it automatically configures DNS. Therefore, this server will use its own IP address as the DNS server. You can enter either its own IP address or the loopback address (127.0.0.1) for DNS resolution.
      
      ![assign IP for second Network adapter](https://github.com/user-attachments/assets/eda86951-9a7f-459d-b28c-ca55cf02f304)

   5. Naming the Domain Controller:
      
      Navigate to the PC Name settings and change it to a recognizable name suitable for a domain controller; for this example, we will use DC.
      
      ![Change PC Name](https://github.com/user-attachments/assets/29f40d95-b09d-4108-bfac-7f109601a9ab)

### Step 4: Configure Windows Server as Domain Controller
   1. Once Windows Server is installed, open the Server Manager.
   2. Select "Add roles and features".

      ![add AD DS](https://github.com/user-attachments/assets/9382fcf5-6083-4bde-be37-39f8a2db4072)

   3. Follow the wizard to install Active Directory Domain Services (AD DS).

      ![add ad ds](https://github.com/user-attachments/assets/8b5331dc-7ee2-424d-8c6c-df39a35d223e)
   
   4. Promote the server to a domain controller.

   
      Now that the role has been installed, we can close the window. You will notice a yellow warning icon in the notification area; please click on it. This indicates that we need to complete the post-deployment configuration. While we have installed the Active Directory Domain Services software, we have not yet created the domain. Therefore, click on the option to promote this computer to a domain controller.

      ![Promote PC to DomainController](https://github.com/user-attachments/assets/2ff9e214-2e3f-4d4b-bf12-7585da252e7c)

   5. Creating a Dedicated Domain Admin Account.

      After restarting the system, we will log in again using the built-in administrator account and its associated password. You will notice that the format now displays MYCOMPANY\Administrator, which was not present before. Please log in with the password Password2024. Once logged in, we will proceed to create our own dedicated domain admin account instead of relying on the built-in administrator account.

      ![login using builtin admin](https://github.com/user-attachments/assets/5a5fdb47-fbc1-43a3-ad34-f5621b2869cc)

      Now, we will create our own dedicated domain admin account instead of using the built-in administrator account. To do this, navigate to Start, then select Administrative Tools, and click on Active Directory Users and Computers. Be cautious not to select any other Active Directory options. You should see mycompany.com, which is our newly created domain.

      Next, we will create an Organizational Unit (OU) to house our admin accounts. We will name this OU _ADMINS. Inside this OU, we will create a new user and name it according to your preference.

      ![Create dedicated admin account](https://github.com/user-attachments/assets/30b2f427-2ad2-4327-a227-ff55ef10dd58)

      Now we have created an account, but it does not yet have administrative privileges, despite being named as such. To make this account a domain administrator, right-click on it and select Properties. Navigate to the Member Of tab and click Add. Enter Domain Admins, then click Check Names to ensure it resolves correctly. Click OK, then Apply, and finally OK again. This process grants us our very own domain admin account.

      ![promoteUsertoAdmin](https://github.com/user-attachments/assets/6371e351-7094-4ee1-a809-9f3cbaaf7008)

      Now we can use the new admin account. Let's proceed by signing out of the domain controller. Select Sign Out and then perform Ctrl + Alt + Delete again. Instead of logging in with the built-in administrator account, choose Other User. You should see an option to sign into your domain. Here, we will enter our newly created domain admin account credentials.
   
### Step 5: Install RAS/NAT for Internet Access

   The next step is to install RAS/NAT (Remote Access Service/Network Address Translation). The purpose of this installation is to enable our Windows 10 client to operate within a private virtual network while still having internet access through the domain controller. By installing RAS and NAT on the domain controller, we will facilitate this connectivity for our clients.
We will navigate to Add Roles and Features, then click Next until we reach the server selection page. Confirm that the correct server is selected and proceed by clicking Next. For roles, select Remote Access. Next, ensure that Routing is selected, along with the automatically selected options. Continue by clicking Next until you reach the confirmation page, and then click Install.

   ![AddRASNAT](https://github.com/user-attachments/assets/24a663d4-a3b6-4240-94cf-40c8ad3d7e76)

   With the role installation complete, close the window and navigate to Tools > Routing and Remote Access. Select Configure and Enable Routing and Remote Access and click Next. Choose the NAT option, which allows internal clients to access the internet using a single IP address.

   On the next screen, you should see both internal and external interfaces listed. Select Configure, then Next, and choose NAT. Select the Public Interface option to connect to the internet, and from the list, select the __INTERNET_ interface that you identified and named earlier. Click Next and then Finish.
Once configured, you should see a green status indicator with an upward arrow, confirming that the setup is complete.

   ![ActivateRAS](https://github.com/user-attachments/assets/b5a29331-bd11-40fa-9ae7-81f0335ef578)

### Step 6: Install the DHCP Server Role

   The next step is to add the DHCP server. To do this, go back to the domain controller and open Add Roles and Features. Click Next until you reach the server selection, then confirm by clicking Next again. Select DHCP and choose Add Features. Continue through the subsequent prompts by selecting Next and, finally, click Install. The DHCP role installation will take a few moments to complete, so please wait for it to finish.

   ![addDHCP](https://github.com/user-attachments/assets/de7fc81d-f160-43a6-92e3-30af62bb893f)

   Now, close the installation window and navigate to Tools > DHCP to begin setting up your DHCP scope. This opens the DHCP management panel, which we’ll use to configure automatic IP address assignment for client computers on the network.

   ![configure scoop](https://github.com/user-attachments/assets/7f3d02b7-5b43-45a2-9704-d39a9d976db4)

   Next you may need to right-click on the DHCP server and select Authorize. After authorizing, right-click the server again and choose Refresh. You should then see the IPv4 section turn green, indicating it’s active, and the scope should now be visible.

   ![refreshScoop](https://github.com/user-attachments/assets/f2ae826e-bbf5-4bc9-af1d-31120421a3c3)

### Step 7: Generate sample users automatically using PowerShell

   Before creating and joining our client computer to the domain, we’ll streamline user creation in Active Directory by using a PowerShell script to generate sample users automatically.

   1. **Create Folder and Files**: On the **Desktop**, create a folder named `AD_Create_Users`. Inside this folder, create two files: `script.ps1` and `names.txt`.

   2. **Populate Files**:
      - **script.ps1**: Copy and paste the following code into `script.ps1`:

```powershell
# ----- Edit these Variables for your own Use Case ----- #
$PASSWORD_FOR_USERS   = "Password2024" 
$USER_FIRST_LAST_LIST = Get-Content .\names.txt
# ------------------------------------------------------ #

$password = ConvertTo-SecureString $PASSWORD_FOR_USERS -AsPlainText -Force
New-ADOrganizationalUnit -Name _USERS -ProtectedFromAccidentalDeletion $false

foreach ($n in $USER_FIRST_LAST_LIST) {
    $first = $n.Split(" ")[0].ToLower()
    $last = $n.Split(" ")[1].ToLower()
    $username = "$($first.Substring(0,1))$($last)".ToLower()
    Write-Host "Creating user: $($username)" -BackgroundColor Black -ForegroundColor Cyan
    
    New-AdUser -AccountPassword $password `
               -GivenName $first `
               -Surname $last `
               -DisplayName $username `
               -Name $username `
               -EmployeeID $username `
               -PasswordNeverExpires $true `
               -Path "ou=_USERS,$(([ADSI]`"").distinguishedName)" `
               -Enabled $true
}
```

   - **names.txt**: Add the following sample names to `names.txt`:

```
Idriss Laabousse 
Kelly Larose
Emerita Sandor
Mandie Ravenscroft
Donny Edgington
Renna Crocker
Agueda Strout
Eryn Servais
Majorie Nicholes
Irmgard Loden
Luana Fuqua
Cathleen Suda
Lacresha Vanepps
Val Kay
Dolores Wain
```

   3. **Run the Script**: Open **Windows PowerShell ISE** as Administrator:
      - Open the **Start** menu, select **Windows PowerShell**, then right-click **Windows PowerShell ISE** and choose **Run as Administrator**. Confirm by clicking **Yes**.
      - In PowerShell ISE, go to **File > Open**, navigate to the `AD_Create_Users` folder on your Desktop, and select `script.ps1` to execute it.


   ![powershelldemo](https://github.com/user-attachments/assets/c822783b-b7ef-4b62-b265-fb6842f5949b)

   To run the script, you’ll need to modify PowerShell’s execution policy to bypass default security restrictions. This is generally a security feature, but since this is a lab environment, we can proceed.

   4. Open **PowerShell** as **Administrator**.
   5. Run the following command to set the execution policy to unrestricted:

      ```powershell
           Set-ExecutionPolicy Unrestricted
   
   Press Enter to confirm, then choose Yes to All when prompted.

   ![execution policy](https://github.com/user-attachments/assets/a4470e88-d33e-4b79-9bff-a8b9cc653363)

   After adjusting the execution policy, you can now run the script:

   6. Open **Windows PowerShell ISE** as **Administrator**.
   7. Navigate to **File > Open**, locate `script.ps1` in the `AD_Create_Users` folder on your Desktop, and select it.
   8. Execute the script to create sample users in Active Directory automatically.
   
   ![runScript](https://github.com/user-attachments/assets/d07ce3d9-49f2-40c1-9d4c-f8b3cd6001bd)
   
   To confirm the script's success, follow these steps:

   9. Open **Active Directory Users and Computers**:
      - Go to **Start Menu > Active Directory Administrative Tools > Active Directory Users and Computers**.
   10. You should see the `_USERS` organizational unit (OU) created by the script, containing multiple user accounts.
   11. Further Confirmation:
      - Right-click on your domain name again and select **Find**.
      - Ensure **Users** is selected as the object type.
      - Click **Find** to view the list, confirming that the users were successfully created.
   
   You should now see a populated list of users in the `_USERS` folder.
   
   ![UsersCreationSuccess](https://github.com/user-attachments/assets/c62729fb-df40-4bc9-b0b7-ddd8005e5b0c)
   
   After successfully creating the users, it’s good practice to restore the execution policy to a more secure setting. To do this, execute the following PowerShell command:
   ```powershell
        Set-ExecutionPolicy Restricted
   ```
   This will revert the policy back to Restricted to help prevent unauthorized scripts from running.


### Step 8: Create Windows 10 Enterprise VM

   1. [Download Windows 10 Enterprise ISO Image](https://www.microsoft.com/en-us/evalcenter/download-windows-10-enterprise)
   2. Open VirtualBox and click on "New".
   3. Name the VM, choose Windows 10 Enterprise ISO image then check Skip Unattended Installation".

   ![nameClient](https://github.com/user-attachments/assets/c56a6452-c755-4a1c-a0ef-d52064b6efed)
   
   4. Allocate memory and Processors (e.g., 4GB).
   
   ![RAMClient](https://github.com/user-attachments/assets/13af08aa-5aea-49a0-a45d-7b18f61cf10c)
   
   5. Create a virtual hard disk (e.g., 50GB).
   
   ![HardDiskClient](https://github.com/user-attachments/assets/7dcb0bad-3514-42b9-83ff-e617e9e9e073)
   
   6. Change Windows 10 Enterprise VM Settings:
      In the General -> Advanced tab, select Bidirectional for both Shared Clipboard and Drag'n'Drop. This configuration facilitates file sharing and enables copy-paste functionality between the host machine and the virtual machine.
   
      In the Network -> Adapter 1 tab, check the Enable Network Adapter option, then set the Attached to option to Internal Network. Rather than connecting directly to the host computer's network using NAT, we will opt for the internal network adapter. This configuration enables us to obtain a DHCP address from the domain controller, thereby simulating a corporate network environment.

      ![Settings Network AdapterClient](https://github.com/user-attachments/assets/94c4fc89-94e0-4d20-8658-282c2c3de3c2)
   
   7. Start the Windows 10 Enterprise VM to install.
      
      ![InstallClient](https://github.com/user-attachments/assets/57008b79-849f-4f26-b4c1-7d1fbd6ebc58)

      After installing Windows, we will open the Command Prompt and check the IP configuration using the ipconfig command. We now have a fully operational default gateway, indicating that our IP addressing is properly configured. To further verify connectivity, we will attempt to ping an external website, such as bing.com. The successful resolution of this domain confirms that our DNS server is functioning correctly. Moreover, the ability to ping external addresses demonstrates that our entire infrastructure is operational. This encompasses connectivity to the default gateway, which is the domain controller, as well as the appropriate NAT configuration on the domain controller that facilitates outbound internet traffic and allows responses to return successfully.
      
      ![pingTest](https://github.com/user-attachments/assets/7658684e-8d1d-45b0-927a-124da6073928)
   
### Step 9: Join Windows 10 Client to the Domain

   1. Go to Settings -> System -> Rename this PC (Advanced).
      
      Before proceeding with any further configurations, let's change the computer's name. To do this, right-click on the Start menu and select System. Instead of simply clicking on Rename this PC, choose Rename this PC (Advanced), as this option allows us to join the domain simultaneously.
      
      ![remameClient](https://github.com/user-attachments/assets/380ffa06-cf7c-41b5-a3a3-2af39528b95f)

   2. Click on "Member of Domain".
      
      we will join the domain we created, which is named mycompany.com. After entering these details, click OK and follow the prompts.
      
      ![JoinDomain](https://github.com/user-attachments/assets/52237a3e-ee5f-4a6d-a6bc-40997f5b8588)
      
      You will then be prompted to enter the credentials of an account authorized to join this computer to the domain. Please remember that we have previously created a domain admin account for this purpose.
      
      ![promptToJoin](https://github.com/user-attachments/assets/0cb68253-6068-45df-b426-bb396218db59)
   
      After clicking OK, you will receive a welcome message indicating that you are joining the mycompany.com domain.
      
      ![WelcomeToDomain](https://github.com/user-attachments/assets/9e53ead0-f907-4e15-b882-c773d91f3ceb)
   
      Afterward, restart your Client1 PC to complete the domain joining process.
   
      Let's navigate to our domain controller by clicking Start, then Server Manager, followed by Tools, and finally selecting DHCP. In the DHCP management panel, we will locate the scope we created for our organization.

      Next, expand the scope to view Address Leases. Here, we can see a lease corresponding to our client computer. When we set up the client computer and connected it to the network, it automatically reached out to the DHCP server to request an IP address, which was successfully granted.
      
      ![addressLeases](https://github.com/user-attachments/assets/9e26e373-df11-429c-8acb-4cbdc7489960)

      Next, let's access Active Directory Users and Computers. We are still working within the domain controller virtual machine, so we will click Start, navigate to Administrative Tools, and select Active Directory Users and Computers.
      
      Within this interface, we will explore the Computers container. Here, we can observe that after successfully joining the client computer to the domain, an entry for that computer has automatically appeared. This indicates that it is now a member of the domain.
      
      ![ADComputer](https://github.com/user-attachments/assets/e339fd54-c279-45d8-a3ba-ea6c1761c9ba)
      
      Since the computer is currently joined to the domain, we can log in using any of these domain accounts, which we will proceed to do next.
      
      ![LoginUsingCreatedAccount](https://github.com/user-attachments/assets/95e94ec9-6320-455a-9f51-2ff8bad577e4)
      
      Returning to our client computer, we can see that we are successfully logged in and the desktop environment is fully operational. To verify our user status, we can open the command line and enter the command `whoami`. This will display the current user account, confirming that the username, such as `esandor`, is associated with the `mycompany` domain. This demonstrates that we are now operating as a member of the domain with the privileges assigned to the chosen account from the list we created earlier.
      
      ![LoggedSuccess](https://github.com/user-attachments/assets/b458b83d-892f-40c3-ae66-a690572e3702)

## Conclusion
   Congratulations! You have successfully set up a fully functioning Active Directory home lab environment using Oracle VirtualBox. This setup enables you to manage a Windows Server 2019 domain controller with Windows 10 client systems, offering a foundational understanding of Active Directory concepts and networked Windows environments. Throughout this tutorial, you've explored fundamental practices for deploying and managing users, computers, and roles within a domain, from configuring network settings and DNS to implementing DHCP and NAT for seamless connectivity.
   
  * By following these steps, you've gained hands-on experience with:
      * Installing and configuring Active Directory and related services.
      * Managing network interfaces and adapting settings for internal and external communication.
      * Automating user account creation and setting up a DHCP server for dynamic IP allocation.
      * Joining Windows 10 clients to the domain and verifying connectivity.
     
   This home lab provides a sandbox environment for experimenting with domain management, simulating real-world IT scenarios, and honing skills essential for IT administration and troubleshooting. Consider further exploration by testing Group Policies, managing security configurations, and practicing user and computer administration within the domain. Enjoy your learning journey, and continue to build on this foundation for more advanced AD concepts!

   ## Credits

This guide is inspired by [Josh Madakor](https://www.youtube.com/watch?v=MHsI8hJmggI). His tutorials provide foundational steps and insights for setting up and configuring Active Directory in a home lab environment.
