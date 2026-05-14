# Scenario 01 - Network Reconnaissance Detection (Nmap)

**MITRE ATT&CK:** T1595.001 - Active Scanning

### Objective
Detect network reconnaissance activity using Suricata IDS and visualize the alerts in Wazuh SIEM.

### Attack Commands (Kali Linux)

```bash
nmap -sS -sV -O -T4 --top-ports 300 192.168.56.20
