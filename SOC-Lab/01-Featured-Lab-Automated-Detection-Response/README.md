# Automated Threat Detection & Response Lab

Designed and implemented a SOC-focused cybersecurity lab to simulate real-world brute-force attacks and demonstrate automated threat detection and response workflows using Wazuh SIEM and Kali Linux.

This project showcases hands-on experience in security monitoring, log analysis, incident investigation, and automated response by detecting SSH brute-force activity, generating real-time alerts, and automatically blocking malicious IP addresses through Wazuh Active Response.

The lab environment includes Linux-based systems monitored through centralized SIEM logging and attack simulation tools commonly used in offensive security testing. Events were analyzed and correlated to identify attack patterns, validate detections, and map activity to the MITRE ATT&CK framework.

## Key Highlights

* Real-time threat detection with Wazuh SIEM
* Automated attacker IP blocking using Active Response
* SSH brute-force attack simulation with Hydra
* Security event correlation and alert investigation
* Linux endpoint monitoring and log analysis
* MITRE ATT&CK mapping for detected techniques
* Practical SOC workflow simulation in a virtualized lab environment

![image.png](attachment:c3dce5a2-d607-402b-80d2-bc2b1ee2fe30:92b477ad-a238-40f9-a339-43a6471c25c8.png)

# 1. Overview

> This lab simulates a brute-force attack against an SSH service in a controlled environment.
> 
> 
> The objective is to analyze how such attacks are recorded at the endpoint level and validate the presence of malicious activity through system logs before SIEM correlation.
> 

---

# 2. Objectives

- Simulate network reconnaissance using Nmap
- Perform a brute-force attack using Hydra
- Analyze authentication logs at the endpoint
- Identify indicators of compromise (IOC)
- Validate attacker activity using real log evidence

---

# 3. Lab Architecture

```
Attacker (Kali Linux) → Target (Ubuntu Agent) → Wazuh SIEM
```

### Network:

- Internal Lab Network: `192.168.56.0/24`

---

# 4. Virtual Machines

| Role | OS | IP |
| --- | --- | --- |
| Attacker | Kali Linux | 192.168.56.130 |
| Target | Ubuntu Server | 192.168.56.20 |
| SIEM | Wazuh Server | 192.168.56.10 |

<aside>
💡

**Phase 1**

</aside>

# Environment Validation (Baseline)

> Before executing the attack simulation, the lab environment was validated to ensure all systems were operational and properly configured.
> 

---

## Connectivity Check

```
ping-c4192.168.56.20
```

**Result:**

- Successful communication between attacker and target

---

## SSH Service Verification

```
sudo systemctl statusssh
```

![image.png](attachment:a11dfcf2-54c2-4e6d-80ab-3787063e4db8:image.png)

**Result:**

- SSH service active and running on target machine

---

## Wazuh Agent Status

```
sudo systemctl status wazuh-agent
```

![image.png](attachment:fc3f0afa-41d7-4c30-a0b6-1b4182ea274a:image.png)

**Result:**

- Agent active and connected to Wazuh server

![image.png](attachment:67603e47-3b3c-40f5-9cea-9633b713dd7f:image.png)

---

## Service Exposure Validation

```
nmap-sT192.168.56.20
```

![image.png](attachment:ad8f77da-4de4-4a32-9a02-757cee21ec16:image.png)

**Result:**

- Port 22 (SSH) confirmed as open

---

## Baseline Observation

> No abnormal authentication activity or security alerts were observed prior to the attack simulation.
> 
> 
> This establishes a clean baseline for comparison during the attack phase.
> 

# Controlled Initial Attack

## Description

> A controlled brute-force attack was simulated against the SSH service of the target machine using Hydra.
> 
> 
> The objective of this phase was to generate multiple failed authentication attempts in order to analyze how such activity is recorded at the endpoint level.
> 

---

## Attack Setup

- **Attacker Machine:** Kali Linux
- **Target Machine:** Ubuntu Server (SSH enabled)
- **Target Service:** SSH (Port 22)
- **Username Tested:** `testuser`
- **Password List:** Custom wordlist (`passwords.txt`)

---

## Command Used

```
hydra-l testuser-P passwords.txtssh://192.168.56.20
```

![image.png](attachment:9a0e7f73-01ae-4235-8cfc-e5e4c4e0168b:image.png)

---

## Attack Objective

- Simulate a brute-force attack against SSH
- Generate repeated failed login attempts
- Create log activity for further analysis
    
    ![image.png](attachment:a2c42438-c40a-4ec9-8c37-a6af94e19f7e:image.png)
    

---

## Observed Behavior

