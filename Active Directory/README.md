# Active Directory Project #

## 1. Executive Summary & Objective ##
Deployed a Windows Server 2025 Domain Controller in an isolated virtual network to manage centralized authentication and simulate enterprise network administration. This environment serves as a foundation for testing Identity and Access Management (IAM) controls and security baselines.
 
## 2. Architecture & Tools.
- **Hypervisor:** Oracle VirtualBox
- **Operating System:** Windows Server 2025 Standard (Desktop Experience) configured as hostname: DC01
- **Network:** Isolated Internal Network (10.10.10.0/24)
- **DNS & Identity:** Local Loopback (127.0.0.1) / Active Directory Domain Services

## 3. Deployment Milestones

### Network & Directory Foundation
- Configured a static IPv4 address (10.10.10.10) for the Windows Server domain controller (DC01) and designated the local loopback for DNS resolution. 
- Installed the Active Directory Domain Services (AD DS) onto the server.
- Promoted the server to the primary Domain Controller for the new forest.
<img width="1031" height="668" alt="Review Options Screenshot" src="https://github.com/user-attachments/assets/86210d32-451e-4a43-9a8e-0f7b5eae115b" />

- Successfully joined a Windows 11 enterprise client to the domain, validating server-side DNS resolution and remote Active Directory authentication.
  
### Role-Based Access Control (RBAC)
- Provisioned an IT Department Organizational Unit (OU) within Active Directory and implemented role-based access control by adding a dedicated administrative user to the Domain Admins security group. This ensures daily administrative tasks are performed with a named account rather than the default built-in Administrator, and allows for the creation of separate, unprivileged accounts for daily use to enforce the Principle of Least Privilege (PoLP).
- Designed a tiered Organizational Unit (OU) hierarchy (e.g., `Sales` > `Managers` / `Representatives`) to cleanly separate Group Policy targets from resource access.
- Implemented logically mapped Security Groups (`SG_Sales_Managers`, `SG_Sales_Reps`) to enforce the principle of least privilege across departments.
    
<img width="841" height="537" alt="Nested OUs and SGs" src="https://github.com/user-attachments/assets/1c623b22-b89a-419c-89ad-8f03f3119df5" />

### Group Policy Automation
- Linked a custom GPO specifically to the `Managers` OU, ensuring policies only affect the intended department tier without impacting standard representatives.
- Utilized Group Policy Preferences (User Configuration > Windows Settings > Drive Maps) to dynamically mount the restricted `Management_Data` network share as an `M:` drive upon authorized user login. 
- Verified successful client-side policy updates (via `gpupdate /force`) and confirmed that standard users in adjacent OUs do not receive the mapped drive, proving proper policy scoping.

### Secure Admin Workstation (SAW) & Endpoint Lockdown
- Configured the Windows 11 client as a Secure Admin Workstation using RSAT to avoid logging directly into the Domain Controller for routine management.
- Utilized the `LabAdmin` account to remotely manage Active Directory and Group Policy without exposing the server GUI.
- Created and linked the `Lockdown Endpoint Settings` GPO specifically to the standard employee OUs.
- Navigated `User Configuration > Policies > Administrative Templates > Control Panel` to enable the "Prohibit access to Control Panel and PC settings" directive.

<img width="1063" height="427" alt="Lockdown GPO" src="https://github.com/user-attachments/assets/18939319-684d-4e5d-aaea-9f29aff61c21" />

- Verified that standard users attempting to modify system configurations are successfully blocked by a system administrator restriction prompt, ensuring endpoint compliance and reducing potential helpdesk tickets.

### Identity and Access Management (IAM)  
- Hardened the Active Directory domain authentication requirements to establish a baseline defense against brute-force attacks and compromised credentials. 
- Modified the `Default Domain Policy` (Computer Configuration > Security Settings > Account Policies) to enforce a minimum password length of 12 characters and mandate complexity requirements.
- Configured the Account Lockout Policy to automatically lock user accounts for 10 minutes after 5 consecutive invalid logon attempts.

<img width="1066" height="395" alt="PW Policy" src="https://github.com/user-attachments/assets/d3561ab8-43db-42bc-b1db-5d4994ed89eb" />

- Verified enforcement at the endpoint level by attempting a password change that violated the new entropy rules, successfully triggering the domain security block.

<img width="652" height="286" alt="Failed PW" src="https://github.com/user-attachments/assets/cb085389-98fe-4d96-9e01-cb92aa6ff060" />

### Automated Software Deployment
- Configured an automated software deployment architecture using Group Policy to streamline endpoint provisioning.
- Utilized remote Computer Management via RSAT to provision a dedicated, read-only network file share (`\\dc01\Software_Deploy`) to host enterprise MSI installers.
- Created the `7-zip` policy linked at the domain level, utilizing `Computer Configuration > Software Settings` to assign the 64-bit 7-Zip MSI package via its UNC network path.
  
<img width="1067" height="496" alt="Software policy" src="https://github.com/user-attachments/assets/7dc3a6a2-3016-440c-8128-3df2f5361eef" />

- Rebooted the Windows 11 endpoint and successfully verified the silent, zero-touch installation of the software prior to user authentication.

## 4. Troubleshooting
- **Issue:** VirtualBox unattended installation scripts created a partition conflict with the Windows Server 2025 ISO, resulting in a setup loop during the initial server build.
- **Resolution:** Bypassed the graphical interface to access the command line, utilized the diskpart utility to wipe the corrupted virtual disk, and executed a manual OS installation.
  
- **Issue:** When configuring the Windows 11 client VM to use the isolated internal Active Directory network, the virtual machine inherently lost external internet connectivity. This prevented the download of necessary Windows Optional Features (specifically RSAT) from Microsoft's servers.
- **The Resolution:** Maintained the primary Internal Network adapter (Adapter 1) to preserve domain communication, and enabled a secondary virtual Network Adapter (Adapter 2) configured for NAT to establish a bridge to the host's internet.

- **The Issue:** When attempting to provision network shares on the Domain Controller (`DC01`) remotely from the Secure Admin Workstation (`Client01`), the connection was rejected. By design, Windows Server maintains a restricted attack surface by blocking incoming DCOM and RPC traffic required for remote Microsoft Management Console (MMC) snap-ins.

<img width="403" height="293" alt="Screenshot 2026-10-17 131815" src="https://github.com/user-attachments/assets/9eb270f7-0513-416e-8f03-cc338d6c3874" />

- **The Resolution:** Accessed Windows Defender Firewall with Advanced Security on the Domain Controller to explicitly permit secure administration traffic. Enabled the `COM+ Network Access (DCOM-In)` and `Remote Event Log Management` inbound rules to allow secure communication from the internal lab network. Successfully connected the remote Computer Management console from the Windows 11 endpoint to the server, allowing for the creation and permissioning of the `Software_Deploy` repository without requiring interactive server logon.

- **Issue:** Identified an issue where Windows 11 Fast Logon optimizations bypassed synchronous network policy processing.
- **Resolution:** Rectified this by enabling the "Always wait for the network at computer startup and logon" Administrative Template, successfully forcing the endpoint to retrieve the MSI payload prior to presenting the user login interface.
