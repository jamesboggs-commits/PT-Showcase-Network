# PT-Showcase-Network
A three-tier, enterprise network in the setting of a county courthouse. \
Concepts covered - device security, network segmentation, OSPF routing, QoS, Windows Active Directory, DNS, DHCP, AAA
# Enterprise County Courthouse Network Infrastructure

## 📌 Project Overview
This project simulates a secure, three-tier enterprise network for a multi-story county courthouse using Cisco Packet Tracer. It demonstrates hierarchical network architecture, VLAN segmentation, dynamic intra-building routing via OSPF, centralized identity & core infrastructure services, and end-to-end device security.

### Key Features & Technologies
* **Architecture:** Three-Tier (Core, Distribution/Access, Edge)
* **Routing Protocol:** OSPF Area 0 (Single Area)
* **Segmentation:** 15+ Departmental VLANs with VLSM Subnetting
* **Services:** Centralized DHCP (with IP Helper), DNS, AAA/RADIUS Authentication, Web Server
* **Security & Control:** Port Security, SSH Management, Native VLAN Isolation, Passive Interfaces

---

## 📐 Network Topology
![Logical Topology](docs/logical-topology.png)

### Device Roles
* **Edge Router / Firewall:** Handles WAN handoff to ISP, NAT, and outer firewall boundary.
* **`L3Sw-Core`:** Acts as the high-speed backbone, aggregating inter-floor traffic and server farm connections.
* **`L3Sw-2` (Floor 1), `L3Sw-4` (Floor 2), `L3Sw-3` (Floor 3):** Multilayer switches providing local inter-VLAN routing for departmental offices.
* **`L2Sw-1` (Annex):** Layer 2 switch servicing Emergency Management and outbuilding operations.

---

## 📊 VLAN & IP Subnetting Scheme

| VLAN ID | Subnet Name / Department | Network Address | Subnet Mask | Usable IP Range | Default Gateway |
|:-------:|:-------------------------|:----------------|:-----------:|:----------------|:----------------|
| **10**  | Emergency Management     | 10.10.0.128/28  | 255.255.255.240 | 10.10.0.129 - 10.10.0.142 | 10.10.0.129 |
| **20**  | Assessor's Office        | 10.10.0.0/29    | 255.255.255.248 | 10.10.0.1 - 10.10.0.6     | 10.10.0.1   |
| **21**  | Collector's Office       | 10.10.0.8/29    | 255.255.255.248 | 10.10.0.9 - 10.10.0.14    | 10.10.0.9   |
| **30**  | Circuit Clerk            | 10.10.0.32/27   | 255.255.255.224 | 10.10.0.33 - 10.10.0.62   | 10.10.0.33  |
| **40**  | Judge's Chambers         | 10.10.0.88/29   | 255.255.255.248 | 10.10.0.89 - 10.10.0.94   | 10.10.0.89  |
| **50**  | Servers                  | 10.10.0.224/27  | 255.255.255.224 | 10.10.0.225 - 10.10.0.254 | 10.10.0.225 |
| **60**  | Guest Wi-Fi              | 192.168.0.0/23  | 255.255.254.0   | 192.168.0.1 - 192.168.1.254 | 192.168.0.1 |
| **98**  | Voice VLAN               | 10.10.1.0/25    | 255.255.255.128 | 10.10.1.1 - 10.10.1.126   | 10.10.1.1   |
| **99**  | Management VLAN          | 10.10.0.160/27  | 255.255.255.224 | 10.10.0.161 - 10.10.0.190 | 10.10.0.161 |

---

## 🛠️ Configuration & Implementation Highlights

### 1. Dynamic Routing (OSPF Area 0)
OSPF is enabled across all Layer 3 switches and the Edge Router to allow automatic route convergence.
```ciscolike
router ospf 1
 router-id 1.1.1.1
 log-adjacency-changes
 passive-interface default
 no passive-interface GigabitEthernet0/0/1
 network 10.10.0.0 0.0.255.255 area 0
