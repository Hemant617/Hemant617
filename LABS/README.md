# 🏠 Home Cybersecurity Lab Setup

## 📚 Available Lab Guides

This directory contains comprehensive guides for setting up professional cybersecurity labs at home.

---

## 🔐 [Kali Linux Home Lab](./KALI_LINUX_HOME_LAB_SETUP.md)

**Purpose**: Offensive security testing and penetration testing practice

**What You'll Learn**:
- Network reconnaissance and scanning
- Vulnerability exploitation
- Web application testing
- Password cracking
- Man-in-the-middle attacks
- Post-exploitation techniques

**Time to Setup**: 4-6 hours

**Difficulty**: Beginner to Intermediate

**Key Components**:
- Kali Linux (Attacker)
- Metasploitable 2/3 (Vulnerable targets)
- DVWA (Web application testing)
- Windows 10 (Target system)

---

## 🔍 [SIEM Lab Setup](./SIEM_LAB_SETUP.md)

**Purpose**: Security monitoring, log analysis, and threat detection

**What You'll Learn**:
- Log collection and aggregation
- Security event analysis
- Detection rule creation
- Alert configuration
- Dashboard building
- Incident response workflows

**Time to Setup**: 6-8 hours

**Difficulty**: Intermediate to Advanced

**SIEM Options**:
1. **Elastic Stack (ELK)** - Recommended for beginners
2. **Splunk** - Industry standard
3. **Wazuh** - Open-source HIDS + SIEM
4. **Security Onion** - All-in-one solution

---

## 🎯 Combined Lab Architecture

For the ultimate SOC analyst training, combine both labs:

```
┌─────────────────────────────────────────────────────────────┐
│                    Host Machine                              │
│                                                              │
│  ┌────────────────────────────────────────────────────┐    │
│  │              Hypervisor Layer                       │    │
│  │                                                     │    │
│  │  ┌──────────────┐      ┌──────────────┐          │    │
│  │  │  SIEM Server │      │ Kali Linux   │          │    │
│  │  │              │      │  (Attacker)  │          │    │
│  │  │ Elastic/     │◄─────┤              │          │    │
│  │  │ Splunk/      │      │ Generate     │          │    │
│  │  │ Wazuh        │      │ Attacks      │          │    │
│  │  └──────┬───────┘      └──────────────┘          │    │
│  │         │                                          │    │
│  │         │ Collect Logs                            │    │
│  │         │                                          │    │
│  │  ┌──────▼───────┐      ┌──────────────┐          │    │
│  │  │ Metasploitable│      │  Windows 10  │          │    │
│  │  │   (Victim)    │      │   (Victim)   │          │    │
│  │  │               │      │              │          │    │
│  │  │ Send Logs ────┼──────┤ Send Logs    │          │    │
│  │  └───────────────┘      └──────────────┘          │    │
│  │                                                     │    │
│  │  ┌──────────────┐                                 │    │
│  │  │     DVWA     │                                 │    │
│  │  │  (Web App)   │                                 │    │
│  │  │              │                                 │    │
│  │  │ Send Logs ───┘                                 │    │
│  │  └──────────────┘                                 │    │
│  └────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────┘
```

---

## 🚀 Quick Start Guide

### Phase 1: Basic Setup (Week 1)
1. ✅ Install hypervisor (VMware/VirtualBox)
2. ✅ Set up Kali Linux VM
3. ✅ Set up Metasploitable 2
4. ✅ Configure network (Host-Only)
5. ✅ Test connectivity between VMs
6. ✅ Run first Nmap scan

### Phase 2: Offensive Skills (Week 2)
1. ✅ Practice reconnaissance techniques
2. ✅ Exploit Metasploitable vulnerabilities
3. ✅ Set up DVWA
4. ✅ Test web application vulnerabilities
5. ✅ Practice password cracking
6. ✅ Document all findings

### Phase 3: SIEM Setup (Week 3)
1. ✅ Choose SIEM platform (Elastic/Splunk/Wazuh)
2. ✅ Install SIEM server
3. ✅ Configure log sources
4. ✅ Verify logs are flowing
5. ✅ Create first dashboard
6. ✅ Set up basic alerts

### Phase 4: Detection & Response (Week 4)
1. ✅ Generate attacks from Kali
2. ✅ Detect attacks in SIEM
3. ✅ Create detection rules
4. ✅ Build incident response playbook
5. ✅ Practice full attack → detect → respond cycle
6. ✅ Document everything

---

## 💻 Hardware Requirements

