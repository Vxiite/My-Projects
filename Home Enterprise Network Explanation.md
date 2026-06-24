## Project Overview

This laboratory project details the technical implementation of a segmented enterprise network environment. The architecture leverages a **pfSense** security appliance to manage separate routing zones, isolating an auditing/security testing segment from a production corporate directory environment.

The primary objectives of this lab are:
1. Constructing a stateful, segmented network boundary using VirtualBox.
2. Building an **Active Directory Domain Services (AD DS)** infrastructure to act as the corporate identity provider, provisioning structured user roles and access controls.
3. Conducting active network scanning and log auditing across routing planes.
4. Diagnosing and remediating core Linux network stack hangs and interface lease failures.

---

## Network Architecture & Topology

The topology uses a single gateway processing traffic across three distinct network interfaces.

* **WAN Interface (em0):** Configured via NAT to access an external upstream path (simulating an external ISP gateway).
* **LAN Segment (`hacker-net` / em1):** Subnet `192.168.1.0/24`. Host environment for the security auditing machine (**Kali Linux**). Inherits an implicit pass-all rule framework by default.
* **OPT1 Segment (`victim-net` / em2):** Subnet `192.168.10.0/24`. Hosts the corporate production zone containing the Windows Server Active Directory Domain Controller (`DC01`) and the enterprise Ubuntu Workstation. Inherits an implicit deny ruleset.

---

## Step 1: Virtual Infrastructure & Network Setup

### 1. pfSense Appliance Configuration

* **Hardware Profile:** 2 Cores, 2GB RAM.
* **Network Interfaces:**
  * **Adapter 1:** Enabled → Attached to `NAT`.
  * **Adapter 2:** Enabled → Attached to `Internal Network`, Named: `hacker-net`.
  * **Adapter 3:** Enabled → Attached to `Internal Network`, Named: `victim-net`.

### 2. Base Client Setup

* **Kali Linux VM:** 2 Cores, 4GB RAM. Network Adapter 1 → Attached to `Internal Network`, Named: `hacker-net`.
* **Ubuntu Workstation VM:** 2 Cores, 4GB RAM. Network Adapter 1 → Attached to `Internal Network`, Named: `victim-net`.
* **Windows Server 2022 (DC01):** 2 Cores, 4GB RAM. Network Adapter 1 → Attached to `Internal Network`, Named: `victim-net`.

---

## Step 2: Active Directory Domain Infrastructure & Role-Based Access Control

To establish the enterprise identity backbone, Windows Server 2022 was promoted to a Domain Controller handling Identity, Internal DNS, and role-based access management.

### 1. Network Parameters & Promotion

* **Static IP Address:** `192.168.10.10`
* **Subnet Mask:** `255.255.255.0`
* **Default Gateway:** `192.168.10.1`
* **Preferred DNS:** `127.0.0.1`
* **Alternate DNS:** `::1`
* **Domain Identity:** Promoted to forest root domain `robcorp.local`.

### 2. Organizational Unit (OU) Structure

To simulate a corporate Active Directory environment, Active Directory Users and Computers (ADUC) was used to organize user identities within a dedicated Organizational Unit (OU).

```text
robcorp.local (Root Domain)

└── Employees (OU)
    ├── Domain DA. Admin (Administrative User Account)
    └── Filbert FP. Picks (Standard Corporate User)
```

### 3. Fine-Grained Password Policy (FGPP)

To strengthen privileged account security, a Fine-Grained Password Policy (FGPP) was created through the Active Directory Administrative Center (ADAC).

The policy was configured within the Password Settings Container and assigned directly to the privileged administrative account.

**Administrative Password Policy Configuration:**

* Minimum Password Length: `14 Characters`
* Password History: `24 Previous Passwords`
* Complexity Requirements: `Enabled`
* Reversible Encryption: `Disabled`
* Target Account: `Domain DA. Admin`

This implementation demonstrates granular identity governance by applying elevated credential requirements exclusively to privileged accounts without impacting standard domain users.

---

## 3. Account Provisioning & Permissions Mapping

The following accounts were provisioned to support administrative separation and enterprise identity management.

### Domain DA. Admin (Administrative Identity)

**Logon Name:** `dadmin`

**Purpose:**
Dedicated privileged account used for Active Directory administration, domain management, and infrastructure configuration tasks.

**Privileges:**
- Member of the built-in **Domain Admins** group.
- Used exclusively for elevated administrative operations.
- Segregated from standard user activity to support least-privilege principles.

### Filbert FP. Picks (Standard Corporate Identity)

**Logon Name:** `filbert`

**Purpose:**
Represents a standard corporate workforce identity used for routine domain authentication and workstation activity.

**Privileges:**
- Inherits standard domain user permissions.
- Restricted from administrative functions.
- Cannot modify domain-level security configurations.

## Step 3: Practical Troubleshooting Case Study

