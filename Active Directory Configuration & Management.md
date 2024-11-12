# Setting Up Active Directory in VirtualBox

## Introduction
<div align="justify">
Active Directory (AD) is a cornerstone of modern network administration, enabling centralized control over users, computers, and security within an organization. Developed by Microsoft, AD is more than just a user management tool; it's a comprehensive framework that integrates network services such as **user authentication**, **access control**, **policy enforcement**, and **directory services**. By leveraging AD, IT administrators can define who can access what resources, enforce security standards, and apply organizational policies—all from a single, central console.

At its core, AD is organized around a **Domain Controller (DC)**, which is a server responsible for responding to security authentication requests within the AD network. The DC manages a database of users, groups, and devices and authenticates users’ access to resources within the domain. This makes it possible for network administrators to handle large numbers of users and devices with a high level of control.

Some key components of Active Directory include:

- **Domains**: These are logical groupings of objects (users, computers, devices) that share the same AD database. A domain helps manage network resources and security settings in a centralized way.
- **Organizational Units (OUs)**: These are sub-divisions within a domain, designed to group similar users or devices together, often by department or function, like IT or Security. OUs allow admins to apply specific policies and permissions to different groups.
- **Group Policy Objects (GPOs)**: GPOs define configurations and policies for users and computers within the domain. From software installations to security settings, GPOs allow admins to set up centralized, automated rules that apply across the network.

In this guide, we’ll walk through the steps to set up a virtual AD environment in **VirtualBox** that replicates a small organizational network. Here’s our configuration:

1. **Windows Server 2022** as the **Domain Controller (DC)** to manage network authentication and resource access.
2. **Two Windows 10 machines** will join the domain as client computers, allowing users to log in using domain credentials.
3. **Organizational Units (OUs)** for departments like IT and Security, each containing specific users.
4. A **Group Policy Object (GPO)** will be configured to handle software installation for all domain users, demonstrating the centralized control AD provides.

This setup provides hands-on experience with AD basics and showcases how Active Directory can enhance security, organization, and efficiency in network management. Let’s get started!
</div>
