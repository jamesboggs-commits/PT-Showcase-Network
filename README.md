# Enterprise County Courthouse Network Infrastructure

## 📌 Project Overview
This project simulates a secure, three-tier enterprise network for a multi-floor county courthouse using Cisco Packet Tracer. \
It demonstrates hierarchical network architecture, network segmentation, dynamic routing, centralized access, authorization, and accounting (AAA), quality of service, link aggregation, NAT, DHCP, DNS, and basic network device security.


### Key Features & Technologies
* **Architecture:** Three-tier (Edge, Core, Distribution/Access)
* **Routing Protocol:** OSPF, HSRP
* **Segmentation:** 15+ Departmental VLANs with VLSM subnetting
* **Services:** Centralized DHCP , DNS, AAA/RADIUS Authentication, Web Server
* **Security & Control:** Port security, Native VLAN isolation, Passive interfaces, SSH management, SNMP, LLDP, STP

---

## 📐 Network Topology
![Logical Topology](docs/Logical_Topology.png)

### Device Roles
* **Edge Router / Firewall:** Handles WAN handoff to ISP, NAT, and outer firewall boundary.
* **`L3Sw-Core`:** Acts as the backbone, facilitating inter-floor traffic and server connections.
* **`L3Sw-2` (Floor 1), `L3Sw-3` (Floor 2), `L3Sw-4` (Floor 3):** Multilayer switches providing local inter-VLAN routing for departmental offices.
* **`L2Sw-1` (Basement):** Layer 2 switch servicing the Office of Emergency Management

---

## 📊 VLAN & IP Subnetting Scheme

| VLAN ID | Department           | Subnet Address | Mask | CIDR | Usable Hosts | Usable Host Range | Default Gateway |
| :---:  | :---                  | :---           | :--- | :---: | :---:       | :---              | :--- |
| **10** | Emergency Management  | 10.10.10.0 | 255.255.255.224 | /27 | 30 | 10.10.10.1 - 10.10.10.30 | 10.10.10.1 |
| **20** | Assessor's Office     | 10.10.20.0 | 255.255.255.224 | /27 | 30 | 10.10.20.1 - 10.10.20.30 | 10.10.20.1 |
| **21** | Collector's Office    | 10.10.21.0 | 255.255.255.224 | /27 | 30 | 10.10.21.1 - 10.10.21.30 | 10.10.21.1 |
| **22** | Coroner's Office      | 10.10.22.0 | 255.255.255.240 | /28 | 14 | 10.10.22.1 - 10.10.22.14 | 10.10.22.1 |
| **23** | County Clerk's Office | 10.10.23.0 | 255.255.255.224 | /27 | 30 | 10.10.23.1 - 10.10.23.30 | 10.10.23.1 |
| **24** | Janitorial            | 10.10.24.0 | 255.255.255.240 | /28 | 14 | 10.10.24.1 - 10.10.24.14 | 10.10.24.1 |
| **30** | Circuit Clerk         | 10.10.30.0 | 255.255.255.224 | /27 | 30 | 10.10.30.1 - 10.10.30.30 | 10.10.30.1 |
| **31** | Juvenile Office       | 10.10.31.0 | 255.255.255.224 | /27 | 30 | 10.10.31.1 - 10.10.31.30 | 10.10.31.1 |
| **32** | Public Administrator  | 10.10.32.0 | 255.255.255.240 | /28 | 14 | 10.10.32.1 - 10.10.32.14 | 10.10.32.1 |
| **40** | Judge Chambers        | 10.10.40.0 | 255.255.255.240 | /28 | 14 | 10.10.40.1 - 10.10.40.14 | 10.10.40.1 |
| **41** | Judge Secretaries     | 10.10.41.0 | 255.255.255.240 | /28 | 14 | 10.10.41.1 - 10.10.41.14 | 10.10.41.1 |
| **42** | Courtrooms            | 10.10.42.0 | 255.255.255.224 | /27 | 30 | 10.10.42.1 - 10.10.42.30 | 10.10.42.1 |
| **43** | Prosecuting Attorney  | 10.10.43.0 | 255.255.255.224 | /27 | 30 | 10.10.43.1 - 10.10.43.30 | 10.10.43.1 |
| **50** | Servers               | 10.10.50.0 | 255.255.255.224 | /27 | 30 | 10.10.50.1 - 10.10.50.30 | 10.10.50.1 |
| **60** | Guest Wi-Fi           | 192.168.0.0 | 255.255.254.0  | /23 | 510 | 192.168.0.1 - 192.168.1.254 | 192.168.0.1 |
| **97** | Printers              | 10.10.97.0 | 255.255.255.224 | /27 | 30 | 10.10.97.1 - 10.10.97.30 | 10.10.97.1 |
| **98** | Voice VLAN (VoIP)     | 10.10.98.0 | 255.255.255.0   | /24 | 254 | 10.10.98.1 - 10.10.98.254 | 10.10.98.1 |
| **99** | Management VLAN       | 10.10.99.0 | 255.255.255.224 | /27 | 30 | 10.10.99.1 - 10.10.99.30 | 10.10.99.1 |

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
