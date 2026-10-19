# Microsoft Entra ID Integration

## Executive Summary
This phase transitions the isolated on-premises Active Directory Domain Services (AD DS) environment (`corp.local`) into a modern **Hybrid Identity** infrastructure. By provisioning an enterprise Microsoft 365 tenant and deploying **Microsoft Entra Connect Sync** on the primary Domain Controller (`DC01`), directory objects and password hashes are synchronized between on-premises and cloud environments. 

This architecture allows centralized identity lifecycle management, enabling users to authenticate across local workstations and cloud applications using single-identity workflows.

---

## Architecture & Technologies Used

- **Directory Services:** Active Directory Domain Services (AD DS) on Windows Server 2025 (`DC01`)
- **Cloud Identity Platform:** Microsoft Entra ID
- **Tenant Licensing:** Microsoft 365 Business Premium
- **Synchronization Engine:** Microsoft Entra Connect Sync
- **Network Virtualization:** Oracle VirtualBox

## Deployment Milestones 
### Entra Connect Provisioning
-   Downloaded and installed **Microsoft Entra Connect Sync** on the primary Domain Controller (`DC01`).
-   Deployed using **Express Settings** to automatically configure Password Hash Synchronization (PHS), allowing users to maintain a single set of credentials.
-   Authenticated the connection between environments using the local Enterprise Admin (`CORP\Administrator`) and the cloud Global Admin (`AnthonyV@ITProject454.onmicrosoft.com`).

### Synchronization Configuration
-   Authorized synchronization for the non-routable on-premises domain (`corp.local`) by selecting "Continue without matching all UPN suffixes." Entra ID automatically maps these users to the default tenant routing domain (`@ITProject454.onmicrosoft.com`).
-   Initiated the first full synchronization cycle during the final step of the Entra Connect setup wizard.

### Verification & Validation
-   Navigated to the **Microsoft Entra admin center** (`entra.microsoft.com`) > Identity > Users > All users.
-   Confirmed that local AD users and security groups successfully replicated to the cloud tenant.
-   Verified the **On-premises sync enabled** column successfully reported as **Yes** for all on-premises objects.

<img width="1125" height="481" alt="Entra User verification" src="https://github.com/user-attachments/assets/8e8fa09f-2e92-447c-ab2b-bae6dc17bc95" />

### Provisioning a New User in Microsoft Entra ID
- As part of managing identity and access, I provisioned a new internal user within Microsoft Entra ID. This process involved configuring the user's basic credentials, contact information, and organizational roles to ensure proper directory integration and access rights. 

<img width="458" height="795" alt="image" src="https://github.com/user-attachments/assets/ac0d322d-9353-4cc2-a310-5ab66abfaeaa" />

### Device Registration Workflow
- Configured Hybrid Azure AD Join for Windows 10/11 devices.
- Verified the Service Connection Point (SCP) was written to the AD DS configuration partition.
- Logged into the domain‑joined workstation (Client01) using a synchronized domain user.
- The device automatically contacted Microsoft Entra ID and registered itself as Hybrid Joined Type.

<img width="984" height="285" alt="image" src="https://github.com/user-attachments/assets/5530d06b-a9d5-42c5-a984-31032f0675c7" />

### Device State Validation
- On Client01, the following command was executed: (`dsregcmd /status`)
- Output confirmed successful hybrid join:

<img width="654" height="258" alt="image" src="https://github.com/user-attachments/assets/0980033f-f643-4a3b-9f5c-fae993a6b1aa" />

### Seamless SSO & MFA Enrollment 
- The SSO State section of dsregcmd /status showed:

<img width="1095" height="260" alt="image" src="https://github.com/user-attachments/assets/4f36aab4-dccd-415f-a5d7-a85332be124d" />

- Confirmed SSO State by using client01 browser to go to (`https://myaccount.microsoft.com`)
- The user was automatically signed into account and prompted to configure Multi-Factor Authentication

<img width="447" height="412" alt="image" src="https://github.com/user-attachments/assets/69f3bf2d-a1a1-4cbf-8a2a-f8557e35f1d1" />
 




