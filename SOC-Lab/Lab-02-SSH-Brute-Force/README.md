## Objective

Simulate repeated SSH authentication failures from an external host (Kali Linux) against a monitored Ubuntu endpoint and validate detection through Wazuh SIEM.

## Scope

This lab focuses on identifying brute-force authentication attempts against an SSH service using real network traffic between attacker and target machines.

## Environment

- **SIEM:** Wazuh Manager — 192.168.56.10
- **Target Endpoint:** ubuntu-agent — 192.168.56.20
- **Attacker:** Kali Linux — 192.168.56.30
- **Network:** VMnet10 — 192.168.56.0/24

## Pre-Conditions

- SSH service was installed and enabled on the Ubuntu endpoint
- Wazuh agent was active and reporting logs
- Network connectivity between Kali and Ubuntu was verified
- Port 22/TCP confirmed open via Nmap

## Attack Simulation

A brute-force attack was simulated using Hydra from the Kali Linux machine targeting the SSH service on the Ubuntu endpoint.

Command used:

```bash
hydra -l testuser -P passwords.txt ssh://192.168.56.20 
```

![image.png](attachment:44ca5a5c-f36f-409f-b692-88b2450e0b10:image.png)

A controlled password list was used to generate multiple authentication attempts.

## Detection

Wazuh successfully detected multiple SSH authentication failures.

**Observed alert details:**

- **Agent:** ubuntu-agent
- **Rule ID:** 5760
- **Description:** sshd: authentication failed
- **Level:** 5
- **Log Source:** `/var/log/auth.log`

![image.png](attachment:7034f9e3-5b09-4807-ae35-d9a615767fcf:image.png)

## Analysis

The detected events correspond to repeated failed SSH authentication attempts captured from the system authentication logs.

### Key Observations

- **Source IP:** 192.168.56.30 (Kali attacker)
- **Target User:** testuser
- **Service:** SSH (sshd)
- **Log Source:** /var/log/auth.log
- **Event Pattern:** Multiple failed login attempts in a short time period

### Log Evidence

![image.png](attachment:117de080-dce0-4fb4-9634-b577140b8720:image.png)

### Interpretation

The activity clearly shows repeated authentication attempts originating from a single external host (Kali Linux) targeting the same user account.

This behavior is consistent with:

- Password guessing
- Brute-force attack attempts

![image.png](attachment:e6cf3a23-4325-41ee-a405-59630b513870:image.png)

## MITRE ATT&CK Mapping

- **Tactic:** Credential Access
- **Technique:** T1110 — Brute Force

The repeated login failures from a single source strongly align with brute-force behavior targeting valid accounts.

## Findings

- Multiple SSH authentication failures were successfully generated
- Attack traffic originated from an external host (Kali Linux)
- Wazuh correctly ingested and detected authentication logs
- The attack pattern was clearly visible and attributable to a single source IP

## Recommendations

- Enforce strong password policies
- Disable password-based SSH authentication where possible
- Implement SSH key-based authentication
- Deploy tools such as Fail2Ban to limit repeated login attempts
- Monitor repeated failed authentication attempts from the same IP
- Consider rate-limiting SSH connections

## Conclusion

This lab successfully demonstrated the detection of SSH brute-force activity using Wazuh SIEM. It highlights how repeated authentication failures from a single external source can be identified and analyzed to detect potential credential-based attacks.
