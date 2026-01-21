### Project: Virtualized AD-DS Laboratory Environment
## Version: 1.0

#### Focus: 
Windows Server 2022, Network Infrastructure, and Client Integration

## Summary
This project outlines the design and deployment of a virtualized Active Directory Domain Services (AD-DS) environment. The architecture utilizes a Windows Server 2022 Domain Controller (DC) configured as a multihomed gateway. It provides DNS, DHCP, and NAT Routing services, bridging an external VMware NAT interface to an isolated internal LAN segment for secure client connectivity.
screenshots


### VMware Settings
| Component | Description |
|------------|-------------|
| **Hypervisor** | VMware Workstation |
| **Operating Systems** | Windows Server 2022, Windows 11 |
| **Virtual Machines** | 2 (Domain Controller & Client) |
| **VM Resources** | 2 CPUs, 4 GB RAM, 30 GB HDD each |

---

<img width="1923" height="1196" alt="VM Settingss" src="https://github.com/user-attachments/assets/65b89cc3-10af-4864-bcae-e4042e2f275e" />


**Network Topology**
| VM | Adapter 1 | Adapter 2 | Configuration|
|----|------------|-----------|----------|
| **Domain Controller (DC)** | NAT(Routing) | LAN Segment| Static IP|
| **Windows 11 Client** | -- | LAN Segment| InternetAccess & DHCP via DC |




**Network Interface Settings**
<img width="2439" height="1323" alt="Netwaork Settings" src="https://github.com/user-attachments/assets/80ce2dfb-a27f-4483-8b74-4227b54c7a0f" />


---
CREATE FOLDERS STRUCTURE.


mkdir -p AD-Lab/screenshots
mkdir -p AD-Lab/scripts
cd AD-Lab
touch README.md
touch screenshots/.gitkeep
touch scripts/.gitkeep

##Roles and Server Installations##
Access the Server Manager Dashboard to begin the service provisioning.

1.Navigate to Manage > Add Roles and Features.

2.Select Role-based or feature-based installation.

3.Target Server: DC_NY.

4.Select the following roles:
  *Active Directory Domain Services (AD-DS)
  *DNS Server
  *DHCP Server
  *Remote Access (Required for NAT/Routing)



**Confirm Selections and Install**

.<img width="3150" height="1758" alt="Ready to Install Roles and features" src="https://github.com/user-attachments/assets/a49dc094-d992-4a19-af78-62d4e18b9653" />


**Select "Promote this Server to Domain Controller"**
---<img width="3072" height="1740" alt="Promote Server to DC" src="https://github.com/user-attachments/assets/6ce765c8-c84c-4fc7-b464-46171e09ac76" />


### 

**Select "Add New Forest and give a name including domain extension eg. ".com" or ".local"**

<img width="2871" height="1731" alt="Naming my Domain" src="https://github.com/user-attachments/assets/462adc4e-2cd1-4da6-9ea7-38c14123bd11" />



**Promotion Successful**
<img width="3249" height="1983" alt="After Restart" src="https://github.com/user-attachments/assets/f12e3460-ca59-4a9d-a95c-18c50a0d122e" />




**ADUAC**

<img width="2970" height="1842" alt="Users and Computers" src="https://github.com/user-attachments/assets/ef43a142-1ed8-4e0d-814a-e8e4c3ff492f" />





### Configuring a DHCP Scope
The DHCP service automates the assignment of IP addresses, subnet masks, and other network parameters to client machines. To initialize this service, an administrator must define a Scope—a consecutive range of possible IP addresses for a network.

Initialization: Within the Server Manager, navigate to Tools > DHCP. In the management console, right-click IPv4 and initiate the New Scope Wizard.

Identification: Assign a logical Name and Description to the scope to ensure administrative clarity within the directory.

Address Range and Subnetting: Define the pool of available IP addresses by entering the Start and End IP addresses. The CIDR (Classless Inter-Domain Routing) notation and Subnet Mask must be configured here to define the boundaries of the local broadcast domain.

Lease Duration: By default, the system sets a Lease Duration (the lifespan of an assigned IP address before it must be renewed) of 8 days.

Scope Options (Gateway and DNS): * Default Gateway: Enter the IP address of the Domain Controller (DC). In this specific architecture, the DC serves as the gateway due to its NAT routing configuration.

DNS Server: Specify the DC’s IP address. This is a critical step; without pointing to the local DNS, client machines will fail to authenticate or resolve the domain name.

Activation: Skip the WINS Server configuration, confirm the activation of the scope, and finalize the wizard.

### Establishing a NAT Gateway
Network Address Translation (NAT) allows multiple devices on a private local network to access the internet using a single public IP address. This is managed through the Routing and Remote Access (RRAS) role.