### 1. Problem Definition

Following an extended period of inactivity, the Ubuntu Client encountered a boot hang while executing the `NetworkManager-wait-online.service` target. Once loaded into the desktop environment, the graphical network adapter panel was grayed out, displaying a complete loss of its assigned IP address configuration.

### 2. Diagnostic Investigation

Execution of `ip a` on the Ubuntu client confirmed that interface `enp0s3` was active (`UP`) but lacked an assigned `inet` address block.

```bash
ip a
```

Checking the pfSense physical console revealed that the `webConfigurator` dashboard was explicitly bound to the OPT1 address (`http://192.168.10.1`), remaining inaccessible from standard LAN networks while default block rules on OPT1 dropped reciprocal client traffic.

### 3. Engineering Remediation & Pivot Sequence

Because the graphical tools were unavailable due to the dropped lease state, a manual terminal configuration sequence was executed on the Ubuntu client to restore baseline connectivity to the gateway.

#### Flush dead interface assignments

```bash
sudo ip addr flush dev enp0s3
```

#### Manually register a static address within the OPT1 subnet

```bash
sudo ip addr add 192.168.10.50/24 dev enp0s3
```

#### Configure the default gateway

```bash
sudo ip route add default via 192.168.10.1 dev enp0s3
```

With the interface provisionally listening, the Kali Linux workstation (located on the LAN segment) was used to bypass the isolated OPT1 constraints.

Connecting to `http://192.168.1.1`, the firewall ruleset was modified:

1. Navigate to **Firewall → Rules → OPT1**
2. Create a testing rule:
   - **Action:** Pass
   - **Protocol:** Any
   - **Source:** Any
   - **Destination:** Any
3. Apply changes to update the rule tables

Returning to the Ubuntu terminal, the network daemon was restarted:

```bash
sudo systemctl restart NetworkManager
```

The interface immediately acquired a DHCP lease, eliminating the boot hang condition and restoring standard network connectivity.

---

## Step 4: Security Auditing & Traffic Analysis

### 1. Network Reconnaissance Target Discovery

From the Kali Linux workstation (`192.168.1.102`), a port discovery scan was directed at the primary LAN gateway:

```bash
nmap 192.168.1.1
```

**Results:**

```text
Host is up (0.00095s latency).

Not shown: 998 filtered tcp ports (no-response)

PORT   STATE SERVICE
53/tcp open  domain
80/tcp open  http

MAC Address: 08:00:27:5E:61:B8 (Oracle VirtualBox virtual NIC)
```

The scan confirmed that HTTP and DNS services were reachable while the firewall silently filtered the remaining probed ports.

### 2. Stateful Firewall Log Verification

Because pfSense filters LAN traffic silently by default, logging was enabled on the primary LAN allow rule.

1. Navigate to **Firewall → Rules → LAN**
2. Edit the default allow rule
3. Enable **Log packets that are handled by this rule**
4. Apply changes
5. Navigate to **Status → System Logs → Firewall → Normal View**

The resulting logs captured the Nmap scan activity:

| Action | Time | Interface | Rule | Source | Destination | Protocol |
|----------|----------|----------|----------|----------|----------|----------|
| PASS | Jun 22 16:22:35 | LAN | Default allow LAN to any | 192.168.1.102:57043 | 192.168.1.1:5862 | TCP:S |
| PASS | Jun 22 16:22:35 | LAN | Default allow LAN to any | 192.168.1.102:57043 | 192.168.1.1:27352 | TCP:S |
| PASS | Jun 22 16:22:35 | LAN | Default allow LAN to any | 192.168.1.102:57043 | 192.168.1.1:987 | TCP:S |
| PASS | Jun 22 16:22:35 | LAN | Default allow LAN to any | 192.168.1.102:57043 | 192.168.1.1:1244 | TCP:S |

The captured events demonstrate a classic horizontal reconnaissance pattern, where a single source system rapidly probes multiple destination ports across a short time interval.

---

## Key Technical Skills Demonstrated

### Perimeter Defense & Firewall Administration
- Designed and administered a segmented pfSense firewall environment.
- Implemented and validated stateful firewall rules and traffic controls.
- Configured logging to improve network visibility and auditability.

### Identity & Access Management (IAM)
- Provisioned Active Directory user accounts and organizational structures.
- Applied least-privilege administrative separation between privileged and standard identities.
- Implemented Fine-Grained Password Policies (FGPP) to enforce stronger credential requirements on administrative accounts.

### Advanced Systems Troubleshooting
- Diagnosed Linux networking failures using command-line utilities.
- Restored connectivity through manual IP addressing and route manipulation using `iproute2`.
- Recovered services by restarting critical networking components.

### Security Event & Log Analysis
- Conducted reconnaissance testing using Nmap.
- Validated firewall detection and logging capabilities.
- Analyzed network traffic patterns to identify scanning activity and potential security events.