### Minimum Setup (Basic Lab)
- **CPU**: Intel i5/AMD Ryzen 5 (4 cores)
- **RAM**: 16GB
- **Storage**: 250GB SSD
- **Cost**: Can use existing computer

**Can Run**:
- Kali Linux + 2 victim VMs
- OR Basic SIEM with 2 log sources

### Recommended Setup (Full Lab)
- **CPU**: Intel i7/AMD Ryzen 7 (6-8 cores)
- **RAM**: 32GB
- **Storage**: 500GB-1TB SSD
- **Cost**: $800-1200 for dedicated machine

**Can Run**:
- Kali Linux
- 3-4 victim VMs
- SIEM server
- All simultaneously

### Professional Setup (Advanced Lab)
- **CPU**: Intel i9/AMD Ryzen 9 (8+ cores)
- **RAM**: 64GB
- **Storage**: 1TB+ NVMe SSD
- **Network**: Dedicated lab network
- **Cost**: $1500-2500

**Can Run**:
- Full enterprise simulation
- Multiple attack scenarios
- Advanced SIEM with ML
- Network traffic analysis
- Multiple concurrent users

---

## 📊 Resource Allocation Guide

### Kali Linux Lab Only
```
Kali Linux:        4GB RAM, 2 CPU
Metasploitable:    512MB RAM, 1 CPU
DVWA:              1GB RAM, 1 CPU
Windows 10:        4GB RAM, 2 CPU
─────────────────────────────────
Total:             ~10GB RAM, 6 CPU
```

### SIEM Lab Only
```
SIEM Server:       8GB RAM, 4 CPU
Windows 10:        4GB RAM, 2 CPU
Linux Server:      2GB RAM, 1 CPU
─────────────────────────────────
Total:             14GB RAM, 7 CPU
```

### Combined Lab
```
SIEM Server:       8GB RAM, 4 CPU
Kali Linux:        4GB RAM, 2 CPU
Metasploitable:    512MB RAM, 1 CPU
DVWA:              1GB RAM, 1 CPU
Windows 10:        4GB RAM, 2 CPU
Linux Server:      2GB RAM, 1 CPU
─────────────────────────────────
Total:             ~20GB RAM, 11 CPU
```

---

## 🎓 Learning Path

### Beginner (0-3 months)
1. **Start with Kali Lab**
   - Learn basic Linux commands
   - Practice Nmap scanning
   - Exploit Metasploitable
   - Test DVWA vulnerabilities

2. **Basic SIEM**
   - Set up Elastic Stack
   - Configure Windows log forwarding
   - Create simple dashboards
   - Set up basic alerts

### Intermediate (3-6 months)
1. **Advanced Exploitation**
   - Windows exploitation
   - Privilege escalation
   - Lateral movement
   - Post-exploitation

2. **Advanced SIEM**
   - Complex detection rules
   - Correlation rules
   - Custom parsers
   - Threat hunting queries

### Advanced (6-12 months)
1. **Red Team Operations**
   - Active Directory attacks
   - Evasion techniques
   - Custom malware testing
   - Full attack chains

2. **Blue Team Operations**
   - Advanced threat detection
   - Behavioral analytics
   - Threat intelligence integration
   - Automated response (SOAR)

---

## 📝 Documentation Templates

### Lab Notebook Structure
```
1. Date & Time
2. Objective
3. Tools Used
4. Steps Performed
5. Results/Findings
6. Screenshots
7. Lessons Learned
8. Next Steps
```

### Incident Report Template
```
1. Incident Summary
2. Timeline
3. Indicators of Compromise (IoCs)
4. Attack Vector
5. Impact Assessment
6. Containment Actions
7. Remediation Steps
8. Recommendations
```

---

## 🔒 Safety Guidelines

### ⚠️ CRITICAL RULES

1. **Network Isolation**
   - ✅ Use Host-Only network for lab
   - ❌ NEVER connect lab VMs directly to internet
   - ✅ Use NAT only for updates, then disconnect

2. **Malware Testing**
   - ✅ Only test in isolated environment
   - ❌ NEVER test real malware on production systems
   - ✅ Use snapshots before malware testing
   - ✅ Revert immediately after testing

3. **Legal Considerations**
   - ✅ Only attack YOUR OWN lab systems
   - ❌ NEVER use these skills on systems you don't own
   - ✅ Understand computer fraud laws in your country
   - ✅ Get written permission for any external testing

