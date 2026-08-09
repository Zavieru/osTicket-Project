# osTicket-Project
Self Hosted IT ticketing system

# Enterprise IT Ticketing System Implementation (osTicket)

## 📌 Project Overview
This project demonstrates the end-to-end deployment, configuration, and security hardening of an enterprise-grade IT Helpdesk & Ticketing system using **osTicket**. 

A ticketing system is the operational backbone of IT and Cybersecurity teams. It serves as the primary mechanism for managing incident response workflows, handling identity & access requests, enforcing Service Level Agreements (SLAs), and maintaining immutable audit trails for compliance frameworks (such as ISO 27001 and SOC 2).

---

## 🏗️ Architecture & Lab Environment

The implementation is hosted locally on a bare-metal homelab infrastructure, split between a dedicated compute node and a management workstation:

* **Compute Node (Host Hardware):** HP ProDesk (Intel Core i5-9500, 32GB DDR4 RAM, 500GB Internal NVMe)
* **Hypervisor:** Proxmox VE (Type-1 Bare-Metal Hypervisor running on host NVMe)
* **Virtual Machine (Guest OS):** Ubuntu Server 22.04 LTS (40GB Virtual Disk, 4GB Dedicated RAM, 2 CPU Cores)
* **Management Station:** Custom built PC (SSH client & Web Browser Control Center)

[Proxmox VM Overview]<img width="1917" height="909" alt="image" src="https://github.com/user-attachments/assets/2111b31e-f9f8-4787-b0f4-5fe4451d7da5" />

---

## 🛠️ Software Stack & Dependencies

The application layer relies on an optimized LAMP (Linux, Apache, MariaDB, PHP) stack built on the Ubuntu VM:

| Service / Layer | Package / Tool Installed | Purpose |
| :--- | :--- | :--- |
| **Operating System** | Ubuntu Server 22.04 LTS | Headless server OS |
| **Networking** | Netplan | Static IP configuration & interface binding |
| **Web Server** | Apache2 (`apache2`, `libapache2-mod-php`) | Serves web traffic and processes PHP requests |
| **Database** | MariaDB Server (`mariadb-server`) | Relational database engine for osTicket tables |
| **PHP Runtime** | PHP 8.x Engine | Core scripting runtime |
| **PHP Extensions** | `php-mysql`, `php-mbstring`, `php-xml`, `php-gd`, `php-intl`, `php-iconv`, `php-fileinfo`, `php-zip`, `php-apcu` | Database bridge, graphics processing, XML parsing, and data caching |
| **Helpdesk Software**| osTicket (v1.18.1) | Core ticketing system engine |

---

## 🚀 Step-by-Step Implementation & Configuration

### Phase 1: Virtual Machine Provisioning & Network Configuration
1. Created a dedicated VM (`osTicket-Server`) in **Proxmox VE** allocating 2 CPU cores, 4096 MB RAM, and a 40GB virtual drive.
2. Completed headless Ubuntu Server OS installation with OpenSSH enabled.
3. Configured static networking via Netplan to lock down IP routing rules for web and database services.

---

### Phase 2: Web Server Stack Deployment & Database Initialization
1. Installed Apache web server, MariaDB, PHP runtime, and essential module dependencies.
2. Initialized the database server and created a dedicated relational database (`osticket`) and application user (`osticket_dbuser`):

```sql
CREATE DATABASE osticket;
CREATE USER 'osticket_dbuser'@'localhost' IDENTIFIED BY '<STRONG_PASSWORD>';
GRANT ALL PRIVILEGES ON osticket.* TO 'osticket_dbuser'@'localhost';
FLUSH PRIVILEGES;
```

---

### Phase 3: osTicket Deployment & Web Installation
1. Retrieved osTicket v1.18.1 source code and extracted files to `/var/www/html/osticket`.
2. Created the core runtime configuration file `ost-config.php` and temporarily assigned write permissions (`chmod 0666`).
3. Navigated to the graphical setup installer at `http://192.168.1.188/osticket/upload/setup/` and connected the front-end installer to the backend MariaDB database (`osticket_dbuser`).

[osTicket Web Installation] <img width="957" height="465" alt="image" src="https://github.com/user-attachments/assets/8cc71772-667d-4660-94ce-ee5054dff0d0" />


---

### Phase 4: Post-Install Hardening & Security Cleanup
To prevent configuration tampering and eliminate security vulnerabilities:
1. Locked down file permissions on the core configuration file:
   ```bash
   sudo chmod 0644 /var/www/html/osticket/upload/include/ost-config.php
   ```
2. Removed the installer setup directory to prevent unauthorized system re-initialization:
   ```bash
   sudo rm -rf /var/www/html/osticket/upload/setup
   ```

---

## ⚙️ Enterprise Configuration & Operational Controls

To transform the raw osTicket setup into a realistic corporate IT / SOC environment, the following structural controls were configured:

### 1. Role-Based Access Control (RBAC)
* Configured administrative permission sets enforcing the **Principle of Least Privilege (PoLP)**.
* Created the **`Supreme Admin / SOC Lead`** role with full administrative and ticket modification rights.

[RBAC Roles]<img width="957" height="498" alt="Admin Role Added Screenshot" src="https://github.com/user-attachments/assets/70798555-6509-4282-a075-82b48926ad6a" />


---

### 2. Enterprise Department Routing
Created dedicated operational queues to partition ticket visibility between standard IT support and security personnel:
* **SysAdmin (Public Queue):** Handles infrastructure, user management, and general software/hardware requests. Bound to **Standard Support SLA**.
* **SOC / Security Incident Response (Private Queue):** Restricted internal queue for security breaches, phishing triage, and malware investigations. Bound to **Emergency SOC Priority SLA**.

[Enterprise Departments] <img width="958" height="524" alt="New Derpartments" src="https://github.com/user-attachments/assets/f155c618-b53b-476c-80d6-50c2023c7df0" />


---

### 3. Service Level Agreement (SLA) Enforcements
Enforced strict resolution timeframes to govern ticket response metrics:
* **Emergency (SOC Priority):** 1-Hour grace period (24/7 schedule) for critical security incidents.
* **High Priority:** 4-Hour grace period for major system outages.
* **Standard Support:** 24-Hour grace period for routine maintenance.

[SLA Plans] <img width="953" height="455" alt="New SLA plans" src="https://github.com/user-attachments/assets/84dff531-43be-4be9-8d93-d3ac1f2526bc" />


---

## 🔑 Access Portals & Operational Verification

* **Staff & Admin Control Panel:** `http://192.168.1.188/osticket/upload/scp/`
* **End-User Public Portal:** `http://192.168.1.188/osticket/upload/`

---

## 📝 Next Steps (In Progress)
- [ ] **Help Topics Setup:** Configure category-based automatic routing rules.
- [ ] **Agent & User Provisioning:** Create Tier-1 analyst accounts to test multi-user identity workflows.
- [ ] **Live Incident Simulation:** Execute and document end-to-end ticket lifecycle handling (Submission $\rightarrow$ Triage $\rightarrow$ Escalation $\rightarrow$ Resolution).
