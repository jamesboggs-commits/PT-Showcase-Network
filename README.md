# Enterprise County Courthouse Network Infrastructure

## 📌 Project Overview
This project simulates a secure, three-tier enterprise network for a multi-floor county courthouse using Cisco Packet Tracer. \
It demonstrates hierarchical network architecture, network segmentation, dynamic routing, Centralized AAA, QoS, EtherChannel, and basic network device security.


### Key Features & Technologies
* **Architecture:** Three-Tier (Edge, Core, Distribution/Access)
* **Routing Protocol:** OSPF Area 0 (Single Area)
* **Segmentation:** 15+ Departmental VLANs with VLSM Subnetting
* **Services:** Centralized DHCP , DNS, AAA/RADIUS Authentication, Web Server
* **Security & Control:** Port Security, SSH Management, Native VLAN Isolation, Passive Interfaces

---

## 📐 Network Topology
![Logical Topology](docs/Logical_Topology.png)
This image is subject to change to a screenshot of the PT file. There is one L2 switch per office space in the new topology.
### Device Roles
* **Edge Router / Firewall:** Handles WAN handoff to ISP, NAT, and outer firewall boundary.
* **`L3Sw-Core`:** Acts as the backbone, facilitating inter-floor traffic and server connections.
* **`L3Sw-2` (Floor 1), `L3Sw-3` (Floor 2), `L3Sw-4` (Floor 3):** Multilayer switches providing local inter-VLAN routing for departmental offices.
* **`L2Sw-1` (Basement):** Layer 2 switch servicing the Office of Emergency Management

---

## 📊 VLAN & IP Subnetting Scheme

### VLAN & VLSM Subnetting Scheme

### VLAN & VLSM Subnetting Scheme (Strictly Packed)

| VLAN ID | Department / Function | Subnet Address | Mask | CIDR | Usable Hosts | Usable Host Range | Default Gateway |
| :---: | :--- | :--- | :--- | :---: | :---: | :--- | :--- |
| **50** | Servers | 10.10.0.0 | 255.255.255.224 | /27 | 30 | 10.10.0.1 - 10.10.0.30 | 10.10.0.1 |
| **98** | Voice VLAN (VoIP) | 10.10.0.32 | 255.255.255.0 | /24 | 254 | 10.10.0.33 - 10.10.0.286 (x.254) | 10.10.0.33 |
| **60** | Guest Wi-Fi | 192.168.0.0 | 255.255.254.0 | /23 | 510 | 192.168.0.1 - 192.168.1.254 | 192.168.0.1 |
| **20** | Assessor's Office | 10.10.1.0 | 255.255.255.224 | /27 | 30 | 10.10.1.1 - 10.10.1.30 | 10.10.1.1 |
| **21** | Collector's Office | 10.10.1.32 | 255.255.255.224 | /27 | 30 | 10.10.1.33 - 10.10.1.62 | 10.10.1.33 |
| **23** | County Clerk's Office | 10.10.1.64 | 255.255.255.224 | /27 | 30 | 10.10.1.65 - 10.10.1.94 | 10.10.1.65 |
| **30** | Circuit Clerk | 10.10.1.96 | 255.255.255.224 | /27 | 30 | 10.10.1.97 - 10.10.1.126 | 10.10.1.97 |
| **31** | Juvenile Office | 10.10.1.128 | 255.255.255.224 | /27 | 30 | 10.10.1.129 - 10.10.1.158 | 10.10.1.129 |
| **42** | Courtrooms | 10.10.1.160 | 255.255.255.224 | /27 | 30 | 10.10.1.161 - 10.10.1.190 | 10.10.1.161 |
| **43** | Prosecuting Attorney | 10.10.1.192 | 255.255.255.224 | /27 | 30 | 10.10.1.193 - 10.10.1.222 | 10.10.1.193 |
| **97** | Printers | 10.10.1.224 | 255.255.255.224 | /27 | 30 | 10.10.1.225 - 10.10.1.254 | 10.10.1.225 |
| **10** | Emergency Management | 10.10.2.0 | 255.255.255.224 | /27 | 30 | 10.10.2.1 - 10.10.2.30 | 10.10.2.1 |
| **99** | Management VLAN | 10.10.2.32 | 255.255.255.224 | /27 | 30 | 10.10.2.33 - 10.10.2.62 | 10.10.2.33 |
| **22** | Coroner's Office | 10.10.2.64 | 255.255.255.240 | /28 | 14 | 10.10.2.65 - 10.10.2.78 | 10.10.2.65 |
| **24** | Facilities / Janitorial | 10.10.2.80 | 255.255.255.240 | /28 | 14 | 10.10.2.81 - 10.10.2.94 | 10.10.2.81 |
| **32** | Public Administrator | 10.10.2.96 | 255.255.255.240 | /28 | 14 | 10.10.2.97 - 10.10.2.110 | 10.10.2.97 |
| **40** | Judge Chambers | 10.10.2.112 | 255.255.255.240 | /28 | 14 | 10.10.2.113 - 10.10.2.126 | 10.10.2.113 |
| **41** | Judge Secretaries | 10.10.2.128 | 255.255.255.240 | /28 | 14 | 10.10.2.129 - 10.10.2.142 | 10.10.2.129 |
---

## 🛠️ Configuration & Implementation Highlights

### 1. Dynamic Routing (OSPF)
OSPF is enabled across all Layer 3 switches and the Edge Router to allow automatic route convergence.
```ciscolike
router ospf 1
 router-id 1.1.1.1
 log-adjacency-changes
 passive-interface default
 no passive-interface GigabitEthernet0/0/1
 network 10.10.0.0 0.0.255.255 area 0
```
### 2. DHCP Relay (`ip helper-address`)
Workstations on departmental VLANs obtain dynamic IP configurations from the Windows Server.
```ciscolike
interface Vlan20
 description Assessor_Office_SVI
 ip address 10.10.0.1 255.255.255.248
 ip helper-address 10.10.0.226
```
### 3. Security Implementation
* **Port Security:** Enabled on access ports across floor switches to restrict MAC address learning and prevent unauthorized device attachments.
* **Management Access:** Telnet is disabled globally in favor of encrypted SSH access over the Management VLAN (`VLAN 99`).
* **Guest Isolation:** Access Control Lists (ACLs) are deployed at the edge to ensure the **Guest Wi-Fi (VLAN 60)** has internet access only, completely blocking lateral movement into county department subnets and the Server Farm.

### 4. Link Aggregation (EtherChannel)
Port channels are configured between the floor Layer 3 switches and the Core switch using LACP (IEEE 802.3ad) to provide redundancy and aggregate bandwidth.
```ciscolike
interface range GigabitEthernet0/1 - 2
 channel-group 1 mode active
!
interface Port-channel 1
 switchport mode trunk
 description Core_Uplink_Aggregation
```
