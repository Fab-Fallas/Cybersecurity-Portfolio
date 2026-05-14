# Scenario 01 - Network Reconnaissance Detection (Nmap)

**MITRE ATT&CK:** T1595.001 - Active Scanning

### Objective
Detect network reconnaissance activity using Suricata IDS and visualize the alerts in Wazuh SIEM.

### Attack Commands (Kali Linux)

```bash
nmap -sS -sV -O -T4 --top-ports 300 192.168.56.20
```
### Detection

Suricata: Detected as decoder events + custom local rules
Wazuh Rule: Suricata integration (events ingested via eve.json)
Severity: 3 (decoder level) - Expected in current tuning phase


### Evidence

Suricata fast.log

Wazuh Dashboard - Security Alerts

Eve.json example

Analysis

Source IP: 192.168.56.30 (Kali Attacker)
Destination: Ubuntu Agent (192.168.56.20)
Technique observed: Port scanning + OS fingerprinting
Integration validated: All Suricata events successfully forwarded to Wazuh

Response & Lessons Learned

Confirmed end-to-end log pipeline: Suricata → Wazuh Agent → Wazuh Manager
Identified need for rule tuning and queue optimization (Agent event queue is full)
Demonstrated troubleshooting capabilities in a SOC-like environment

Status: Detection + Integration validated
