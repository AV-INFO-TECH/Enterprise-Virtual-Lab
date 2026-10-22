# Network Segmentation & Perimeter Security #

## 1. Executive Summary & Objective ##
Evolved a flat virtualized Active Directory lab into a segmented, enterprise-grade architecture using a pfSense firewall. Physically isolated tier-zero infrastructure (Domain Controller) from standard client endpoints to implement a Zero Trust security model. This project validates fundamental network engineering skills, including inter-VLAN routing, stateful firewall rule creation, centralized DNS forwarding, and DHCP relay configuration.
 
## 2. Architecture & Tools
- **Perimeter Firewall:** pfSense (VirtualBox VM)
- **Segment 1 (WAN):** NAT Interface for upstream Internet access
- **Segment 2 (dcnetwork):** Isolated Server VLAN (10.10.10.0/24) hosting Windows Server 2025 (DC01)
- **Segment 3 (clientnetwork):** Isolated Endpoint VLAN (10.10.20.0/24) hosting Windows 11 Enterprise
- **Core Protocols Configured:** IPv4 Routing, DNS Forwarding, DHCP Relay (IP Helper), TCP/UDP Port Aliasing

## 3. Deployment Milestones

### Firewall Deployment & Network Segmentation
- Deployed a pfSense virtual appliance, provisioning three distinct virtual network interface cards (NICs) to establish independent broadcast domains.
- Configured static IPv4 assignments via the pfSense CLI for the internal networks (`10.10.10.1` and `10.10.20.1`) to serve as default gateways for their respective subnets.
- Changed default admin Credentials and Renamed interfaces within the pfSense WebGUI (`dcnetwork` and `clientnetwork`) to align with enterprise documentation standards and improve rule management.
- Successfully migrated the Windows 11 client machine to the new physical endpoint network, physically isolating it from the Domain Controller.

<img width="730" height="408" alt="Firewall interface" src="https://github.com/user-attachments/assets/2e7f0275-20f6-4c70-8b88-8d4ce97e88d2" />

### DNS Forwarding & DHCP Relay Integration
- Reconfigured the Windows Server DNS service to utilize DNS Forwarders (8.8.8.8 / 1.1.1.1), allowing the Domain Controller to natively resolve external internet queries for the internal network while maintaining authority over the `.local` domain.

<img width="753" height="180" alt="DNS foward" src="https://github.com/user-attachments/assets/3d911d9b-ac42-47ae-a301-30de72f8da07" />

- Authorized and activated a secondary IPv4 scope (10.10.20.100 - 10.10.20.200) within the Windows Server DHCP Manager to service the new client subnet.
- Implemented a DHCP Relay (IP Helper) agent on the pfSense `clientnetwork` interface, successfully catching broadcast IP requests from endpoints and routing them securely across the firewall to the Domain Controller at `10.10.10.10`.
- Verified dynamic IP allocation and gateway assignment on the endpoint using `ipconfig /renew`.
  
<img width="916" height="280" alt="DHCP Scope" src="https://github.com/user-attachments/assets/5b3497f2-157c-4f79-a0b7-3e37f34d038e" />

### Zero Trust Firewall Hardening & Traffic Filtering
- Leveraged pfSense Aliases to logically group critical Active Directory service ports (`ADPorts`: 53 DNS, 88 Kerberos, 389 LDAP, 445 SMB) for streamlined rule creation.
- Enforced the Principle of Least Privilege by implementing a top-down stateful firewall rule hierarchy on the client interface:
  1. **Explicit Allow:** Permitted inbound traffic from the client subnet exclusively targeting the Domain Controller IP on the designated `ADPorts` alias.
  2. **Explicit Deny:** Blocked all other traffic originating from the client network destined for the tier-zero `dcnetwork` subnet to prevent lateral movement and unauthorized scanning.
  3. **Default Allow:** Permitted remaining non-internal traffic to egress to the WAN for standard internet browsing.

<img width="1160" height="486" alt="Firewall Rules" src="https://github.com/user-attachments/assets/d1b8e981-cce0-4485-96d0-e7448daa2274" />

### Connectivity & Routing Validation
- Conducted command-line verification using `ping` and `tracert` to validate network routing.
- Confirmed the pfSense firewall (`10.10.10.1`) successfully acts as the first hop for all outbound client traffic.

<img width="699" height="321" alt="tracert" src="https://github.com/user-attachments/assets/e7b573dd-527d-4306-adfb-3ad68d0731bf" />

- Validated the firewall rules by attempting to ping the Domain Controller from the client (successfully dropped by the explicit deny rule) while maintaining successful DNS resolution and web access to external addresses.

<img width="543" height="511" alt="Segmentation Verification" src="https://github.com/user-attachments/assets/baa3cd71-da33-452f-a9ea-914a67ac4eb7" />

