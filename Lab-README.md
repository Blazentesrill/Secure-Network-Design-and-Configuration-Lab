# Lab 5: Secure Network Design and Configuration

## Overview
Designed and configured a security-hardened institutional network in Cisco Packet Tracer, applying defense-in-depth principles: network segmentation with VLANs, a DMZ for public-facing services, DHCP for internal address management, NAT/PAT for outbound traffic, and ACL-based firewall rules enforcing least-privilege access between zones.

## Tools Used
- Cisco Packet Tracer
- Cisco ISR4331 Routers (Core Router, ISP Router)
- Cisco 3650-24PS Multilayer Switches
- PT Network Controller
- PC, Laptop, and Server end hosts

## Network Architecture

```
Internet / ISP
      │
  [ISP Router] ── [Web+DNS Server]  ← 200.z.2.0/24
      │
  [Core Router]  ← NAT/PAT boundary, ACL firewall
   /  |  |  \
Sw10 Sw20 Sw30 Sw40
 │    │    │    │
Exec Emp Guest DMZ+NetMgmt
VLAN VLAN VLAN  (static IPs)
```

Internal space: `10.z.0.0/16` | Public IP: `200.z.1.0/30`

## What I Did

### 1. Network Segmentation
- Created four internal zones: Executive, Employee, Guest, and DMZ/Network Management
- Assigned each zone a dedicated VLAN and subnet
- Configured trunk links from switches to the Core Router for inter-VLAN routing

### 2. DHCP, DNS, and HTTP Services
- Configured a centralized DHCP server to serve all three dynamic zones (Executive, Employee, Guest) with appropriate scope per VLAN
- Set up DNS and HTTP services on the Web+DNS Server in the ISP zone
- Verified all internal hosts resolve names and reach the web server

### 3. NAT and PAT
- Configured dynamic NAT/PAT on the Core Router so all internal hosts can reach external destinations using the organization's single public IP
- Configured PAT (port forwarding) on the Core Router to allow external clients to reach the Web Server and DNS Server directly by public IP, while keeping all other internal devices unreachable from outside

### 4. Network Controller
- Connected the PT Network Controller to the management zone
- Configured it to scan and learn the full network topology, asset inventory, and link status

### 5. ACL Firewall Rules
Implemented access control lists on the Core Router enforcing the following policy:

| Zone              | Outbound to Internet | Internal Access                              |
|-------------------|----------------------|----------------------------------------------|
| Executive         | ✅ Allowed           | All zones except Network Management          |
| Employee          | ✅ Allowed           | All zones except Network Management          |
| Guest             | ✅ Allowed           | Guest zone and DMZ only                      |
| DMZ               | ❌ Blocked           | Can only respond to inbound client requests  |
| Network Mgmt      | ✅ Unrestricted      | Full access, no restrictions                 |

- DHCP traffic exempted from Guest zone restrictions to allow address assignment
- ACLs applied inbound on each VLAN subinterface of the Core Router

### 6. Attack Surface Analysis
Documented the remaining attack surface after firewall configuration and proposed mitigations:
- DMZ servers are publicly reachable — mitigated by ensuring they cannot initiate outbound connections
- Guest zone could pivot to DMZ — mitigated by restricting Guest to DMZ read-only (HTTP/DNS)
- Core Router is a single point of failure — redundancy and rate limiting recommended in production
- Network Management zone has full access — physical and logical access controls critical

## Key Concepts Demonstrated
- Defense-in-depth and zone-based segmentation
- VLAN design mapped to security policy
- Centralized DHCP with per-VLAN scopes
- NAT overload (PAT) with selective port forwarding
- ACL design for least-privilege inter-zone traffic control
- DMZ architecture separating public services from internal network
- Network controller for topology visibility and policy enforcement

## Files
- `lab5.pkt` — Packet Tracer file with completed secure network configuration
