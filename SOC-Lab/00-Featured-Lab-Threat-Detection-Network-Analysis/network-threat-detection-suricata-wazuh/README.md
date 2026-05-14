# Network Threat Detection & Investigation Lab  
**Suricata IDS + Wazuh SIEM + Wireshark**

![Lab Architecture](../architecture/lab-architecture.png)

**Status:** Completed  
**Level:** Junior SOC Analyst / SIEM Engineer  
**Date:** May 2026

### Objective
Deploy a functional IDS sensor (Suricata) integrated with a SIEM platform (Wazuh) to detect network-based attacks, perform packet analysis, and execute basic incident investigation workflows in an enterprise-like environment.

### Lab Architecture
- **Network:** VMnet10 (Host-Only) - 192.168.56.0/24
- **Wazuh All-in-One SIEM:** 192.168.56.10
- **Ubuntu Agent + Suricata IDS:** 192.168.56.20
- **Kali Linux (Attacker):** 192.168.56.30
- **Windows Victim:** 192.168.56.40

### Tools
- Suricata 8.0.4 (AF_PACKET mode)
- Wazuh 4.x All-in-One
- Wireshark
- Nmap, Hydra
- MITRE ATT&CK Framework

### Key Achievements
- Successful real-time integration of Suricata `eve.json` logs into Wazuh SIEM
- Detection of network reconnaissance (Nmap) and brute force attempts (SSH)
- Centralised visibility of IDS alerts in Wazuh Dashboard
- Incident investigation workflow (log analysis, IOC extraction, timeline)

### Implemented Scenarios
- **Scenario 01** → Network Reconnaissance Detection (Nmap)
- **Scenario 02** → SSH Brute Force Simulation (in progress)

### Skills Demonstrated
- IDS sensor deployment and configuration
- SIEM log ingestion and correlation (`localfile` monitoring of `eve.json`)
- Threat detection and basic incident response
- Troubleshooting (agent queue, firewall, rule loading)

---
# Scenario 01 - Network Reconnaissance Detection (Nmap)

**MITRE ATT&CK:** T1595.001 - Active Scanning

### Objective
Detect network reconnaissance activity using Suricata IDS and visualize the alerts in Wazuh SIEM.

### Attack Commands (Kali Linux)
```bash
nmap -sS -sV -O -T4 --top-ports 300 192.168.56.20
