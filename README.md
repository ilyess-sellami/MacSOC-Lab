# SOC Home Lab Project

## 📌 Overview
This project is a **Security Operations Center (SOC) Home Lab** designed to simulate real-world cybersecurity operations.  
It integrates **SIEM, Agents, and Threat Emulation tools** to detect and analyze security events in a controlled environment.  

The lab helps me (and others) to practice **threat detection, log analysis, and SOC workflows** using open-source tools.

---

## 🛠️ Components
- **SIEM & Monitoring**  
  - [Wazuh](https://wazuh.com/) – Open-source SIEM (includes Wazuh Indexer, Wazuh Server, and Wazuh Dashboard)  

- **Endpoint Agent**  
  - [Wazuh Agent](https://documentation.wazuh.com/current/installation-guide/wazuh-agent/index.html) – Installed on endpoints (e.g., macOS, Windows, Linux) to collect logs and forward them to the Wazuh server  

- **Threat Emulation**  
  - [Atomic Red Team](https://github.com/redcanaryco/atomic-red-team) – Adversary simulation framework (ATT&CK-based testing)  

- **Lab Environment**  
  - **Ubuntu Server** (Wazuh + TheHive)  
  - **MacBook (Victim Machine)** – Target for Atomic Red Team simulation  

---

## 🎯 Objectives
- Build a **realistic SOC environment** for hands-on practice  
- Detect and analyze simulated attacks with **Wazuh**  
- Improve SOC skills: **log analysis, detection engineering, IR playbooks**  
- Share knowledge with the community and future SOC analysts  

---

## 🏗️ Architecture
- **Virtualization**: VirtualBox
- **Server OS**: Ubuntu Server 24.04 LTS
- **SIEM**: Wazuh
- **Agents**: Wazuh agents installed on victim machines to forward logs/events
- **Threat Simulation**: Atomic Red Team
- **Victim Machine**: macOS

---

## 📂 Table of Contents
1. [Install VirtualBox](#1-install-virtualbox)
2. [Download & Install Ubuntu Server](#2-download--install-ubuntu-server)
3. [Configure Ubuntu Server](#3-configure-ubuntu-server)
4. [Install Wazuh (Indexer + Server + Dashboard)](#4-install-wazuh)
5. [Connect Victim Machine (macOS)](#7-connect-victim-machine-macos)
6. [Set Up Atomic Red Team](#6-set-up-atomic-red-team)
7. [Testing & Simulating Attacks](#8-testing--simulating-attacks)
8. [Future Improvements](#10-future-improvements)

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

## 5. Connect Victim Machine (macOS)

In this step, we will connect the macOS victim machine to the Wazuh server. The process includes registering the agent in the dashboard, installing and configuring the agent on the Mac, and verifying that logs are successfully sent to the SIEM.

### 5.1 Add Agent in Wazuh Dashboard
1. Log in to the Wazuh Dashboard.
2. Navigate to **Agents → Add Agent**.
3. Select: **OS**: ```macOS```, **Name**: ```macbook-victim```, **Server Address**: your wazuh ```<server-ip>```
4. Copy the generated installation command provided by Wazuh Dashboard.

![Wazuh Add Agent](/images/wazuh-add-agent.png)


### 5.2 Install and Configure Wazuh Agent on macOS
On the victim machine (macOS), run the following command in the terminal:

```bash
curl -so wazuh-agent.pkg https://packages.wazuh.com/4.x/macos/wazuh-agent-4.12.0-1.intel64.pkg \
&& echo "WAZUH_MANAGER='<server-ip>' && WAZUH_AGENT_NAME='macbook-victim'" > /tmp/wazuh_envs \
&& sudo installer -pkg ./wazuh-agent.pkg -target /
```

Start the Wazuh agent service

```bash
sudo /Library/Ossec/bin/wazuh-control start
```

### 5.3 Verify Agent Registration and Logs

1. Go back to ***Wazuh Dashboard → Agents***.
(You should see the new agent ```macbook-victim``` listed and connected.)

![Wazuh succesfully Agent Add](/images/wazuh-succesfully-agent-add.png)

2. Check logs in Wazuh Dashboard → Discover → Logs.
(Confirm that events from the macOS victim machine are being ingested.)

![Wazuh Macbook Logs](/images/wazuh-macbook-logs.png)


---

## 6. Set Up Atomic Red Team
Atomic Red Team is an ***open-source framework*** for simulating adversary techniques based on the MITRE ATT&CK framework. In this step, we will install it on the ***victim machine (macOS)*** and run simple simulations to generate logs that Wazuh can detect.

### 6.1 Install Atomic Red Team

1. Install prerequisites (Python & Homebrew) if not already installed:

```bash
# Install Homebrew (if not installed)
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install Python3
brew install python
```


---

## 7. Testing & Simulating Attacks
Perform simulations and validate detections.


---

## 8. Future Improvements
Ideas for scaling, adding tools (e.g., MISP, Cortex, Velociraptor).
