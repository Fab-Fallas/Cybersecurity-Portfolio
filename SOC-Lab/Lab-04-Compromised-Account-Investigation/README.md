## Objective

Simulate a brute-force attack from an external machine (Kali Linux) against a Windows endpoint using SMB authentication and detect the activity using Wazuh SIEM.

## Environment

| Component | Role | IP Address |
| --- | --- | --- |
| Wazuh SIEM | Log analysis | 192.168.56.10 |
| Windows Victim | Target | 192.168.56.40 |
| Kali Linux | Attacker | 192.168.56.30 |

## Pre-Conditions

- The Windows agent was active and reporting to Wazuh
- Network connectivity between all machines was verified
- SMB service (port 445) was accessible on the Windows machine
- A valid user account (`victim`) existed on the target system

## Attack Simulation

A brute-force attack was performed from Kali Linux against the Windows endpoint using SMB authentication.

Due to compatibility limitations with Hydra and modern SMB implementations, **CrackMapExec** was used to perform the attack.

Multiple authentication attempts were executed using a password wordlist against the user account `victim`.

![image.png](attachment:8c61addf-057f-48ba-a87d-8320287f1e0c:image.png)

<aside>
🧠

CrackMapExec was used instead of Hydra due to better compatibility with modern SMB implementations, reflecting real-world offensive tooling.

</aside>

## Detection

Wazuh successfully detected multiple failed authentication attempts from the Windows Security Event Log.

![image.png](attachment:da6c8074-06aa-431a-82a3-dbfebd857227:image.png)

## Observed Alert Details

- **Agent:** windows-victim
- **Rule ID:** 60122
- **Event ID:** 4625
- **Description:** Logon failure – Unknown user or bad password
- **Level:** 5

![image.png](attachment:50df0d2d-4ecb-47e7-8fd5-471da8d42955:image.png)

## Analysis

The detected events correspond to **Windows Event ID 4625**, indicating failed authentication attempts.

The analysis of the event logs revealed the following:

- The source IP address was **192.168.56.30**, corresponding to the Kali attacker machine
- The authentication attempts used the **NTLM protocol**, commonly associated with SMB authentication
- The **Logon Type was 3 (Network)**, confirming that the login attempts were performed remotely
- The status codes:
    - `0xc000006d` → Failed authentication
    - `0xc000006a` → Incorrect password

These findings indicate that:

- The attacker was targeting a **valid user account (`victim`)**
- The passwords used were incorrect
- Multiple repeated attempts were made within a short time frame

This behavior is consistent with a **brute-force attack over SMB**.

## MITRE ATT&CK Mapping

- **Tactic:** Credential Access
- **Technique:** T1110 — Brute Force

## Key Findings

- Multiple failed login attempts detected
- Attack originated from an external machine (Kali Linux)
- Targeted a valid user account
- Network-based authentication (SMB) confirmed
- Clear brute-force pattern identified

## Recommendations

- Implement account lockout policies
- Enable Multi-Factor Authentication (MFA)
- Restrict SMB access to trusted hosts only
- Monitor repeated failed authentication attempts
- Use network segmentation to limit lateral movement