> The attack generated multiple authentication attempts against the target system within a short time frame.
> 
> 
> Each attempt resulted in a failed login, producing a high volume of log entries related to SSH authentication failures.
> 

---

## Initial Evidence

Although the main analysis is performed in the next phase, during execution it was observed that:

- The target system responded to each authentication attempt
- No access was granted
- Multiple connection attempts were initiated from a single source IP

---

## ⚠️ Important Note

> This attack was performed in a controlled lab environment for educational purposes only.
> 
> 
> No real systems or unauthorized targets were affected.
> 

<aside>
💡

**Phase 2**

</aside>

# Endpoint Log Analysis

![image.png](attachment:0f0abc15-2557-43d7-965c-9e86b8d106aa:image.png)

## Description

> In this phase, authentication logs from the target system were analyzed to identify evidence of the brute-force attack.
> 
> 
> The objective was to validate malicious activity directly at the endpoint level by examining system-generated logs.
> 

---

## Log Source

```
/var/log/auth.log
```

![image.png](attachment:75657d27-79cb-419d-86d1-bffa3a50a2eb:image.png)

---

## Commands Used

### 🔎 Filtrar intentos fallidos

```
sudogrep -i "failed\|invalid" /var/log/auth.log
```

![image.png](attachment:75657d27-79cb-419d-86d1-bffa3a50a2eb:image.png)

---

### 🔢 Contar número de intentos

```
sudogrep"192.168.56.30" /var/log/auth.log |wc-l
```

![image.png](attachment:ca5252f6-09e7-44b0-96bc-7477ea504b71:image.png)

---

## Evidence of Attack

```
Failed password for testuser from 192.168.56.30 port 35402 ssh2
Failed password for testuser from 192.168.56.30 port 35398 ssh2
Failed password for testuser from 192.168.56.30 port 35418 ssh2
```

---

## Analysis

> The authentication logs clearly show multiple failed login attempts targeting the user `testuser` from a single source IP (`192.168.56.30`).
> 
> 
> The frequency and repetition of these events strongly indicate an automated brute-force attack.
> 

---

## Indicators of Compromise (IOCs)

- Multiple failed login attempts
- Repeated activity from a single IP (`192.168.56.30`)
- Targeted user account (`testuser`)
- SSH service being abused

---

## Attack Pattern

> The attacker performed repeated authentication attempts in rapid succession, which is consistent with automated tools such as Hydra.
> 
> 
> This behavior is commonly associated with credential guessing attacks.
> 

---

## Key Finding

> The endpoint logs alone provide sufficient evidence to confirm the presence of a brute-force attack without requiring SIEM correlation.
> 

---

## Professional Insight

> Early detection at the endpoint level is critical, as it allows security teams to identify malicious activity even before centralized monitoring systems generate alerts.
> 

<aside>
💡

**Phase 3**

</aside>

## Wazuh Detection Validation

> After confirming the brute-force activity at the endpoint level, the same events were reviewed in Wazuh to validate centralized detection.
> 
> 
> Wazuh successfully collected authentication logs from the Ubuntu agent and generated security events related to failed SSH login attempts.
> 

### Search Filters Used

```
agent.name: ubuntu-agent
```

```
sshd
```

```
192.168.56.30
```

### Detection Result

> The SIEM displayed multiple SSH authentication failure events associated with the attacker IP.
> 
> 
> This confirms that endpoint logs were successfully forwarded, parsed, and correlated by Wazuh.
> 

### Analyst Note

> Endpoint validation and SIEM validation were both performed to confirm the attack from two perspectives: the original log source and the centralized monitoring platform.
> 

<aside>
💡

**Phase 4**

</aside>

# Detection Tuning (Custom Rule Creation & Validation)

## Description

> In this phase, a custom detection rule was created in Wazuh to identify brute-force attacks based on multiple failed SSH authentication attempts.
> 
> 
> The goal was to enhance detection capabilities by correlating repeated events within a defined timeframe.
> 

---

# Rule Development

## Base Rule Identified

- **Rule ID:** `5760`
- **Description:** SSH authentication failed
- **Level:** 5

> This rule detects individual failed login attempts but does not identify attack patterns.
> 

---

## Custom Rule Implementation

A new rule was created to detect multiple failed authentication attempts from the same source IP:

```xml
<group name="local,ssh,bruteforce,">
  <rule id="100001" level="10" frequency="5" timeframe="60">
    <if_matched_sid>5760</if_matched_sid>
    <same_source_ip/>
    <description>Possible SSH brute force attack detected from same source IP</description>
    <mitre>
      <id>T1110</id>
    </mitre>
  </rule>
</group>
```

