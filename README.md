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
* **Management Station:** ASUS ZenBook 14 OLED (SSH client & Web Browser Control Center)

![Proxmox VM Overview](https://via.placeholder.com/800x400?text=Insert+Proxmox+VM+Dashboard+Screenshot+Here)

---

## 🛠️ Software Stack & Dependencies

The application layer relies on a optimized LAMP (Linux, Apache, MariaDB, PHP) stack built on the Ubuntu VM:

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
