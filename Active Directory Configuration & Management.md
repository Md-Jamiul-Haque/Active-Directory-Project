# Setting Up Active Directory in VirtualBox

## Introduction
<div align="justify">
Active Directory (AD) is a cornerstone of modern network administration, enabling centralized control over users, computers, and security within an organization. Developed by Microsoft, AD is more than just a user management tool; it's a comprehensive framework that integrates network services such as **user authentication**, **access control**, **policy enforcement**, and **directory services**. By leveraging AD, IT administrators can define who can access what resources, enforce security standards, and apply organizational policies—all from a single, central console.

At its core, AD is organized around a **Domain Controller (DC)**, which is a server responsible for responding to security authentication requests within the AD network. The DC manages a database of users, groups, and devices and authenticates users’ access to resources within the domain. This makes it possible for network administrators to handle large numbers of users and devices with a high level of control.

Some key components of Active Directory include:

- **Domains**: Logical groupings of objects (users, computers, devices) that share the same AD database, managing network resources and security settings in a centralized way.
- **Organizational Units (OUs)**: Sub-divisions within a domain designed to group similar users or devices, often by department or function, like IT or Security. OUs allow admins to apply specific policies and permissions to different groups.
- **Group Policy Objects (GPOs)**: GPOs define configurations and policies for users and computers within the domain. From software installations to security settings, GPOs allow admins to set up centralized, automated rules that apply across the network.

In this guide, we’ll walk through the steps to set up a virtual AD environment in **VirtualBox** that replicates a small organizational network. Here’s our configuration:

- A **Windows Server 2022** machine acting as the **Domain Controller (DC)**
- **Two Windows 10** virtual machines configured as domain users
- Creation of **Organizational Units (OUs)**, each containing users from different departments
- A **Group Policy Object (GPO)** for centralized software installation

This setup provides hands-on experience with AD basics and showcases how Active Directory can enhance security, organization, and efficiency in network management. Let’s get started!
</div>

## Installing Windows Server 2022 & Windows 10 Machine in Virtualbox

### Step 1: Create a Virtual Machine
1. Open **VirtualBox** and click **New** to create a new VM. Give a clear name eg. `Domain Controller`.
2. Select a folder where you want to store the VM
3. Select the ISO image you downloaded
4. Skip unattended installation
5. Allocate **RAM (4GB recommended)** and adjust processors based on the host machine’s capacity.
6. Select **Create Virtual Hard Disk Now** and allocate disk space (50GB recommended)
7. Then hit Finish, start the VM & follow the installation process
8. Please make sure to slect `Windows Server Standard Desktop Experience`.

Follow the same instructions to install two Windows 10 VM, you can name the machines as `Win-Host-1` & `Win-Host-2` and for the ISO image select **Windows 10 ISO**

### Configure a Static IP

1. Open **Network & Internet Settings** > **Ethernet** > **Change adapter options** on each Windows machine.
   <p align="center">
     <img src="https://github.com/Md-Jamiul-Haque/Active-Directory-Project/blob/main/Pictures/net-internet.PNG" width="40%"/>
   </p>
3. Right-click on **Ethernet** > **Properties** > **Internet Protocol Version 4 (TCP/IPv4)** > **Properties**.
   <p align="center">
     <img src="https://github.com/Md-Jamiul-Haque/Active-Directory-Project/blob/main/Pictures/adapter.PNG" width="40%"/>
   </p>