![image.png](attachment:25a125a2-e586-467b-8148-83ddeddd9eda:image.png)

---

## Rule Logic Explanation

- **if_matched_sid:** Links to base rule (failed SSH login)
- **frequency:** Triggers after 5 events
- **timeframe:** Within 60 seconds
- **same_source_ip:** Ensures correlation from same attacker
- **level:** Increased severity (10)
- **MITRE Mapping:** T1110 (Brute Force)

---

# Rule Deployment

After adding the rule:

```bash
sudo systemctl restart wazuh-manager
```

---

# Validation Process

To validate the rule:

1. The brute-force attack was executed again using Hydra
2. Multiple failed authentication attempts were generated
3. Wazuh events were monitored in real-time

![image.png](attachment:00305b81-d0c6-4549-aa2e-0e4f55b9384a:image.png)

---

# Detection Result

> The custom rule (ID 100001) was successfully triggered after multiple failed SSH login attempts from the same source IP.
> 

![image.png](attachment:6eb14dbe-c45d-4bea-b522-ab7c37d525d4:image.png)

---

# Analysis

> The SIEM successfully correlated multiple authentication failures and identified them as a brute-force attack.
> 
> 
> This demonstrates the effectiveness of custom rule tuning in improving detection accuracy.
> 

---

# Key Improvement

Before:

- Individual failed logins only
- No attack pattern recognition

After:

- Detection of brute-force behavior
- Correlation of multiple events
- Higher severity alert

---

# Security Impact

> The implementation of this rule significantly improves the ability to detect credential-based attacks by identifying patterns rather than isolated events.
> 

<aside>
💡

**Phase 4**

</aside>

# Active Response Implementation & Validation

## Description

> In this phase, an automated response mechanism was implemented in Wazuh to block attacker IPs when a brute-force attack is detected.
> 
> 
> The objective was to move from detection to active defense by enforcing real-time mitigation at the endpoint level.
> 

---

# Active Response Configuration

The following configuration was added to `ossec.conf`:

```
<command>
<name>firewall-drop</name>
<executable>firewall-drop</executable>
<timeout_allowed>yes</timeout_allowed>
</command>

<active-response>
<command>firewall-drop</command>
<location>local</location>
<rules_id>100001</rules_id>
<timeout>600</timeout>
</active-response>
```

![image.png](attachment:df68adc7-f45a-4f1c-a1ae-3a6f86138d56:image.png)

---

## Configuration Explanation

- **command:** Defines the action to execute
- **firewall-drop:** Built-in script to block IPs
- **location:** `local` → executed on the agent (target machine)
- **rules_id:** Links response to brute-force detection rule
- **timeout:** Block duration (600 seconds)

---

# Deployment

After applying the configuration:

```
sudo systemctlrestart wazuh-manager
```

---

# Validation Process

To validate the active response:

1. The brute-force attack was executed again using Hydra
2. Wazuh detected the attack using the custom rule (ID 100001)
3. The active response mechanism was triggered
4. The firewall-drop script executed automatically

---

# Evidence of Execution

From active response logs:

```
active-response/bin/firewall-drop: Starting
active-response/bin/firewall-drop: add
```

![image.png](attachment:a3564a03-e66a-45a9-be52-beee03f38469:image.png)

---

# Attack Blocking Result

From the attacker machine (Kali):

```
[ERROR] could not connect to ssh://192.168.56.20:22 - Timeout connecting
```

![image.png](attachment:bcc1e7ab-3407-4b0e-a058-182c94bf9b77:image.png)

---

## Analysis

> The attacker machine was unable to establish a connection to the target system after the active response was triggered.
> 
> 
> This indicates that the firewall successfully blocked the attacker IP, preventing further attack attempts.
> 

---

# Firewall Verification

Firewall rules were inspected using:

```
sudo iptables-L-n
```

### Result:

```
DROP  all  --  192.168.56.30  0.0.0.0/0
```

![image.png](attachment:fbf9b8f3-bd23-45cb-a726-6553e5812a65:image.png)

---

## Interpretation

> The presence of a DROP rule for the attacker IP confirms that the automated response was successfully applied at the network level.
> 

---

# Security Impact

- Attack was detected in real time
- Response was executed automatically
- Attacker access was blocked
- System exposure was reduced

---

# Key Insight

> Active Response enables security systems to not only detect threats but also mitigate them automatically, significantly improving response time and reducing risk.
> 

---

# Important Consideration

> Automated blocking mechanisms must be carefully tuned to avoid false positives, which could result in legitimate users being denied access.
>
