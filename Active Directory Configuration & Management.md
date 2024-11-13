# Setting Up Active Directory in VirtualBox

## Introduction
<div align="justify">

**Active Directory (AD)** is a foundational tool for centralized network management, created by Microsoft to streamline control over users, computers, and security within organizations. AD allows administrators to manage access, apply security policies, and maintain directory services from one central location.

At the heart of AD is the **Domain Controller (DC)**, a server that authenticates user access and manages a database of users, groups, and devices. This enables efficient and secure handling of network resources across numerous users and devices.

### Key AD Components:
- **Domains**: Logical groups of users and devices that share a database, enabling centralized management of resources and security.
- **Organizational Units (OUs)**: Subdivisions within a domain, often organized by department or function (e.g., IT, Security), where specific policies can be applied.
- **Group Policy Objects (GPOs)**: Rules and configurations that admins set to manage software installations, security, and other settings across the network.

With AD, IT teams can set policies, enforce security, and simplify user access—keeping network management efficient and consistent.


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
**Note:** This article was created after the installation process was completed, which is why the status is shown as (Installed). Additionally, the following two screenshots were taken from a different environment, so the domain names does not match with the one in this setup.

5. Check `Active Directory Domain Services` and click **Next**. A pop-up will prompt you to add additional features – click **Add Features**.

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



## Deploy Software Using Group Policy (GPO)

In this section, we will use Group Policy to deploy software to computers and users. In this example, we will deploy Mozilla Firefox to computers via Group Policy. The steps in this example will work with other MSI files.

### Create a Network Share for the MSI File

1. Create a folder on any computer or server that everyone can access to the shared folder. For example, we’ll create a folder named `Software` on a domain controller. You can name your folders anything you want.
2. Right-click on the folder, then select Properties.
<p align="center">
     <img src="https://github.com/Md-Jamiul-Haque/Active-Directory-Project/blob/main/Pictures/properties.PNG" width="30%"/>
</p>

3. Select the Sharing tab then click Advanced Sharing option.

<p align="center">
     <img src="https://github.com/Md-Jamiul-Haque/Active-Directory-Project/blob/main/Pictures/sharing.PNG" width="30%"/>
</p>

4. On the Advanced Sharing wizard, check the box to share this folder. The share name can be anything you want.

<p align="center">
     <img src="https://github.com/Md-Jamiul-Haque/Active-Directory-Project/blob/main/Pictures/advanced-sharing.PNG" width="30%"/>
</p>  

5. Now hit ok and click on the `Share` button then grant Read/Write permission for Everyone group and click Share.
<p align="center">
     <img src="https://github.com/Md-Jamiul-Haque/Active-Directory-Project/blob/main/Pictures/everyone.PNG" width="30%"/>
</p>

6. Now copy the MSI files to the shared folder you have just created.


### Create GPO to to Deploy Software

**Group Policy: Targeting Computers for Software Deployment**

Group Policy provides a powerful way to manage and control both user and computer configurations across the domain. For deploying software, we’ll focus on targeting **computers**, ensuring the application installs at the system level. This means that anyone who logs onto a targeted computer will have the software readily available—no additional setup needed per user.

> **Pro Tip**: Always create a **new Group Policy Object (GPO)** specifically for software installations. This keeps things organized and avoids cluttering the **Default Domain Policy** with specific settings.

For example, we’ve created an OU for the computers of IT team. Then we’ll deploy the app into all computers of this OU. In this case I have only one computer in IT Computer OU.

<p align="center">
     <img src="https://github.com/Md-Jamiul-Haque/Active-Directory-Project/blob/main/Pictures/link.PNG" width="40%"/>
</p>

1. Open Group Policy Management console then navigate to the OU. Right click and select Create a GPO in this domain, and link it here.

<p align="center">
     <img src="https://github.com/Md-Jamiul-Haque/Active-Directory-Project/blob/main/Pictures/create-gpo.PNG" width="40%"/>
</p>

2. Give the GPO a descriptive name (eg. `Software-Deployment`), then right click on the newly created GPO to edit it.

<p align="center">
     <img src="https://github.com/Md-Jamiul-Haque/Active-Directory-Project/blob/main/Pictures/edit-gpo.PNG" width="40%"/>
</p>

3. Navigate to Computer `Configuration > Policies > Software Settings > Software installation` then right click New > Package… to add the MSI installation files from the network shared folder we’ve created in the previous steps.

<p align="center">
     <img src="https://github.com/Md-Jamiul-Haque/Active-Directory-Project/blob/main/Pictures/package.PNG" width="40%"/>
</p>

4. Browse to the network share using the UNC path. In my case it is `\\DC-01\Software`. Select the MSI you want to install, and click open.
5. On the Deploy Software wizard, click `Assigned` and then click OK.

<p align="center">
     <img src="https://github.com/Md-Jamiul-Haque/Active-Directory-Project/blob/main/Pictures/assigned.PNG" width="40%"/>
</p>

6. With that, our GPO configuration is all set! The final settings should look like this.

<p align="center">
     <img src="https://github.com/Md-Jamiul-Haque/Active-Directory-Project/blob/main/Pictures/final-gpo.PNG" width="45%"/>
</p>

### Installing software using Group Policy

The software installation will occur on the next reboot, but first, the computer needs to refresh its GPO settings. By default, GPO settings update every 90 minutes, but to apply the changes immediately, you can run the `gpupdate /force` command on the client machine.

After running gpupdate, you may see a prompt indicating that some settings require a system restart or user logon to take effect. This message is referring to the software deployment via GPO, so go ahead and type `Y` to restart the computer and complete the installation.

<p align="center">
     <img src="https://github.com/Md-Jamiul-Haque/Active-Directory-Project/blob/main/Pictures/force-update.PNG" width="60%"/>
</p>

When users log in, they’ll see the app’s icon on the desktop, confirming that the software has successfully installed.

<p align="center">
     <img src="https://github.com/Md-Jamiul-Haque/Active-Directory-Project/blob/main/Pictures/firefox-installed.PNG" width="45%"/>
</p>


## Conclusion
<div align="justify">
In this guide, you've learned how to set up Active Directory in VirtualBox, including configuring a Domain Controller, creating Organizational Units (OUs), and applying Group Policy Objects (GPOs) for centralized software installation. This hands-on experience provides a solid foundation for managing and securing network resources efficiently.

Active Directory is a key tool for IT administration, and mastering its basics helps ensure streamlined network management and security. With these steps, you’ve taken the first step toward becoming proficient in managing organizational networks.
</div>