4. **Data Protection**
   - ✅ Never use real credentials in lab
   - ✅ Never store sensitive data in lab VMs
   - ✅ Encrypt lab VM storage
   - ✅ Regular backups of lab configurations

---

## 🛠️ Essential Tools Checklist

### Reconnaissance
- [ ] Nmap
- [ ] Masscan
- [ ] Netdiscover
- [ ] Wireshark
- [ ] tcpdump

### Exploitation
- [ ] Metasploit Framework
- [ ] SQLmap
- [ ] Burp Suite
- [ ] OWASP ZAP
- [ ] Hydra

### Post-Exploitation
- [ ] Mimikatz
- [ ] PowerSploit
- [ ] Empire
- [ ] BloodHound
- [ ] CrackMapExec

### Defense
- [ ] SIEM (Elastic/Splunk/Wazuh)
- [ ] IDS/IPS (Suricata/Snort)
- [ ] EDR simulation
- [ ] Log analysis tools
- [ ] Forensics tools

---

## 📚 Additional Resources

### Free Training
- **TryHackMe**: https://tryhackme.com/
- **HackTheBox**: https://www.hackthebox.com/
- **SANS Cyber Aces**: https://www.cyberaces.org/
- **Cybrary**: https://www.cybrary.it/

### Vulnerable VMs
- **VulnHub**: https://www.vulnhub.com/
- **PentesterLab**: https://pentesterlab.com/
- **OverTheWire**: https://overthewire.org/

### Documentation
- **Kali Docs**: https://www.kali.org/docs/
- **Elastic Docs**: https://www.elastic.co/guide/
- **Splunk Docs**: https://docs.splunk.com/
- **MITRE ATT&CK**: https://attack.mitre.org/

### Communities
- **Reddit**: r/cybersecurity, r/netsec, r/AskNetsec
- **Discord**: Many cybersecurity servers
- **Twitter**: #InfoSec, #CyberSecurity
- **LinkedIn**: Cybersecurity groups

---

## 🎯 Project Ideas

### Beginner Projects
1. **Network Scanner Tool** - Build custom Nmap wrapper
2. **Log Parser** - Parse and analyze Windows Event Logs
3. **Password Strength Checker** - Analyze password complexity
4. **Port Knock Script** - Implement port knocking
5. **Simple IDS** - Detect suspicious network traffic

### Intermediate Projects
1. **Custom Exploit** - Write exploit for known vulnerability
2. **SIEM Dashboard** - Build comprehensive security dashboard
3. **Threat Intelligence Feed** - Integrate threat feeds into SIEM
4. **Automated Scanner** - Build vulnerability scanner
5. **Incident Response Tool** - Automate IR workflows

### Advanced Projects
1. **Red Team Framework** - Build custom C2 framework
2. **ML-based Detection** - Use ML for anomaly detection
3. **SOAR Platform** - Build security orchestration tool
4. **Custom EDR** - Build endpoint detection tool
5. **Threat Hunting Platform** - Build threat hunting tool

---

## ✅ Lab Completion Checklist

### Kali Linux Lab
- [ ] All VMs installed and running
- [ ] Network configured and tested
- [ ] Successfully exploited Metasploitable
- [ ] Completed DVWA challenges
- [ ] Documented 10+ vulnerabilities
- [ ] Created lab notebook

### SIEM Lab
- [ ] SIEM installed and accessible
- [ ] 3+ log sources configured
- [ ] Logs flowing into SIEM
- [ ] 5+ detection rules created
- [ ] Dashboards built
- [ ] Alerts tested and working

### Combined Lab
- [ ] Both labs fully functional
- [ ] Attack → Detect → Respond cycle tested
- [ ] Incident response playbook created
- [ ] 10+ attack scenarios documented
- [ ] Detection rules for all attacks
- [ ] Full lab documentation complete

---

## 🚀 Next Steps

1. **Choose your starting point**:
   - New to security? Start with Kali Lab
   - Have offensive skills? Add SIEM Lab
   - Want full SOC experience? Build combined lab

2. **Follow the guides**:
   - [Kali Linux Home Lab Setup](./KALI_LINUX_HOME_LAB_SETUP.md)
   - [SIEM Lab Setup](./SIEM_LAB_SETUP.md)

3. **Practice daily**:
   - Spend 1-2 hours in lab daily
   - Document everything
   - Share findings on LinkedIn/GitHub

4. **Build portfolio**:
   - Create writeups for exploits
   - Share detection rules
   - Build custom tools
   - Contribute to open-source

---

**Ready to build your cybersecurity lab? Pick a guide and start today! 🔐**
