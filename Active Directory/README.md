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
<img width="1031" height="668" alt="Review Options Screenshot" src="https://github.com/user-attachments/assets/86210d32-451e-4a43-9a8e-0f7b5eae115b" />

- Provisioned an IT Department Organizational Unit (OU) within Active Directory and implemented role-based access control by adding a dedicated administrative user to the Domain Admins security group. This ensures daily administrative tasks are performed with a named account rather than the default built-in Administrator, and allows for the creation of separate, unprivileged accounts for daily use to enforce the Principle of Least Privilege (PoLP).
- Successfully joined a Windows 11 enterprise client to the domain, validating server-side DNS resolution and remote Active Directory authentication.
- Designed a tiered Organizational Unit (OU) hierarchy (e.g., `Sales` > `Managers` / `Representatives`) to cleanly separate Group Policy targets from resource access.
- Implemented logically mapped Security Groups (`SG_Sales_Managers`, `SG_Sales_Reps`) to enforce the principle of least privilege across departments.
    
<img width="841" height="537" alt="Nested OUs and SGs" src="https://github.com/user-attachments/assets/1c623b22-b89a-419c-89ad-8f03f3119df5" />

- To minimize helpdesk ticket volume and streamline user onboarding, I implemented Group Policy Objects (GPOs) to automate environment configuration based on Organizational Unit placement.
- Linked a custom GPO specifically to the `Managers` OU, ensuring policies only affect the intended department tier without impacting standard representatives.
- Utilized Group Policy Preferences (User Configuration > Windows Settings > Drive Maps) to dynamically mount the restricted `Management_Data` network share as an `M:` drive upon user login. 
- Verified successful client-side policy updates (via `gpupdate /force`) and confirmed that standard users in adjacent OUs do not receive the mapped drive, proving proper policy scoping.

## 4. Incident Response & Troubleshooting
- **Issue:** VirtualBox unattended installation scripts created a partition conflict with the Windows Server 2025 ISO, resulting in a setup loop during the initial server build.
- **Resolution:** Bypassed the graphical interface to access the command line, utilized the diskpart utility to wipe the corrupted virtual disk, and executed a manual OS installation.

