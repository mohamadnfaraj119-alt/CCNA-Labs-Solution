# CCNA 200-301 Practical Labs

## Lab 01: Basic LAN Configuration & Connectivity Test
- **Topology**: 2 PCs connected to a Cisco 2960 Switch.
- **Goal**: Configure IPv4 addresses and verify end-to-end connectivity using `ping`.
- **IP Scheme**:
  - PC0: `192.168.1.10/24`
  - PC1: `192.168.1.20/24`
- **Result**: Successful reply received on ping execution.

---

## Lab 02: Switch Basic Configuration & Security
- **Topology**: Cisco 2960 Switch.
- **Goal**: Configure device identity, secure CLI access modes, enable encryption, and save running configuration to NVRAM.
- **Key Configurations Implemented**:
  - `hostname SW-Access1`
  - `enable secret cisco123`
  - `line console 0` -> `password adminpass` & `login`
  - `service password-encryption`
  - `banner motd # Authorized Access Only! #`
  - `copy running-config startup-config`
- **Validation**:
  - Console access successfully requested `adminpass`.
  - Privileged mode successfully restricted via `cisco123`.
  - Running configuration verified with encrypted password hashes.


---

## Lab 03: Basic Router Configuration & Inter-Network Routing

### 📌 Overview
Configured a Cisco Router (4331/1941) to enable routing between two distinct IP subnets, acting as default gateways for local hosts. Integrated management security and verified end-to-end connectivity.

### 🌐 Topology & IP Scheme
- **Router (R1-Core)**:
  - `g0/0/0`: `192.168.10.1/24` (Gateway for Subnet A)
  - `g0/0/1`: `192.168.20.1/24` (Gateway for Subnet B)
- **PC0**: IP `192.168.10.10/24` | Default Gateway: `192.168.10.1`
- **PC1**: IP `192.168.20.10/24` | Default Gateway: `192.168.20.1`

### 🛠️ Key CLI Configurations
```cisco
! Assign Hostname
enable
configure terminal
hostname R1-Core

! Configure Gateway Interface for Subnet A
interface gigabitEthernet 0/0/0
 ip address 192.168.10.1 255.255.255.0
 no shutdown
 exit

! Configure Gateway Interface for Subnet B
interface gigabitEthernet 0/0/1
 ip address 192.168.20.1 255.255.255.0
 no shutdown
 exit

! Security & Persistence
enable secret cisco123
exit
copy running-config startup-config

## Lab 04: Cisco Switch VLANs & Trunking Configuration

### 📌 Overview
Configured two Cisco Switches (`SW1` & `SW2`) with VLAN segmentation (`VLAN 10 Sales` and `VLAN 20 IT`) and established an `802.1Q Trunk` link on interface `Gi0/1` to allow cross-switch inter-VLAN communication while maintaining Layer 2 isolation.

### 🌐 Topology & IP Scheme
- **SW1 Connections:**
  - `Fa0/1`: PC0 | IP: `192.168.10.10/24` | Access `VLAN 10` (Sales)
  - `Fa0/2`: PC1 | IP: `192.168.10.20/24` | Access `VLAN 20` (IT)
- **SW2 Connections:**
  - `Fa0/1`: PC2 | IP: `192.168.10.11/24` | Access `VLAN 10` (Sales)
  - `Fa0/2`: PC3 | IP: `192.168.10.21/24` | Access `VLAN 20` (IT)
- **Trunk Link (Backbone):**
  - `SW1 Gi0/1` <---> `SW2 Gi0/1` (IEEE 802.1Q Trunk)

### 🛠️ Key CLI Configurations

#### Switch 1 (SW1)
! Assign Hostname
enable
configure terminal
hostname SW1

! Create and Name VLANs
vlan 10
 name Sales
exit
vlan 20
 name IT
exit

! Configure Access Ports
interface FastEthernet 0/1
 switchport mode access
 switchport access vlan 10
exit

interface FastEthernet 0/2
 switchport mode access
 switchport access vlan 20
exit

! Configure Trunk Port
interface GigabitEthernet 0/1
 switchport mode trunk
exit

#### Switch 2 (SW2)
! Assign Hostname
enable
configure terminal
hostname SW2

! Create and Name VLANs
vlan 10
 name Sales
exit
vlan 20
 name IT
exit

! Configure Access Ports
interface FastEthernet 0/1
 switchport mode access
 switchport access vlan 10
exit

interface FastEthernet 0/2
 switchport mode access
 switchport access vlan 20
exit

! Configure Trunk Port
interface GigabitEthernet 0/1
 switchport mode trunk
exit

### ✅ Verification & Validation Tests
- **VLAN Database Verification (`show vlan brief`):** Verified `VLAN 10` (Sales) and `VLAN 20` (IT) are active on both switches with correct port assignments (`Fa0/1` & `Fa0/2`).
- **Intra-VLAN Connectivity Test (PC0 to PC2):** `ping 192.168.10.11` -> **SUCCESS** (`Reply from 192.168.10.11`). Confirmed Trunk port successfully forwards tagged frames across switches for the same VLAN.
- **Inter-VLAN Isolation Test (PC0 to PC1):** `ping 192.168.10.20` -> **FAILED** (`Request timed out`). Confirmed strict Layer 2 isolation between different VLANs without a router.
-
