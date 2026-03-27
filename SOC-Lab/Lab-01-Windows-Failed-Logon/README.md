## Objective

Simulate failed logon attempts on a Windows endpoint and validate detection through Wazuh SIEM.

## Scope

This lab focuses on Windows authentication failure events generated manually on the monitored endpoint and their visibility within the Wazuh dashboard.

## Environment

- **SIEM:** Wazuh Manager — 192.168.56.10
- **Target Endpoint:** windows-victim — 192.168.56.40
- **Agent ID:** 002
- **Network:** VMnet10 — 192.168.56.0/24

## Pre-Conditions

Before starting the simulation:

- The Windows agent was verified as **Active** in the Wazuh dashboard
- Connectivity between all machines was confirmed
- Log ingestion into Wazuh was functioning correctly

## Attack Simulation

Multiple failed logon attempts were manually generated on the Windows endpoint by entering incorrect credentials during the login process.

## Detection

Wazuh successfully detected multiple authentication failure events from the Windows Security Event Log.

![LogOn Failure](../images/lab02-logon-failure.png)

**Observed alert details:**

- **Agent:** windows-victim
- **Rule ID:** 60122
- **Event ID:** 4625
- **Description:** Logon failure - Unknown user or bad password
- **Level:** 5

## Analysis

The detected events correspond to **Windows Event ID 4625**, which indicates failed authentication attempts.

### Key Observations

- **Logon Type:** 2 → Interactive login
- **Source IP Address:** 127.0.0.1 → Local machine
- **Target User:** victim
- **Process:** svchost.exe
- **Failure Reason:** Incorrect password

### Interpretation

The use of **Logon Type 2** confirms that the login attempts were performed locally on the system.

The source IP address (**127.0.0.1**) indicates that the activity originated from the same host, meaning this was not a remote attack.

The status codes:

- `0xC000006D`
- `0xC000006A`

indicate that the username exists but the password provided was incorrect.

### Behavioral Insight

Multiple failed authentication attempts within a short time frame may indicate:

- User login errors
- Password guessing
- Early stages of brute-force activity

In a real-world environment, this pattern would require further investigation.

## MITRE ATT&CK Mapping

- **Tactic:** Credential Access
- **Technique:** T1110 — Brute Force

Although Wazuh maps this alert to **T1078 (Valid Accounts)**, the observed behavior aligns more closely with brute-force or password guessing activity due to repeated failed attempts.

## Findings

- Windows Event ID 4625 logs were successfully generated and detected
- Wazuh agent correctly forwarded authentication failure events
- Multiple failed login attempts were observed in a short time window
- Activity was identified as **local and interactive**

## Recommendations

- Monitor repeated failed logon attempts across endpoints
- Implement account lockout policies
- Enforce strong password policies
- Enable multi-factor authentication (MFA)
- Correlate failed logons with successful logins for compromise detection

## Conclusion

This lab validated the ability of Wazuh SIEM to detect and process Windows authentication failure events. It also provided a foundational understanding of how failed logons are recorded, analyzed, and interpreted in a SOC environment.
