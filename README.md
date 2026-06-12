# 🛡️ Enterprise SOC Home Lab — Wazuh SIEM

A fully functional Security Operations Center (SOC) home lab built using **Wazuh SIEM**, **Ubuntu Server 26.04 LTS**, **Kali Linux 2026.2**, and **VirtualBox**. This project demonstrates hands-on experience in threat detection, log analysis, detection engineering, and SOC analyst workflows.

---

## 📸 Preview

![Threat Hunting Dashboard](screenshots/threat_hunting_dashboard.png)

---

## 🧱 Lab Architecture

| Component | Role | IP |
|---|---|---|
| Ubuntu Server 26.04 | Wazuh Manager + Dashboard + Indexer | 192.168.56.10 |
| Kali Linux 2026.2 | Attack Simulation + Wazuh Agent | 192.168.56.20 |
| Metasploitable 2 | Vulnerable Target | 192.168.56.30 |
| Windows 10 | Additional Endpoint | 192.168.56.x |

**Network:** NAT (internet) + Internal Network `SOC-LAB` (isolated SOC comms)

---

## ⚙️ Stack

- **SIEM:** Wazuh v4.12.0 (All-in-One)
- **OS:** Ubuntu Server 26.04 LTS / Kali GNU/Linux 2026.2
- **Virtualisation:** Oracle VirtualBox
- **Agent:** Wazuh Agent v4.12.0

---

## ✅ What Was Built

- [x] Deployed Wazuh All-in-One on Ubuntu Server
- [x] Configured VirtualBox NAT + Internal Network (`SOC-LAB`)
- [x] Enrolled Kali Linux as active Wazuh agent (`kali-attacker`)
- [x] Wrote custom detection rules (XML)
- [x] Simulated real attacks from Kali Linux
- [x] Performed threat hunting via Wazuh dashboard
- [x] Mapped detections to MITRE ATT&CK
- [x] Monitored authentication and privilege escalation events
- [x] Tested File Integrity Monitoring (FIM)

---

## 🔍 Detections & Alerts

| Rule ID | Description | Level | MITRE |
|---|---|---|---|
| 5402 | Successful sudo to ROOT executed | 3 | T1548.003 |
| 5501 | PAM: Login session opened | 3 | T1078 |
| 5502 | PAM: Login session closed | 3 | T1078 |
| 100500 | Nmap reconnaissance Detected *(custom)* | 5 | T1046 |

**Total alerts generated:** 496  
**Level 12+ alerts:** 1  
**Auth successes monitored:** 48

---

## 🧠 Custom Detection Rule — Rule 100500

```xml
<rule id="100500" level="5">
  <match>nmap</match>
  <description>Nmap reconnaissance Detected</description>
  <group>recon,custom,</group>
</rule>
```

Deployed at `/var/ossec/etc/rules/local_rules.xml`

---

## 🎯 MITRE ATT&CK Coverage

| Technique | ID |
|---|---|
| Valid Accounts | T1078 |
| Sudo and Sudo Caching | T1548.003 |
| Create Account | T1136 |
| Disable or Modify Tools | T1562 |
| Network Service Scanning | T1046 |

---

## 🔧 Attack Simulations Run

```bash
# File Integrity Monitoring
sudo touch /etc/resume-test.txt
echo "modified by pavan" | sudo tee -a /etc/resume-test.txt
sudo chmod 777 /etc/resume-test.txt
sudo rm /etc/resume-test.txt

# Nmap Recon
nmap -sV 192.168.56.10

# SSH Brute Force (Hydra)
hydra -l root -P passwords.txt ssh://192.168.56.10
```

---

## 🚧 Challenges Solved

| Challenge | Solution |
|---|---|
| Wazuh installer: insufficient RAM | Increased VM RAM from 4GB → 6GB |
| Indexer flood-stage watermark (read-only) | `lvextend` + `resize2fs` to expand LVM |
| Dashboard inaccessible from host | Accessed via Kali browser at `https://192.168.56.10` |
| XML syntax errors in custom rules | Validated with `wazuh-analysisd -t` before restart |

---

## 🚀 Future Plans

- [ ] Enable Vulnerability Detection (CVE feeds)
- [ ] Active Response — auto-block brute force IPs
- [ ] Suricata IDS integration
- [ ] Deploy agent on Metasploitable
- [ ] Build custom dashboards (FIM, Auth, Recon)
- [ ] SOAR integration (TheHive / Shuffle)

---

## 📁 Repository Structure
