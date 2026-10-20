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
- As part of managing identity and access, I provisioned a new internal user within Microsoft Entra ID. This process involved configuring the user's basic credentials, contact information, and organizational roles to ensure proper directory integration and access rights. (Cloud Only User) 

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

## Microsoft Intune Integration 
- Verified that the appropriate Microsoft 365 Business Premium licenses were assigned to the synced users to authorize MDM features.
- Configured a Group Policy for on-premise domain joined workstations for auto MDM Enrollment

<img width="1226" height="285" alt="Auto MDM Enroll" src="https://github.com/user-attachments/assets/208693a5-5270-4c4e-8631-ec716b09b903" />

- Configured a Group Policy Object to the domain to register domain computers as devices

<img width="1258" height="267" alt="image" src="https://github.com/user-attachments/assets/af2da995-964e-4e09-99e7-098305e0753b" />

- Executed gpupdate /force on Client01 to pull the new policy
- Personal laptop operating outside of the corp.local domain was utilized.
- Accessed Settings > Accounts > Access work or school and signed in using the cloud-synced lab admin credentials.
- The device authenticated with Microsoft Entra ID and successfully provisioned itself as Microsoft Entra registered (as opposed to Hybrid Joined).
- Because the MDM user scope was active, Intune automatically hooked into the registration process, bringing the personal device under management while keeping personal and corporate data separated.
- Confirmed both devices are registered and monitored via intune admin center 

<img width="1444" height="335" alt="image" src="https://github.com/user-attachments/assets/b3a62975-3e81-4370-9b5f-fbe7381aa319" />

### Endpoint Security & Device Compliance
- Policy Creation: Navigated to the Microsoft Intune admin center > Devices > Compliance and created a new policy specifically for the Windows 10 and later platform
- Configured the policy to require that windows firewall to be active 
- Set the policy rules to mark devices as noncompliant immediately upon failing any evaluation check.
- Deployed the baseline policy to all users

<img width="787" height="677" alt="image" src="https://github.com/user-attachments/assets/1c99779e-7f75-49e8-a986-3be23dd0e707" />

- Disabled firewall on client01 VM machine and performed a manual device sync to the AD
- Verified Compliance on MS Entra and Intune 

<img width="1508" height="311" alt="image" src="https://github.com/user-attachments/assets/665a7f9c-92bf-4188-8dbb-7996e2d6cf91" />
<img width="1064" height="271" alt="image" src="https://github.com/user-attachments/assets/5938bc22-4b14-4375-b5e8-314b96f9bb72" />

- Re-enabled firewall to restore compliance and verified with entra/intune admin portal 




 




