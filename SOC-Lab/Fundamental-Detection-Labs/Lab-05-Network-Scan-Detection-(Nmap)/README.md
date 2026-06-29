## Objective

Simulate a network scan from an attacker machine (Kali Linux) against a monitored Linux endpoint and analyze how scan activity is reflected in host-based logs using Wazuh SIEM.

## Enviroment

| Component | Role | IP Addres  |
| --- | --- | --- |
| Wazuh Siem | Log Analysis | 192.168.56.10 |
| Ubuntu Agent | Target | 192.168.56.20 |
| Kali Linux | Attacker | 192.168.56.30 |

## Pre-Conditions

Ubuntu agent active and reporting to Wazuh
Network connectivity verified between all machines
SSH service running on Ubuntu target

## Attack Simulation

A network scan was performed using Nmap from the attacker machine:

```bash
nmap -sS -p- 192.168.56.20
```

![image.png](attachment:ddb0d7f0-45af-4a33-987a-31c3eab50b76:image.png)

## Detection

Although Wazuh does not explicitly classify port scanning as a standalone alert, multiple anomalous SSH-related events were detected during the scan.

![image.png](attachment:e5c34101-723d-4364-a535-c772cb03564f:image.png)

## Host Log Evidence

System logs from the Ubuntu agent revealed multiple suspicious connection attempts originating from the attacker machine:

![image.png](attachment:9c285f02-94c7-44d9-b580-4c37619653d1:image.png)

## Observed Indicators

- Multiple connection attempts from a single IP (192.168.56.30)
- Protocol negotiation errors
- Invalid user login attempts
- Rapid connection closures (pre-authentication)

## Analysis

The activity observed corresponds to a network reconnaissance scan.

While Wazuh does not natively detect port scans as a specific alert, it provides strong indicators through:

- SSH anomalies
- Repeated failed authentication attempts
- Abnormal connection patterns

These artifacts allow analysts to infer scanning behavior at the host level.

## MITRE ATT&CK Mapping

- **T1046 — Network Service Discovery**

## Recommendations

- Implement network-based IDS (e.g., Suricata) for full visibility
- Restrict SSH access using firewall rules
- Enable rate limiting and intrusion prevention (Fail2Ban)
- Monitor repeated connection anomalies

## Conclusion

The lab demonstrates how host-based monitoring solutions like Wazuh can detect indirect indicators of network scanning activity, even without dedicated network traffic analysis.
