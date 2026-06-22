# On-Premises Active Directory Infrastructure & Helpdesk Integration Lab

## 📌 Project Overview
In this project, I designed, deployed, and configured an end-to-end isolated enterprise IT environment using VMware Workstation. I divided my deployment into two core phases: 
1. **Windows Server Infrastructure:** Building an on-premises Windows Server 2022 Active Directory domain controller, setting up network boundaries, establishing automated user lifecycle provisioning via PowerShell, and enforcing endpoint security policies using Group Policy Objects (GPOs).
2. **Linux & Helpdesk Integration:** Deploying a dedicated Ubuntu Linux helpdesk server running osTicket, configuring a LAMP environment, resolving advanced web server execution policies, and testing cross-platform LDAP authentication directory lookups.

---

## 🛠️ Technologies & Skills Gained
* **Virtualization & Isolation:** VMware Workstation Pro/Player, Private LAN Segments, NAT Adapter Multiplexing.
* **Windows Server Administration:** Active Directory Domain Services (AD DS), Domain Name System (DNS), Dynamic Host Configuration Protocol (DHCP) Scopes.
* **Automation & Scripting:** PowerShell ISE automation, CSV-driven identity provisioning.
* **Endpoint Hardening:** Group Policy Objects (GPOs), system restriction parameters.
* **Linux System Administration:** Ubuntu Server CLI, Apache web server optimization, MariaDB relational databases, OpenSSH configuration, network debugging (`ip a`).
* **Web Services & Apps:** PHP compilation extensions, osTicket Helpdesk implementation, secure file copying via `scp`.
* **Directory Protocols:** LDAP (Lightweight Directory Access Protocol) schema bridges.

---

## 📐 Network Architecture & Design
To prevent conflicts with my live home network (such as consumer routers handing out conflicting DHCP addresses), I strictly confined the core infrastructure to an isolated internal virtual network segment named `AD-Lab`.

### Infrastructure Scheme
| Device Hostname | Operating System | Network Segment Configuration | Core Network Roles | IP Address Assignment | DNS Mapping |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Domain Controller** | Windows Server 2022 | Isolated `AD-Lab` Segment | AD DS, DNS Server, DHCP Scope Master | `192.168.10.10` (Static) | `127.0.0.1` (Loopback) |
| **Client Workstation** | Windows 11 Enterprise | Isolated `AD-Lab` Segment | Corporate Managed Endpoint | `192.168.10.50` - `200` (DHCP) | `192.168.10.10` |
| **Helpdesk Server** | Ubuntu Server 26.04 | Dual: Host NAT & `AD-Lab` LAN | Apache, MariaDB, PHP, osTicket Platform | Interface 2: NAT (Internet)<br>Interface 3: Domain Client | Multi-Homed Routing |

---

## 🚀 Deployment Phases

### Part 1: Windows Active Directory Infrastructure

#### Phase 1: Virtual Infrastructure & Network Baseline
* Created an evaluation instance of Windows Server 2022.
* Applied standard isolation configurations to encapsulate broadcast domains.
* Configured static IPv4 settings (`192.168.10.10 /24`) and initialized loopback routing to handle localized directory resolutions.

#### Phase 2: Core Domain Services Promotion
* Installed the **Active Directory Domain Services (AD DS)** role and promoted the node to an authoritative root Domain Controller for the new forest environment: `adlab.local`.
* Activated standard scope distributions spanning `192.168.10.50` to `192.168.10.200` to allocate parameter leases dynamically to endpoints joining the LAN segment.

#### Phase 3: Client Onboarding & Verification
* Deployed a Windows 11 client image bound to the identical `AD-Lab` segment.
* Executed standard processing and validation steps for DHCP discovery.
* Initiated a formal domain enlistment task under advanced system settings to move the endpoint into the `adlab.local` directory boundary, validating its presence inside the directory tree via *Active Directory Users and Computers*.

#### Phase 4: Identity Automation & Group Policy Enforcement
* Formulated an HR-style comma-separated file (`users.csv`) profiling new hire details (First Name, Last Name, Department, Job Title).
* Processed mass object instantiation automatically within Active Directory via custom automation loops.
* Engineered a strict corporate **Group Policy Object (GPO)** explicitly denying endpoint permissions to launch the OS Control Panel or system configuration settings.

---

### Part 2: Linux App Deployment & Integration (osTicket)

#### Phase 1: LAMP Environment Standup
* Provisioned an Ubuntu Linux server framework equipped with dual interfaces, allowing simultaneous web downloads over external NAT and database discovery over internal LAN links.
* Initialized standard background configurations and access tuning via OpenSSH.
* Built out a standardized open-source web application runtime layer: **Apache2** (Web Server), **MariaDB** (Relational SQL Database), and **PHP Core Engines**.

#### Phase 2: Relational Schema Handshakes
* Provisioned dedicated storage boundaries and mapped administrative profiles to enforce query protections via backend database monitors:
```sql
CREATE DATABASE osticket_db;
CREATE USER 'osticket_user'@'localhost' IDENTIFIED BY 'LabPassword123!';
GRANT ALL PRIVILEGES ON osticket_db.* TO 'osticket_user'@'localhost';
FLUSH PRIVILEGES;
EXIT;
