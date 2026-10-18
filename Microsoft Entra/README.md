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
