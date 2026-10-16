# Active Directory Project #

## 1. Executive Summary & Objective ##
Deployed a Windows Server 2025 Domain Controller in an isolated virtual network to manage centralized authentication and simulate enterprise network administration. This environment serves as a foundation for testing Identity and Access Management (IAM) controls and security baselines.
 
## 2. Architecture & Tools.
- **Hypervisor:** Oracle VirtualBox
- **Operating System:** Windows Server 2025 Standard (Desktop Experience) configured as hostname: DC01
- **Network:** Isolated Internal Network (10.10.10.0/24)
- **DNS & Identity:** Local Loopback (127.0.0.1) / Active Directory Domain Services

## 3. Deployment Milestones
- Configured a static IPv4 address (10.10.10.10) for the Windows Server domain controller (DC01) and designated the local loopback for DNS resolution. 
- Installed the Active Directory Domain Services (AD DS) onto the server.
- Promoted the server to the primary Domain Controller for the new forest.
- Provisioned an IT Department Organizational Unit (OU) within Active Directory and implemented role-based access control by adding a dedicated administrative user to the Domain Admins security group. This ensures daily administrative tasks are performed with a named account rather than the default built-in Administrator, and allows for the creation of separate, unprivileged accounts for daily use to enforce the Principle of Least Privilege (PoLP).  

## 4. Incident Response & Troubleshooting
- **Issue:** VirtualBox unattended installation scripts created a partition conflict with the Windows Server 2025 ISO, resulting in a setup loop during the initial server build.
- **Resolution:** Bypassed the graphical interface to access the command line, utilized the diskpart utility to wipe the corrupted virtual disk, and executed a manual OS installation.

