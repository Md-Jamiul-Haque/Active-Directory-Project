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

### Step 3: Promoting the server to a Domain Controller (Post-Deployment)

1. Back in the Server Manager, notice a yellow **caution flag** icon at the top right corner. Click on it, and you'll see an option for **Promote this server to a domain controller**. Click this to start the AD configuration wizard.
   <p align="center">
     <img src="https://github.com/Md-Jamiul-Haque/Active-Directory-Project/blob/main/Pictures/promoting-domain-controller.png" width="60%"/>
   </p>

### Step 5: Create a New Forest and Set Up Domain Details

1. Under **Deployment Configuration**, select **Add a new forest**.
2. Enter a name for your root domain (e.g., `MYDOMAIN.local` or anything you prefer).
3. Click **Next** to proceed to the **Domain Controller Options**.



### Step 6: Set Domain Controller Options

1. Here, you'll choose the **Forest and Domain Functional Level** (leave at the default if unsure).
2. Check the **Domain Name System (DNS) server** box if it's not already checked.
3. In the **Directory Services Restore Mode (DSRM)** password field, set a password that you can easily remember.
4. Click **Next** to continue, leaving the remaining options at their default settings.



### Step 7: Complete Installation and Reboot

1. Click **Install** to finalize the AD setup. The server will automatically reboot once the process is complete.
2. After rebooting, your VM's default local administrator account will now be part of the domain. Log in with your domain credentials, formatted as `MYDOMAIN\Administrator`.


Your server is now a domain controller for your network, ready for managing users, devices, and security policies within your Active Directory domain. With AD DS fully set up, you have established the backbone for network-wide management and security.

> **Next Steps**: Let's move on to creating organizational units (OUs), adding users, and setting up group policies for centralized management.

---


