# 1. Overview

This project consists of a personal SOC lab built using VMware Workstation, designed to simulate real-world attack and detection scenarios using Wazuh SIEM.

# 2. Objectives

- Build a segmented virtual network
- Deploy a SIEM (Wazuh)
- Simulate attacks from Kali Linux
- Detect and analyze security events
- Map findings to MITRE ATT&CK

# 3. Lab Architecture

The lab consists of four virtual machines connected through an isolated host-only network (VMnet10).

```
Host Machine
│
├── VMnet8 (NAT – Internet)
│
└── VMnet10 (Host-only – SOC Network)
        │
        ├── Wazuh SIEM
        │   IP: 192.168.56.10
        │
        ├── Ubuntu Agent
        │   IP: 192.168.56.20
        │
        ├── Kali Linux Attacker
        │   IP: 192.168.56.30
        │
         └── Windows Victim
            IP: 192.168.56.40
```

# 4. Network Design

Network: `192.168.56.0/24`

- Wazuh SIEM → 192.168.56.10
- Ubuntu Agent → 192.168.56.20
- Kali Linux → 192.168.56.30
- Windows Victim → 192.168.56.40

### Network Segmentation

- **VMnet10 (Host-only):** Internal SOC network used for all attack and monitoring traffic
- **VMnet8 (NAT):** Provides internet access for updates and package installation

### Traffic Flow

All attack traffic originates from Kali Linux and targets internal machines through VMnet10, ensuring full visibility by the Wazuh SIEM.

# 5. Virtual Macines

<aside>

[Time Management](https://www.notion.so/3283d223f00881238aead9740daafc48?pvs=21)

</aside>

# 6. Installation

The lab environment was built using VMware Workstation with four virtual machines: Wazuh SIEM, Ubuntu agent, Windows victim, and Kali Linux attacker.

### Wazuh SIEM Installation (Ubuntu Server)

The Wazuh manager was installed on an Ubuntu Server system using the official APT repository.

Steps:

- Updated system packages
- Added Wazuh repository and GPG key
- Installed Wazuh manager
- Enabled and started the Wazuh service

### Ubuntu Agent Installation

The Wazuh agent was installed on the Ubuntu endpoint.

Steps:

- Installed Wazuh agent package
- Configured the manager IP address (192.168.56.10)
- Registered the agent with the manager
- Started and enabled the agent service

### Windows Agent Installation

The Wazuh agent was installed on the Windows 10 machine using the official MSI package.

Steps:

- Downloaded the Wazuh agent installer
- Configured manager IP (192.168.56.10)
- Assigned agent name (windows-victim)
- Installed and started the Wazuh service

### Kali Linux Setup

Kali Linux was configured as the attacker machine.

Steps:

- Connected to VMnet10 (internal network)
- Configured IP address within 192.168.56.0/24
- Verified connectivity with target machines

# 7. Configuration

The lab was configured to simulate a segmented network environment with controlled communication between machines.

### Network Configuration

- VMnet10 was configured as a host-only network for internal communication
- VMnet8 (NAT) was used to provide internet access
- All attack and detection traffic flows through VMnet10

### IP Address Configuration

Static IP addresses were assigned to all machines:

- Wazuh SIEM → 192.168.56.10
- Ubuntu Agent → 192.168.56.20
- Kali Linux → 192.168.56.30
- Windows Victim → 192.168.56.40

Ubuntu systems were configured using Netplan to ensure persistent IP settings.

### Wazuh Agent Configuration

Each agent was configured to communicate with the Wazuh manager:

- Manager IP: 192.168.56.10
- Unique agent names assigned per host
- Agents registered and authenticated with the manager

### Firewall Configuration

- Windows Firewall was configured to allow ICMP traffic for connectivity testing
- Required ports (1514 and 1515) were enabled for Wazuh communication

### Connectivity Validation

- All machines successfully communicate via ICMP (ping)
- Wazuh agents appear as **Active** in the dashboard
- Logs are successfully ingested into the SIEM

# 8. Validation

- All machines have successful connectivity verified via ICMP
- Wazuh agents are active and running
- Security logs are successfully collected and stored in the SIEM

# 9. Troublesshooting

### DNS Resolution Issue

**Error:**

Temporary failure resolving archive.ubuntu.com

**Fix:**

- Enabled NAT interface
- Verified internet connectivity

### Network Interface Down (ens37)

**Temporary Fix:**

```bash
ip link set ens37 up
ip addr add 192.168.56.10/24 dev ens37
```

**Permanent Fix:**

- Configured Netplan for persistent network configuration

### Wazuh Installation Script Error

**Error:**

AccessDenied (XML response)

**Fix:**

- Used official APT repository instead of installation script

### Agent Not Connecting

**Error:**

Agent version must be lower or equal to manager version

**Fix:**

- Installed compatible version (4.7.5)

### Static IP Configuration (Netplan)

### Issue

Network interfaces were initially configured using DHCP, causing IP address changes after system reboot. This affected communication between agents and the Wazuh manager.

### Fix

DHCP was disabled and static IP addresses were configured manually using Netplan to ensure consistent network communication.
