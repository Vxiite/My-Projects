# Network Segmentation and WAN Routing Lab

## Project Overview

This project demonstrates the design and implementation of a segmented network environment using Cisco Packet Tracer. The lab simulates a corporate **Victim Network** and an isolated **Hacker Network**, connected through a WAN serial link. The primary objective was to establish end-to-end communication between two separate subnets using manually configured static routes.

## Network Topology

### Victim Network (192.168.10.0/24)
- Contains critical infrastructure including:
  - **DC01 (Domain Controller)**
  - **Logging Server**
- Connected to **Router0**

### Hacker Network (192.168.1.0/24)
- Contains an attacker workstation
- Connected to **Router1**

### WAN Link (10.0.0.0/30)
- Point-to-point serial connection between:
  - **Router0**
  - **Router1**

## Implementation Steps

### 1. Interface Configuration
Configured IP addresses on router serial and Gigabit Ethernet interfaces to establish Layer 3 connectivity across the network.

### 2. Host Configuration
Assigned static IPv4 addresses, subnet masks, and default gateways to all workstations and servers to ensure proper network communication and traffic routing.

### 3. Static Routing
Implemented bi-directional static routes on both routers to enable communication between non-adjacent networks across the WAN link.

**Router1**
ip route 192.168.10.0 255.255.255.0 10.0.0.1
ip route 192.168.1.0 255.255.255.0 10.0.0.2

## Skills Demonstrated

- Cisco Packet Tracer
- IPv4 Addressing
- Subnetting
- Static Routing
- WAN Connectivity
- Network Troubleshooting
- Routing Table Analysis
- Connectivity Testing
