# Enterprise Helpdesk Deployment

## 1. Executive Summary & Objective ##
Deployed an enterprise-grade ticketing system (osTicket) on a Linux LAMP stack to simulate a real-world IT support environment. Integrated the web application with a Windows Server Active Directory domain via LDAP to enable Single Sign-On (SSO) and implement Role-Based Access Control (RBAC). This project validates core systems administration skills, including Linux server management, database provisioning, Active Directory integration, and Group Policy management.
 
## 2. Architecture & Tools
- **Server OS:** Ubuntu Server (Linux)
- **Web Infrastructure:** LAMP Stack (Apache, MariaDB, PHP)
- **Application:** osTicket
- **Identity Provider:** Windows Server Active Directory (`corp.local` domain)
- **Network Integration:** Dedicated Application Subnet (10.10.30.0/24) 
- **Core Protocols & Concepts:** LDAP, Single Sign-On (SSO), RBAC, Group Policy (GPO), Service Accounts

## 3. Deployment Milestones

### Ubuntu Server Provisioning & Network Configuration

* Deployed an Ubuntu Server virtual machine to act as the primary application host for the helpdesk environment.
* Configured a static IPv4 address (`10.10.30.10`) for the server, establishing it within a dedicated application network segment to maintain proper logical separation from the tier-zero Domain Controller and client endpoints.
* Enabled and secured the OpenSSH server to allow remote command-line administration, consistently managing the deployment via Windows PowerShell using a dedicated `labadmin` account.

<img width="606" height="146" alt="image" src="https://github.com/user-attachments/assets/29da54b2-2b93-4d7b-bf59-91bbe43542a3" />

### LAMP Stack Provisioning & Database Configuration
- Engineered a robust LAMP stack to support dynamic web operations, and configuring the Apache web server.
- Deployed MariaDB and initialized a dedicated relational database (`osticket_db`) to securely store helpdesk backend data and user metrics.
- Enforced the Principle of Least Privilege (PoLP) by provisioning a dedicated service account (`osticket_user`) with strictly scoped permissions (`GRANT ALL PRIVILEGES ON osticket_db.*`), mitigating unauthorized root access.

### osTicket Installation & Service Configuration
- Orchestrated the application initialization via the web-based setup utility, proactively diagnosing and remediating database connectivity and PHP module dependencies by `sudo apt install libapache2-mod-php -y` 
- Hardened the production environment post-deployment by locking down critical configuration files (`chmod 0644`) and purging residual installation directories to eliminate default web vulnerabilities.
- Architected the enterprise support framework by establishing role-based departments (e.g., Tier 1 Helpdesk), defining incident routing topics, and enforcing strict Service Level Agreements (SLAs) for ticket resolution.

<img width="730" height="305" alt="image" src="https://github.com/user-attachments/assets/34ad732e-5b06-40d7-a20b-e940b836b9b6" />

### LDAP Integration & Active Directory Authentication
- Deployed the official osTicket LDAP plugin to bridge the Linux-based web application with the Windows Server Active Directory environment.
- Configured service bindings and dual-layer authentication routing, enabling seamless Single Sign-On (SSO) for client portal access while restricting the administrative Staff Panel.
- **Security Note:** In this lab environment, LDAP signing requirements were temporarily relaxed via Group Policy (GPO) to allow standard port 389 bindings. For a production deployment, I would deploy Active Directory Certificate Services (AD CS) to enforce LDAPS (port 636), ensuring credential encryption in transit without compromising Domain Controller GPO hardening. (Will exploit in future lab) 

<img width="719" height="829" alt="image" src="https://github.com/user-attachments/assets/d362c8f9-399f-4cdb-aa01-813856116abb" />

### Single Sign-On (SSO) & Role-Based Access Control (RBAC)
- Synchronized AD user attributes (like `sAMAccountName` and Email) to seamlessly provision accounts.
- Mapped specific IT staff Active Directory accounts to osTicket Agent profiles, restricting backend system access exclusively to the IT Organizational Unit.

<img width="749" height="173" alt="image" src="https://github.com/user-attachments/assets/47083a3f-6e9e-4f12-9488-ed607265b570" />
<img width="723" height="273" alt="image" src="https://github.com/user-attachments/assets/35b86f37-a41f-4974-8db2-0b9781f3bc53" />

- Validated the SSO integration by successfully logging into the Staff Panel using standard Windows domain credentials, bypassing the need for local application passwords.

