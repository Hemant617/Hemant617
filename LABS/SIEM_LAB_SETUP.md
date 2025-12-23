# 🔍 Complete SIEM Home Lab Setup Guide

## 📋 Table of Contents
1. [What is a SIEM Lab?](#what-is-a-siem-lab)
2. [Hardware Requirements](#hardware-requirements)
3. [SIEM Options Comparison](#siem-options-comparison)
4. [Lab Architecture](#lab-architecture)
5. [Option 1: Elastic Stack (ELK)](#option-1-elastic-stack-elk)
6. [Option 2: Splunk](#option-2-splunk)
7. [Option 3: Wazuh](#option-3-wazuh)
8. [Option 4: Security Onion](#option-4-security-onion)
9. [Log Sources Configuration](#log-sources-configuration)
10. [Detection Rules & Alerts](#detection-rules--alerts)
11. [Practice Scenarios](#practice-scenarios)

---

## 🎯 What is a SIEM Lab?

A SIEM (Security Information and Event Management) lab allows you to:
- **Collect logs** from multiple sources
- **Analyze security events** in real-time
- **Create detection rules** for threats
- **Practice incident response** workflows
- **Build dashboards** for monitoring
- **Develop correlation rules** for advanced threats

---

## 💻 Hardware Requirements

### Minimum Specs
- **CPU**: Intel i5/AMD Ryzen 5 (6 cores)
- **RAM**: 24GB (SIEM is memory-intensive!)
- **Storage**: 500GB SSD
- **Network**: Gigabit Ethernet

### Recommended Specs
- **CPU**: Intel i7/i9 or AMD Ryzen 7/9 (8+ cores)
- **RAM**: 32GB or more
- **Storage**: 1TB SSD (logs grow quickly!)
- **Network**: Gigabit Ethernet

### Resource Allocation per SIEM

| SIEM Solution | RAM | CPU | Disk |
|--------------|-----|-----|------|
| Elastic Stack | 8-16GB | 4 cores | 200GB+ |
| Splunk Free | 4-8GB | 2 cores | 100GB+ |
| Wazuh | 4-8GB | 2 cores | 100GB+ |
| Security Onion | 16GB+ | 4 cores | 200GB+ |

---

## 🔄 SIEM Options Comparison

### 1. **Elastic Stack (ELK)** - Recommended for Beginners
- **Pros**: Free, powerful, great visualization, large community
- **Cons**: Complex setup, resource-intensive
- **Best for**: Learning SIEM fundamentals, log analysis
- **Cost**: Free (Open Source)

### 2. **Splunk**
- **Pros**: Industry standard, powerful, great documentation
- **Cons**: 500MB/day limit on free version, expensive for enterprise
- **Best for**: Career preparation, enterprise-like experience
- **Cost**: Free (limited), Enterprise license expensive

### 3. **Wazuh**
- **Pros**: Free, open-source, built-in HIDS, compliance features
- **Cons**: Steeper learning curve
- **Best for**: Host-based detection, compliance monitoring
- **Cost**: Free (Open Source)

### 4. **Security Onion**
- **Pros**: All-in-one security monitoring, includes IDS/IPS
- **Cons**: Very resource-intensive, complex
- **Best for**: Advanced users, full SOC simulation
- **Cost**: Free (Open Source)

---

## 🏗️ Lab Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    Host Machine                              │
│                                                              │
│  ┌────────────────────────────────────────────────────┐    │
│  │              Hypervisor (VMware/VirtualBox)        │    │
│  │                                                     │    │
│  │  ┌──────────────┐      ┌──────────────┐          │    │
│  │  │  SIEM Server │      │  Log Sources │          │    │
│  │  │              │◄─────┤              │          │    │
│  │  │ Elasticsearch│      │ - Windows 10 │          │    │
│  │  │ Logstash     │      │ - Linux      │          │    │
│  │  │ Kibana       │      │ - Web Server │          │    │
│  │  │              │      │ - Firewall   │          │    │
│  │  │ 8-16GB RAM   │      │ - Kali Linux │          │    │
│  │  │ 4 CPU cores  │      │              │          │    │
│  │  └──────────────┘      └──────────────┘          │    │
│  │                                                     │    │
│  │  ┌──────────────┐                                 │    │
│  │  │  Attacker VM │                                 │    │
│  │  │              │                                 │    │
│  │  │  Kali Linux  │                                 │    │
│  │  │              │                                 │    │
│  │  └──────────────┘                                 │    │
│  └────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────┘
```

---

## 🔵 Option 1: Elastic Stack (ELK) Setup

### Step 1: Create Ubuntu Server VM

```bash
# VM Specifications
OS: Ubuntu Server 22.04 LTS
RAM: 8GB minimum, 16GB recommended
CPU: 4 cores
Disk: 200GB
Network: NAT or Host-Only

# Download Ubuntu Server
URL: https://ubuntu.com/download/server
```

### Step 2: Install Elasticsearch

```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install Java (required for Elasticsearch)
sudo apt install -y openjdk-11-jdk

# Add Elastic repository
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg

echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-8.x.list

# Install Elasticsearch
sudo apt update
sudo apt install -y elasticsearch

# Configure Elasticsearch
sudo nano /etc/elasticsearch/elasticsearch.yml

# Update these settings:
cluster.name: siem-lab
node.name: node-1
network.host: 0.0.0.0
http.port: 9200
discovery.type: single-node

# Start Elasticsearch
sudo systemctl daemon-reload
sudo systemctl enable elasticsearch
sudo systemctl start elasticsearch

# Verify installation
curl -X GET "localhost:9200"
```

### Step 3: Install Kibana

```bash
# Install Kibana
sudo apt install -y kibana

# Configure Kibana
sudo nano /etc/kibana/kibana.yml

# Update these settings:
server.port: 5601
server.host: "0.0.0.0"
elasticsearch.hosts: ["http://localhost:9200"]

# Start Kibana
sudo systemctl enable kibana
sudo systemctl start kibana

# Access Kibana
# Open browser: http://<VM-IP>:5601
```

### Step 4: Install Logstash

```bash
# Install Logstash
sudo apt install -y logstash

# Create basic configuration
sudo nano /etc/logstash/conf.d/beats-input.conf

# Add this configuration:
input {
  beats {
    port => 5044
  }
}

filter {
  # Add filters here later
}

output {
  elasticsearch {
    hosts => ["localhost:9200"]
    index => "%{[@metadata][beat]}-%{[@metadata][version]}-%{+YYYY.MM.dd}"
  }
}

# Start Logstash
sudo systemctl enable logstash
sudo systemctl start logstash
```

### Step 5: Install Filebeat (on log sources)

```bash
# On Windows (PowerShell as Admin)
# Download Filebeat
Invoke-WebRequest -Uri https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-8.x.x-windows-x86_64.zip -OutFile filebeat.zip

# Extract and install
Expand-Archive filebeat.zip -DestinationPath "C:\Program Files"
cd "C:\Program Files\filebeat-8.x.x-windows-x86_64"

# Configure
notepad filebeat.yml

# Update configuration:
output.elasticsearch:
  hosts: ["<SIEM-IP>:9200"]

filebeat.inputs:
- type: log
  enabled: true
  paths:
    - C:\Windows\System32\winevt\Logs\*.evtx

# Install and start service
.\install-service-filebeat.ps1
Start-Service filebeat

# On Linux
curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-8.x.x-amd64.deb
sudo dpkg -i filebeat-8.x.x-amd64.deb

# Configure
sudo nano /etc/filebeat/filebeat.yml

# Update:
output.elasticsearch:
  hosts: ["<SIEM-IP>:9200"]

# Enable system module
sudo filebeat modules enable system

# Start Filebeat
sudo systemctl enable filebeat
sudo systemctl start filebeat
```

### Step 6: Create First Dashboard

```bash
# Access Kibana: http://<SIEM-IP>:5601

# 1. Go to Management → Stack Management → Index Patterns
# 2. Create index pattern: filebeat-*
# 3. Select @timestamp as time field
# 4. Go to Discover to see logs
# 5. Create visualizations in Dashboard
```

---

## 🟠 Option 2: Splunk Setup

### Step 1: Create Splunk VM

```bash
# VM Specifications
OS: Ubuntu Server 22.04 or CentOS 8
RAM: 8GB minimum
CPU: 2-4 cores
Disk: 100GB
Network: NAT or Host-Only
```

### Step 2: Install Splunk Enterprise

```bash
# Download Splunk (requires free account)
# URL: https://www.splunk.com/en_us/download/splunk-enterprise.html

# On Ubuntu
wget -O splunk-9.x.x-linux-2.6-amd64.deb 'https://download.splunk.com/...'
sudo dpkg -i splunk-9.x.x-linux-2.6-amd64.deb

# Start Splunk
cd /opt/splunk/bin
sudo ./splunk start --accept-license

# Create admin account when prompted
# Username: admin
# Password: <your-secure-password>

# Enable boot-start
sudo ./splunk enable boot-start

# Access Splunk Web
# http://<VM-IP>:8000
```

### Step 3: Install Universal Forwarder (on log sources)

```bash
# On Windows
# Download Universal Forwarder
# URL: https://www.splunk.com/en_us/download/universal-forwarder.html

# Install with GUI
# Configure:
# - Receiving Indexer: <SIEM-IP>:9997
# - Admin credentials

# On Linux
wget -O splunkforwarder-9.x.x-linux-2.6-amd64.deb 'https://download.splunk.com/...'
sudo dpkg -i splunkforwarder-9.x.x-linux-2.6-amd64.deb

cd /opt/splunkforwarder/bin
sudo ./splunk start --accept-license

# Add forward server
sudo ./splunk add forward-server <SIEM-IP>:9997

# Add monitor for logs
sudo ./splunk add monitor /var/log
```

### Step 4: Configure Data Inputs

```bash
# In Splunk Web Interface:
# 1. Settings → Data Inputs
# 2. Configure receiving port: 9997
# 3. Add data sources from forwarders
# 4. Create indexes for different log types
```

### Step 5: Create Searches and Alerts

```bash
# Example search for failed logins
index=windows EventCode=4625

# Example search for successful logins
index=windows EventCode=4624

# Create alert:
# Settings → Searches, Reports, and Alerts → New Alert
```

---

## 🟢 Option 3: Wazuh Setup

### Step 1: Install Wazuh Server

```bash
# VM Specifications
OS: Ubuntu Server 22.04
RAM: 4-8GB
CPU: 2-4 cores
Disk: 100GB

# Install Wazuh using installation script
curl -sO https://packages.wazuh.com/4.x/wazuh-install.sh
sudo bash ./wazuh-install.sh -a

# Save the admin credentials displayed
# Access Wazuh dashboard: https://<VM-IP>
```

### Step 2: Install Wazuh Agent (on endpoints)

```bash
# On Windows (PowerShell as Admin)
Invoke-WebRequest -Uri https://packages.wazuh.com/4.x/windows/wazuh-agent-4.x.x-1.msi -OutFile wazuh-agent.msi

# Install
msiexec.exe /i wazuh-agent.msi /q WAZUH_MANAGER='<SIEM-IP>' WAZUH_AGENT_NAME='Windows10-Lab'

# Start service
NET START WazuhSvc

# On Linux
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import && chmod 644 /usr/share/keyrings/wazuh.gpg

echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | tee -a /etc/apt/sources.list.d/wazuh.list

sudo apt update
sudo apt install -y wazuh-agent

# Configure manager IP
sudo nano /var/ossec/etc/ossec.conf
# Update <address> with SIEM IP

# Start agent
sudo systemctl daemon-reload
sudo systemctl enable wazuh-agent
sudo systemctl start wazuh-agent
```

### Step 3: Configure Wazuh Rules

```bash
# Access Wazuh dashboard
# Management → Rules
# Browse existing rules or create custom ones

# Example custom rule for SSH brute force
<rule id="100001" level="10">
  <if_sid>5551</if_sid>
  <same_source_ip />
  <description>Multiple SSH authentication failures</description>
  <group>authentication_failures,</group>
</rule>
```

---

## 🔴 Option 4: Security Onion Setup

### Step 1: Download and Install

```bash
# VM Specifications (MINIMUM)
RAM: 16GB (32GB recommended)
CPU: 4 cores (8 recommended)
Disk: 200GB
Network: 2 adapters (Management + Monitor)

# Download Security Onion
URL: https://github.com/Security-Onion-Solutions/securityonion/blob/master/VERIFY_ISO.md

# Create VM and boot from ISO
# Follow installation wizard
# Choose "Standalone" for home lab
```

### Step 2: Initial Configuration

```bash
# After installation, run setup
sudo so-setup

# Choose:
# - Evaluation Mode (for home lab)
# - Standalone installation
# - Configure network interfaces
# - Set admin credentials

# Access Security Onion Console (SOC)
# https://<VM-IP>
```

### Step 3: Configure Sensors

```bash
# Security Onion includes:
# - Suricata (IDS/IPS)
# - Zeek (Network analysis)
# - Elasticsearch (SIEM)
# - Kibana (Visualization)

# All configured automatically!
```

---

## 📊 Log Sources Configuration

### 1. Windows Event Logs

#### Enable Audit Policies
```powershell
# Run as Administrator
# Enable detailed logging
auditpol /set /category:"Logon/Logoff" /success:enable /failure:enable
auditpol /set /category:"Account Logon" /success:enable /failure:enable
auditpol /set /category:"Object Access" /success:enable /failure:enable
auditpol /set /category:"Process Tracking" /success:enable /failure:enable

# Enable PowerShell logging
Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\PowerShell\ScriptBlockLogging" -Name "EnableScriptBlockLogging" -Value 1

# Enable command line logging
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System\Audit" -Name "ProcessCreationIncludeCmdLine_Enabled" -Value 1
```

#### Key Event IDs to Monitor
```
4624 - Successful logon
4625 - Failed logon
4672 - Special privileges assigned
4688 - Process creation
4720 - User account created
4732 - User added to security group
4768 - Kerberos TGT requested
5140 - Network share accessed
7045 - Service installed
```

### 2. Linux Syslog

```bash
# Configure rsyslog to forward to SIEM
sudo nano /etc/rsyslog.conf

# Add at the end:
*.* @@<SIEM-IP>:514

# Restart rsyslog
sudo systemctl restart rsyslog

# Key logs to monitor:
# /var/log/auth.log - Authentication
# /var/log/syslog - System events
# /var/log/apache2/access.log - Web access
# /var/log/apache2/error.log - Web errors
```

### 3. Web Server Logs (Apache/Nginx)

```bash
# Apache
sudo nano /etc/apache2/apache2.conf

# Add custom log format
LogFormat "%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\"" combined

# Nginx
sudo nano /etc/nginx/nginx.conf

# Configure logging
access_log /var/log/nginx/access.log combined;
error_log /var/log/nginx/error.log warn;
```

### 4. Firewall Logs

```bash
# pfSense/OPNsense
# System → Advanced → Miscellaneous
# Enable remote syslog
# Add SIEM IP and port 514

# iptables (Linux)
sudo iptables -A INPUT -j LOG --log-prefix "IPTABLES-INPUT: "
sudo iptables -A OUTPUT -j LOG --log-prefix "IPTABLES-OUTPUT: "
```

---

## 🚨 Detection Rules & Alerts

### Elastic Stack Detection Rules

```yaml
# Create detection rule in Kibana
# Security → Rules → Create new rule

# Example: Brute Force Detection
Rule Type: Threshold
Index Pattern: filebeat-*
Query: event.code:4625
Threshold: 5 attempts
Time window: 5 minutes
Actions: Email alert

# Example: Suspicious PowerShell
Rule Type: Query
Query: event.code:4104 AND powershell.file.script_block_text:*DownloadString*
Actions: Create case
```

### Splunk Alerts

```spl
# Brute Force Detection
index=windows EventCode=4625
| stats count by src_ip
| where count > 5

# Save as Alert:
# Trigger: Real-time
# Action: Send email

# Suspicious Process Creation
index=windows EventCode=4688 
(CommandLine="*powershell*" AND CommandLine="*-enc*")
OR (CommandLine="*cmd.exe*" AND CommandLine="*/c*")

# Lateral Movement Detection
index=windows EventCode=4624 LogonType=3
| stats dc(dest) as unique_hosts by src_user
| where unique_hosts > 10
```

### Wazuh Rules

```xml
<!-- Custom rule file: /var/ossec/etc/rules/local_rules.xml -->

<!-- SSH Brute Force -->
<rule id="100001" level="10">
  <if_sid>5551</if_sid>
  <same_source_ip />
  <description>Multiple SSH authentication failures from same source</description>
  <group>authentication_failures,pci_dss_10.2.4,pci_dss_10.2.5,</group>
</rule>

<!-- Suspicious File Download -->
<rule id="100002" level="8">
  <if_sid>550</if_sid>
  <match>wget|curl</match>
  <description>Suspicious file download detected</description>
  <group>web,attack,</group>
</rule>

<!-- Privilege Escalation -->
<rule id="100003" level="12">
  <if_sid>5401</if_sid>
  <match>sudo su|sudo -i</match>
  <description>Potential privilege escalation attempt</description>
  <group>privilege_escalation,</group>
</rule>
```

---

## 🎯 Practice Scenarios

### Scenario 1: Detect Brute Force Attack

```bash
# On Kali Linux (Attacker)
hydra -l admin -P /usr/share/wordlists/rockyou.txt ssh://<target-ip>

# In SIEM, create query to detect:
# - Multiple failed login attempts (EventCode 4625)
# - From same source IP
# - Within short time window
# - Create alert rule
```

### Scenario 2: Detect Port Scan

```bash
# On Kali Linux
nmap -p- -T4 <target-ip>

# In SIEM, detect:
# - High volume of connection attempts
# - To multiple ports
# - From single source
# - Short time window
```

### Scenario 3: Detect Malware Execution

```bash
# Simulate with EICAR test file
echo 'X5O!P%@AP[4\PZX54(P^)7CC)7}$EICAR-STANDARD-ANTIVIRUS-TEST-FILE!$H+H*' > eicar.com

# In SIEM, detect:
# - Process creation (EventCode 4688)
# - Suspicious file names
# - Unusual parent-child process relationships
```

### Scenario 4: Detect Lateral Movement

```bash
# Simulate with PsExec
psexec.exe \\<target-ip> -u admin -p password cmd.exe

# In SIEM, detect:
# - Network logon (LogonType 3)
# - From unusual source
# - Service creation (EventCode 7045)
# - Named pipe creation
```

### Scenario 5: Detect Data Exfiltration

```bash
# Simulate large file transfer
scp large_file.zip user@external-server:/tmp/

# In SIEM, detect:
# - Large outbound data transfer
# - To unusual destination
# - Outside business hours
# - Unusual protocol usage
```

---

## 📈 Dashboard Creation

### Essential Dashboards

#### 1. Security Overview Dashboard
```
- Total events per hour
- Top source IPs
- Top destination IPs
- Failed login attempts
- Successful logins
- Top event types
- Geographic map of connections
```

#### 2. Authentication Dashboard
```
- Failed logins over time
- Successful logins over time
- Top users with failed logins
- Top source IPs with failed logins
- Account lockouts
- Password changes
```

#### 3. Network Activity Dashboard
```
- Top talkers (source IPs)
- Top destinations
- Protocol distribution
- Port usage
- Bandwidth usage
- Connection timeline
```

#### 4. Threat Detection Dashboard
```
- Active alerts
- Alert severity distribution
- Top attack types
- Compromised hosts
- Malware detections
- Suspicious processes
```

---

## 🔧 Maintenance & Optimization

### Log Retention

```bash
# Elastic Stack - Configure ILM (Index Lifecycle Management)
# Kibana → Stack Management → Index Lifecycle Policies

# Splunk - Configure retention
# Settings → Indexes → Edit index
# Set retention period (e.g., 30 days)

# Wazuh - Configure in ossec.conf
<localfile>
  <log_format>syslog</log_format>
  <location>/var/log/messages</location>
  <age>30d</age>
</localfile>
```

### Performance Tuning

```bash
# Elasticsearch heap size
sudo nano /etc/elasticsearch/jvm.options
# Set to 50% of available RAM (max 32GB)
-Xms8g
-Xmx8g

# Logstash workers
sudo nano /etc/logstash/logstash.yml
pipeline.workers: 4
pipeline.batch.size: 125

# Splunk
# Settings → Server settings → General settings
# Adjust search limits and timeouts
```

---

## ✅ SIEM Lab Checklist

- [ ] SIEM platform installed and accessible
- [ ] At least 3 log sources configured
- [ ] Logs flowing into SIEM (verify in dashboard)
- [ ] Index patterns/data inputs created
- [ ] First dashboard created
- [ ] At least 5 detection rules configured
- [ ] Alert notifications working
- [ ] Baseline traffic established
- [ ] Attack scenarios tested
- [ ] Incident response playbook created

---

## 📚 Additional Resources

### Learning Platforms
- **Splunk Fundamentals**: https://www.splunk.com/en_us/training.html
- **Elastic Training**: https://www.elastic.co/training/
- **Wazuh Documentation**: https://documentation.wazuh.com/
- **Security Onion**: https://docs.securityonion.net/

### Detection Rules
- **Sigma Rules**: https://github.com/SigmaHQ/sigma
- **Elastic Detection Rules**: https://github.com/elastic/detection-rules
- **Splunk Security Content**: https://github.com/splunk/security_content

### Practice Datasets
- **EVTX Attack Samples**: https://github.com/sbousseaden/EVTX-ATTACK-SAMPLES
- **Mordor Dataset**: https://github.com/OTRF/mordor
- **Boss of the SOC**: https://github.com/splunk/botsv3

---

## 🎓 Next Steps

1. **Generate baseline traffic** for 24-48 hours
2. **Create detection rules** for common attacks
3. **Practice incident response** workflows
4. **Build custom dashboards** for your environment
5. **Integrate threat intelligence** feeds
6. **Automate response actions** with SOAR
7. **Document everything** in a SOC playbook

---

**Your SIEM lab is now ready for security monitoring and threat detection! 🚀**
