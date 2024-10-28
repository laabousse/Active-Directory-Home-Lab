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
1. **Install VirtualBox**
2. **Create Windows Server 2019 VM**
3. **Configure the Network interface controller**
4. **Configure Windows Server as Domain Controller**
5. **Install RAS/NAT for Internet Access**
6. **Create Windows 10 Enterprise VM**
7. **Join Windows 10 Client to the Domain**

## Instructions

### Step 1: Install VirtualBox
1. Download and install VirtualBox from [Oracle's website](https://www.virtualbox.org/wiki/Downloads).
2. Follow the installation instructions.
 
![VirtualBox installation](https://github.com/user-attachments/assets/0bf3d0c0-06bf-48d6-a3e3-b8deec0c2444)

### Step 2: Create Windows Server 2019 VM
1. [Download Windows Server 2019 ISO Image](https://www.microsoft.com/en-us/evalcenter/download-windows-server-2019)
1. Open VirtualBox and click on "New".
2. Name the VM, choose Windows Server 2019 ISO image then check Skip Unattended Installation".

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

4. Naming the Domain Controller:
   
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


### Step 7: Create Windows 10 Enterprise VM
1. Repeat the VM creation process for Windows 10.
2. Load the Windows 10 ISO and start the installation.
   ![Windows 10 VM Creation](path/to/screenshot_win10_vm_creation.png)
   
### Step 7: Join Windows 10 Client to the Domain
1. Go to Settings -> System -> About.
2. Click on "Join a domain".
3. Enter the domain name and follow the prompts.
   ![Join Domain Screenshot](path/to/screenshot_join_domain.png)

## Conclusion
You should now have a basic home lab running Active Directory. Feel free to contribute with additional setup steps or improvements.

