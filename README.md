# 🛡️ Enterprise SOC Home Lab Using Wazuh SIEM

## 📌 Project Overview

This project documents the design and deployment of a Security Operations Center (SOC) Home Lab using Wazuh SIEM, Ubuntu Server, Kali Linux, and VirtualBox.

The objective was to gain hands-on experience in:

* SIEM Deployment
* Threat Hunting
* Detection Engineering
* Log Analysis
* Security Monitoring
* Linux Administration
* Incident Investigation

The final environment provides centralized log collection, custom alerting, threat hunting, and endpoint monitoring capabilities.

---

# 🏗️ Lab Architecture

```text
                Ubuntu Server
             (Wazuh Manager)
               192.168.56.10
                     |
      --------------------------------
      |                              |
      |                              |
 Kali Linux                    Metasploitable
(Agent/Attacker)             (Target System)
192.168.56.20                192.168.56.30
```

---

# 🖥️ Technologies Used

| Technology     | Purpose                   |
| -------------- | ------------------------- |
| Wazuh 4.12     | SIEM Platform             |
| Ubuntu Server  | Wazuh Manager             |
| Kali Linux     | Attack Simulation & Agent |
| VirtualBox     | Virtualization            |
| Linux          | System Administration     |
| Threat Hunting | Security Monitoring       |

---

# 🎯 Project Objectives

* Deploy Wazuh SIEM
* Configure SOC Lab Network
* Connect Endpoints
* Perform Threat Hunting
* Create Custom Detection Rules
* Investigate Alerts
* Monitor Authentication Activity
* Implement File Integrity Monitoring

---

# 🌐 Network Configuration

Each VM was configured with:

### Adapter 1

```text
NAT
```

Purpose:

* Internet Connectivity

### Adapter 2

```text
Internal Network
```

Configuration:

```text
Network Name: SOC-LAB
Promiscuous Mode: Allow All
Virtual Cable Connected
```

Purpose:

* Communication between SOC systems

---

# 🔧 Static IP Configuration

### Ubuntu Server

```bash
sudo ip addr add 192.168.56.10/24 dev enp0s8
sudo ip link set enp0s8 up
```

### Kali Linux

```bash
sudo ip addr add 192.168.56.20/24 dev eth1
sudo ip link set eth1 up
```

### Metasploitable

```bash
ifconfig eth1 192.168.56.30 netmask 255.255.255.0 up
```

---

# 📡 Connectivity Verification

Commands Used:

```bash
ping 192.168.56.10
ping 192.168.56.20
ping 192.168.56.30
```

Result:

✅ Successful communication between all systems.

---

# ⚙️ Wazuh Installation

### Download Installer

```bash
curl -sO https://packages.wazuh.com/4.12/wazuh-install.sh
```

### Make Executable

```bash
chmod +x wazuh-install.sh
```

### Install Wazuh

```bash
sudo ./wazuh-install.sh -a
```

Installed Components:

* Wazuh Manager
* Wazuh Dashboard
* Wazuh Indexer

---

# 🚨 Challenges Faced

## Memory Requirement Error

Issue:

```text
System does not meet minimum hardware requirements
```

Solution:

* Increased Ubuntu RAM allocation

---

## Dashboard Accessibility Issue

Issue:

Unable to access Wazuh Dashboard.

Root Cause:

* Internal network configuration

Solution:

* Accessed dashboard from Kali browser

---

## Disk Utilization Issue

Error:

```text
Flood-stage watermark exceeded
```

Investigation:

```bash
df -h
lsblk
```

Root Cause:

Ubuntu LVM only used approximately half of the virtual disk.

Solution:

```bash
sudo lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv

sudo resize2fs /dev/ubuntu-vg/ubuntu-lv
```

Result:

✅ Filesystem expanded successfully.

---

# 🛰️ Agent Deployment

### Agent Installation

```bash
sudo WAZUH_MANAGER='192.168.56.10' \
WAZUH_AGENT_NAME='kali-attacker' \
dpkg -i wazuh-agent.deb
```

### Start Agent

