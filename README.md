# SOC Home Lab Project

## 📌 Overview
This project is a **Security Operations Center (SOC) Home Lab** designed to simulate real-world cybersecurity operations.  
It integrates **SIEM, Threat Emulation, and Incident Response platforms** to detect, analyze, and respond to security events in a controlled environment.  

The lab helps me (and others) to practice **threat detection, incident response, and SOC workflows** with open-source tools.

---

## 🛠️ Components
- **SIEM & Monitoring**  
  - [Wazuh](https://wazuh.com/) – Open-source SIEM (includes Wazuh Indexer, Wazuh Server, and Wazuh Dashboard)  

- **Incident Response**  
  - [TheHive](https://thehive-project.org/) – Incident Response Platform (IRP)  

- **Threat Emulation**  
  - [Atomic Red Team](https://github.com/redcanaryco/atomic-red-team) – Adversary simulation framework (ATT&CK-based testing)  

- **Lab Environment**  
  - **Ubuntu Server** (Wazuh + TheHive)  
  - **MacBook (Victim Machine)** – Target for Atomic Red Team simulation  

---

## 🎯 Objectives
- Build a **realistic SOC environment** for hands-on practice  
- Detect and analyze simulated attacks with **Wazuh**  
- Conduct incident response workflows with **TheHive**  
- Improve SOC skills: **log analysis, detection engineering, IR playbooks**  
- Share knowledge with the community and future SOC analysts  

---

## 🏗️ Architecture
- **Virtualization**: VirtualBox
- **Server OS**: Ubuntu Server 24.04 LTS
- **SIEM**: Wazuh
- **IR Platform**: TheHive
- **Threat Simulation**: Atomic Red Team
- **Victim Machine**: macOS

---

## 📂 Table of Contents
1. [Install VirtualBox](#1-install-virtualbox)
2. [Download & Install Ubuntu Server](#2-download--install-ubuntu-server)
3. [Configure Ubuntu Server](#3-configure-ubuntu-server)
4. [Install Wazuh](#4-install-wazuh)
5. [Install & Configure TheHive](#5-install--configure-thehive)
6. [Set Up Atomic Red Team](#6-set-up-atomic-red-team)
7. [Connect Victim Machine (macOS)](#7-connect-victim-machine-macos)
8. [Testing & Simulating Attacks](#8-testing--simulating-attacks)
9. [Incident Response Workflow](#9-incident-response-workflow)
10. [Future Improvements](#10-future-improvements)

---

## 1. Install VirtualBox
Step-by-step guide for installing **Oracle VirtualBox** on your host machine.

1. Go to the official VirtualBox website: [https://www.virtualbox.org/](https://www.virtualbox.org/).
2. Download the version for your operating system (Windows, macOS, Linux).
3. Run the installer and follow the installation wizard.
4. Once installation is complete, launch VirtualBox from your applications menu.

![VirtualBox Website Screenshot](/images/virtualbox-website.png)


---

## 2. Download & Install Ubuntu Server
We will install **Ubuntu Server 24.04.3 LTS** inside VirtualBox.  
Ubuntu Server will act as the main system for our SOC lab (Wazuh, Elastic Stack, TheHive, etc.).


### 2.1 Download Ubuntu Server ISO
1. Go to the official Ubuntu Server download page: [Ubuntu Server Downloads](https://ubuntu.com/download/server).  
2. Download the **24.04.3 LTS ISO** for your system.

![Ubuntu Server Download](/images/ubuntu-server-website.png)


### 2.2 Create a New Virtual Machine
1. Open **VirtualBox** → Click **New**.  
2. Enter VM details:  
   - Name: `Ubuntu-SOC`  
   - Type: `Linux`  
   - Version: `Ubuntu (64-bit)` 
3. Choose the downloaded Ubuntu Server ISO.   
4. Allocate **RAM**: 4GB minimum (8GB recommended)  
5. Create a **Virtual Hard Disk**: 40GB minimum, VDI, dynamically allocated  


![Create VM](/images/virtualbox-create-vm.png)


### 2.3 Configure Network
1. Go to **Settings → Network**.  
2. Configure adapters:  
   - **Adapter 1** → `Bridged Adapter` (gets an IP from your local network)  
   - **Adapter 2** → `Host-Only Adapter` (optional, for internal lab communication)  
3. Boot the VM and check IP:  
```bash
ip a
```


---

## 3. Configure Ubuntu Server
After successfully installing Ubuntu Server on VirtualBox, follow these steps to configure your server.


### 3.1 Update and Upgrade the System

Make sure your system is up to date:

```bash
sudo apt update && sudo apt upgrade -y
```

### 3.2 Check Network Connectivity

Verify that your server is connected to the internet by pinging an external host

```bash
ping -c 4 google.com
```

### 3.3 Set Hostname

Set a custom hostname for your server


```bash
sudo hostnamectl set-hostname ubuntu-soc
```

Verify the change

```bash
hostnamectl
```

### 3.4 Enable OpenSSH Server

Install and enable the OpenSSH server to allow remote connections

```bash
sudo apt install openssh-server -y
sudo systemctl enable ssh
sudo systemctl start ssh
```

Check status

```bash
systemctl status ssh
```


---

## 4. Install Wazuh (Indexer + Server + Dashboard)

We install Wazuh using the **assisted installation script**, which sets up all three core components:

. **Wazuh Indexer →** Stores security events
. **Wazuh Server →** Processes and correlates logs from agents
. **Wazuh Dashboard →** Web interface for visualization and management

### 4.1 Download the installer

```bash
curl -sO https://packages.wazuh.com/4.12/wazuh-install.sh
chmod +x wazuh-install.sh
```

### 4.2 Run the installer

To install all components in one server:

```bash
sudo bash wazuh-install.sh --generate-config-files
sudo bash wazuh-install.sh --install-all
```

### 4.3 Extract credentials

The installer generates a file with all passwords:

```bash
tar -O -xvf wazuh-install-files.tar wazuh-install-files/wazuh-passwords.txt
```

Example admin credentials (from my setup):

```bash
Username: admin
Password: u8Dl3+rXpQalQnarV288Jl5c6ZktC.?z
```

### 4.4 Access the dashboard

. LAN: ```https://<server-ip>/```

. Login with the admin credentials from the password file.

. Browser will warn about self-signed SSL → accept to continue.

📸 Screenshot of Wazuh dashboard running here

![Wazuh Dashboard Screenshot](/images/wazuh-dashboard.png)

---

## 5. Install & Configure TheHive
Installation and integration with Wazuh/Elastic.

---

## 6. Set Up Atomic Red Team
Running threat simulations to test detections.

---

## 7. Connect Victim Machine (macOS)
Configuration for the MacBook as a victim machine.

---

## 8. Testing & Simulating Attacks
Perform simulations and validate detections.

---

## 9. Incident Response Workflow
Using TheHive to respond to alerts from Wazuh.

---

## 10. Future Improvements
Ideas for scaling, adding tools (e.g., MISP, Cortex, Velociraptor).
