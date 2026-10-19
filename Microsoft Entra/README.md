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