Accessing RRAS: From the Server Manager, select Tools > Routing and Remote Access.

Server Configuration: Right-click the server node and select Configure and Enable Routing and Remote Access.

Role Selection: Choose Network Address Translation (NAT) from the list of deployment options.

Interface Mapping: Select the External/Public Network Interface Card (NIC). This is the interface that connects to the internet service provider or the external network.

Completion: Finalize the setup to enable the routing of traffic from the internal private network to the external public network.                                            







### 
📸 *Screenshot Placeholder:*  
`![VM Settings](./screenshots/vm-settings.png)`

---

### 3️⃣ Install Operating Systems
- Mount the downloaded ISOs in each VM.
- Choose **Custom installation** for partition setup.
- For Windows Server:
  - Select **Desktop Experience (GUI)**.
  - Set an **Administrator password**.

📸 *Screenshot Placeholder:*  
`![Windows Server Installation](./screenshots/server-installation.png)`

---

### 4️⃣ Configure Networking
**Domain Controller (DC):**
- Adapter 1 → NAT  
- Adapter 2 → Host-Only  

**Windows 11 Client:**
- Adapter 1 → Host-Only  

📸 *Screenshot Placeholder:*  
`![Network Configuration](./screenshots/network-config.png)`

---

## 🧩 Active Directory Setup

### 5️⃣ Install and Configure AD DS (PowerShell)
On the **DC VM**, open PowerShell as Administrator:

```powershell
# Install ADDS Role
Install-WindowsFeature AD-Domain-Services -IncludeManagementTools

# Configure new forest
Install-ADDSForest -DomainName "homelab.local"

Reboot after promotion and log in as the new Domain Admin.

📸 Screenshot Placeholder:
![AD DS Installation](./screenshots/adds-install.png)

#Add Users via PowerShell

Example command to create a test user:

New-ADUser -Name "John Doe" `
-AccountPassword (ConvertTo-SecureString "P@ssword123" -AsPlainText -Force) `
-Enabled $true

📸 Screenshot Placeholder:
![Add AD User](./screenshots/add-user.png)

💻 Join Windows 11 Client to Domain

Change the client’s DNS to the DC’s Host-Only IP (e.g. 192.168.56.10).

Go to:

Settings → System → About → Rename this PC (Advanced)


Enter the domain name:

homelab.local


Provide domain credentials to join.

📸 Screenshot Placeholder:
![Join Domain](./screenshots/join-domain.png)

After restart, log in using the domain account.

cmd:whoami


## 📂 Folder Structure
```
ADDS-LAB/
├── README.md
├── /screenshots
│   └── .gitkeep
├── /scripts
│   └── .gitkeep
├── /notes
│   └── .gitkeep
└── /docs
    └── .gitkeep
```


# 🧱 Active Directory Lab in VMware

![VMware](https://img.shields.io/badge/VMware-0071C5?style=for-the-badge&logo=vmware&logoColor=white)
![Windows Server 2022](https://img.shields.io/badge/Windows%20Server%202022-0078D6?style=for-the-badge&logo=windows&logoColor=white)
![Windows 11](https://img.shields.io/badge/Windows%2011-0078D6?style=for-the-badge&logo=windows11&logoColor=white)
![PowerShell](https://img.shields.io/badge/PowerShell-5391FE?style=for-the-badge&logo=powershell&logoColor=white)
[Status](https://img.shields.io/badge/Status-Completed-brightgreen?style=for-the-badge)
[License](https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge)

---


Title Idea: Building a Secure Active Directory Lab: A Step-by-Step Virtualization Guide
Subtitle: Master Windows Server 2022, NAT Routing, and Client Integration using VMware.

The Vision
In the world of System Administration, understanding the backbone of enterprise identity management—Active Directory Domain Services (AD-DS)—is essential. I decided to build a fully functional, isolated laboratory environment to simulate a real-world corporate network.

My goal was simple: Create a secure, multihomed environment where a Windows Server 2022 Domain Controller acts as the gateway for internal clients, providing DNS, DHCP, and NAT Routing.
The Blueprint: Network Topology
Before touching any ISO files, I mapped out the architecture. The lab relies on a dual-homed Domain Controller that bridges the gap between the internet and a private LAN segment.

Component,Role,Network Configuration
Domain Controller,Gateway / DNS / DHCP,Adapter 1 (NAT) & Adapter 2 (LAN Segment)
Windows 11 Client,End-User Workstation,LAN Segment (Isolated)



### Folder Descriptions
- **/screenshots** → images used in documentation  
- **/scripts** → setup or automation scripts  
- **/notes** → personal notes or test results  
- **/docs** → additional documentation or guides  

---