```bash
sudo systemctl daemon-reload

sudo systemctl enable wazuh-agent

sudo systemctl start wazuh-agent
```

Verification:

```bash
sudo systemctl status wazuh-agent
```

Result:

✅ Agent Active

---

# 🔍 Threat Hunting

Threat Hunting was performed using:

```text
Threat Hunting → Discover
```

Observed Events:

* PAM Login Sessions
* Authentication Events
* Rootcheck Alerts
* Sudo Activity
* System Events

---

# 🛠️ Custom Detection Engineering

A custom Wazuh rule was created to detect reconnaissance activity.

### Custom Rule

```xml
<rule id="100500" level="12">
  <match>nmap</match>
  <description>Nmap Reconnaissance Detected</description>
  <group>recon,custom,</group>
</rule>
```

### Rule Deployment

```bash
sudo nano /var/ossec/etc/rules/local_rules.xml
```

Validate:

```bash
sudo /var/ossec/bin/wazuh-analysisd -t
```

Restart:

```bash
sudo systemctl restart wazuh-manager
```

---

# 🧪 Rule Testing

Generated Event:

```bash
echo "nmap scan detected" | logger
```

Result:

✅ Custom Alert Generated

### Alert Information

```text
Rule ID: 100500
Level: 12
Description:
Nmap Reconnaissance Detected
```

---

# 📁 File Integrity Monitoring (FIM)

Activities Performed:

```bash
sudo touch /etc/resume-test.txt

echo "modified by pavan" | sudo tee -a /etc/resume-test.txt

sudo chmod 777 /etc/resume-test.txt

sudo rm /etc/resume-test.txt
```

Purpose:

* File Creation Detection
* File Modification Detection
* Permission Change Detection
* File Deletion Detection

---

# 🔐 Authentication Monitoring

Observed Events:

* Login Success
* Login Sessions
* PAM Activity
* Root Access
* Authentication Logs

These events provide visibility into user activity across monitored endpoints.

---

# ⬆️ Privilege Escalation Monitoring

Commands Used:

```bash
sudo whoami

sudo su
```

Observed Alert:

```text
Rule ID: 5402

Successful sudo to ROOT executed
```

Importance:

Privilege escalation monitoring helps identify unauthorized administrative activity.

---

# 📚 Skills Demonstrated

### SIEM

* Wazuh Deployment
* Dashboard Management
* Agent Management

### Security Operations

* Alert Investigation
* Threat Hunting
* Event Analysis

### Detection Engineering

* Custom Rule Development
* Alert Tuning
* Rule Validation

### Linux Administration

* Networking
* Service Management
* LVM Storage Expansion

---

# 🚀 Future Improvements

## Vulnerability Detection

* CVE Monitoring
* Package Assessment

## Active Response

* Automatic IP Blocking
* Brute Force Mitigation

## Suricata Integration

* Network IDS
* Port Scan Detection
* Exploit Detection

## SSH Brute Force Detection

* Hydra Simulations
* Authentication Monitoring

## MITRE ATT&CK Mapping

* Tactics
* Techniques
* Detection Coverage

## Metasploitable Monitoring

* Vulnerable Host Monitoring
* Attack Simulation

---

# 📈 Project Outcomes

Successfully built a SOC Home Lab capable of:

✅ Centralized Log Collection

✅ Endpoint Monitoring

✅ Threat Hunting

✅ Detection Engineering

✅ Authentication Monitoring

✅ Privilege Escalation Monitoring

✅ File Integrity Monitoring

✅ Alert Investigation

---

# 🏆 Key Takeaways

This project provided hands-on experience with real-world SOC analyst workflows including:

* Security Monitoring
* Incident Investigation
* Threat Hunting
* SIEM Administration
* Detection Engineering
* Linux Security Operations

The lab serves as a strong foundation for future work in Blue Team Operations, Incident Response, Threat Hunting, and Detection Engineering.

---

## 👨‍💻 Author

**Pavan (Pa1)**

Mechanical Engineering Student | Cybersecurity Enthusiast

Currently focused on:

* SOC Analysis
* Threat Hunting
* Detection Engineering
* Blue Team Security