5. Select **Use the Following IP address** & set a unique static IP for each machine (e.g., `10.27.221.245` for DC, `10.27.221.240` for Client1 and `10.27.221.241` for CLient2):
   - **IP Address**: `10.27.221.245` (replace it with your unique IP)
   - **Subnet Mask**: `255.255.255.0`
   - **Default Gateway**: `10.27.221.3` (replace it with your network's default gateway. Open cmd and type `ipconfig` to find default gateway)
   - **Preferred DNS Server**: `127.0.0.1` (because it is a Domain Controller, for the domain user use DC's IP address as DNS server)
   - Hit ok.
   <p align="center">
     <img src="https://github.com/Md-Jamiul-Haque/Active-Directory-Project/blob/main/Pictures/static-ip.PNG" width="30%"/>
   </p>
  
## Setting Up Active Directory Domain Services (AD DS) on Windows Server 2022

In this section, we'll dive into installing and configuring Active Directory Domain Services (AD DS) on your Windows Server 2022 virtual machine. This setup will turn your server into a domain controller, enabling centralized user management and authentication across the network. Let's get started with the step-by-step process!



### Step 1: Open Server Manager and Begin Installation

1. Launch **Server Manager** from your Windows Server 2022 dashboard.
   <p align="center">
     <img src="https://github.com/Md-Jamiul-Haque/Active-Directory-Project/blob/main/Pictures/server-manager.PNG" width="50%"/>
   </p>
3. In the Server Manager, click on **Add roles and features**.
4. The **Add Roles and Features Wizard** will appear. For the first three steps, simply click **Next** to keep the default settings.
   <p align="center">
     <img src="https://github.com/Md-Jamiul-Haque/Active-Directory-Project/blob/main/Pictures/role-features.PNG" width="50%"/>
   </p>
**Note:** Since I’m writing this article after completing the installation, the status is showing as (Installed).
5. Check **Active Directory Domain Services** and click **Next**. A pop-up will prompt you to add additional features – click **Add Features**.
6. Click **Next** to proceed, leaving all subsequent options at their default settings.
7. Click **Install** to begin installing Active Directory Domain Services. After installation close the wizard.

Your AD DS role is now installed, but to fully set up AD, we need to configure this server as a domain controller. Let's continue to the next steps.

### Step 2: Promoting the server to a Domain Controller (Post-Deployment)

Back in the Server Manager, notice a yellow **caution flag** icon at the top right corner. Click on it, and you'll see an option for **Promote this server to a domain controller**. Click this to start the AD configuration wizard.
   <p align="center">
     <img src="https://github.com/Md-Jamiul-Haque/Active-Directory-Project/blob/main/Pictures/promoting-domain-controller.png" width="60%"/>
   </p>
   
1. Under **Deployment Configuration**, select **Add a new forest**.
2. Enter a name for your root domain (e.g., `MYDOMAIN.local` or anything you prefer). The domain name must have a top level domain.
   <p align="center">
     <img src="https://github.com/Md-Jamiul-Haque/Active-Directory-Project/blob/main/Pictures/add-new-forest.png" width="65%"/>
   </p>
4. Click **Next** to proceed to the **Domain Controller Options**.
5. Here, leave at the default if unsure.
6. Check the **Domain Name System (DNS) server** box if it's not already checked.
7. In the **Directory Services Restore Mode (DSRM)** password field, set a password that you can easily remember.
8. Click **Next** to continue, leaving the remaining options at their default settings.
9. Click **Install** to finalize the AD setup. The server will automatically reboot once the process is complete.
10. After rebooting, your VM's default local administrator account will now be part of the domain. Log in with your domain credentials, formatted as `MYDOMAIN\Administrator`.


Your server is now a domain controller for your network, ready for managing users, devices, and security policies within your Active Directory domain. 
> Let's move on to creating organizational units (OUs), adding users, and setting up group policies for centralized management.

## Organizing Active Directory with OUs and User Accounts

Now that Active Directory is set up, let’s organize it by creating two Organizational Units (OUs) and adding a user to each. This structure helps us manage departments, users, and group policies more efficiently. 

### Creating Organizational Units (OUs)

1. **Open Active Directory Users and Computers**:
   - Navigate to **Server Manager** > **Tools** > **Active Directory Users and Computers**. This opens the AD management console.

2. **Create the HR OU**:
   - Right-click on your domain name (e.g., `MYDOMAIN.local`) in the left-hand panel.
   - Select **New** > **Organizational Unit**.
   <p align="center">
     <img src="https://github.com/Md-Jamiul-Haque/Active-Directory-Project/blob/main/Pictures/OU.PNG" width="50%"/>
   </p>
   - Enter **HR** as the name for the OU.
   - Ensure **Protect container from accidental deletion** is checked.
   - Click **OK** to create the HR OU.
   <p align="center">
     <img src="https://github.com/Md-Jamiul-Haque/Active-Directory-Project/blob/main/Pictures/HR.PNG" width="40%"/>
   </p>

3. Create another OU by following the same steps.

### Adding Users to OU

1. **Add a User to the HR OU**:
   - Right-click on the **HR** OU, select **New** > **User**.
   - In the **New Object - User** wizard:
     - Enter **First Name**: `First`
     - **Last Name**: `Name`
     - **User Logon Name**: `username`
   - Click **Next**.
   <p align="center">
     <img src="https://github.com/Md-Jamiul-Haque/Active-Directory-Project/blob/main/Pictures/user.PNG" width="40%"/>
   </p> 
   - Enter and confirm a password for the user.
   - Uncheck **User must change password at next logon** if you want to set a permanent password.
   <p align="center">
     <img src="https://github.com/Md-Jamiul-Haque/Active-Directory-Project/blob/main/Pictures/pass.PNG" width="40%"/>
   </p> 
   - Click **Next** and then **Finish** to create the user.


By creating these OUs and users, you now have an organized structure in Active Directory, which will make managing permissions and policies easier as your domain grows.



## Joining to the Domain as a user

With our Active Directory setup complete, let’s add the Windows 10 client machines to the domain. This step allows domain users to log into these machines with their AD credentials, enabling centralized control and management.

1. On the Windows 10 machine, right-click **This PC** on the desktop and select **Properties**.
2. In the **System** window, click on **Advanced system settings**.
3. In the **System Properties** dialog box, select the **Computer Name** tab and click on **Change** (Before joining the domain, it’s good practice to give each machine a unique name)
4. In the **Computer Name** field, enter a name (e.g., `WIN-HOST-1`) & Click **OK**
5. You need to restart the computer before proceeding.
<p align="center">
     <img src="https://github.com/Md-Jamiul-Haque/Active-Directory-Project/blob/main/Pictures/host-name.PNG" width="30%"/>
</p>   

6. Go to **System Properties** window and `Computer Name` tab, click on **Change**.
<p align="center">
     <img src="https://github.com/Md-Jamiul-Haque/Active-Directory-Project/blob/main/Pictures/join-domain.PNG" width="30%"/>
</p> 
7. Select **Domain** under the **Member of** section.
<p align="center">
     <img src="https://github.com/Md-Jamiul-Haque/Active-Directory-Project/blob/main/Pictures/domain.PNG" width="30%"/>
</p>
8. Type your domain name (e.g., `MYDOMAIN.local`) and click **OK**.
9. A dialog box will prompt you for a username and password with permission to join the domain.
10. Enter the **Domain Administrator** credentials (e.g., `MYDOMAIN\Administrator`), then click **OK**.
11. Once authentication is successful, you’ll see a welcome message to the domain.
12. Click **OK** on the confirmation message.
13. You’ll be prompted to restart the computer—go ahead and **Restart Now** to apply the changes.


After restarting, you can log in as a domain user:

1. On the login screen, select **Other user**.
2. Enter the domain user’s credentials in the format:  
   `MYDOMAIN\username`  
   Enter the `password` for this user.
3. Click **Enter** to log in. The domain user is now authenticated and logged into the machine.


With these steps, you’ve successfully connected a Windows 10 machine to the domain. You can follow the same process for additional Windows 10 machines.

