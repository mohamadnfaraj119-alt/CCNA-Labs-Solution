## Lab 04: VLANs & 802.1Q Trunking Configuration

### 📌 Overview
Configured logical network segmentation using VLANs and established an 802.1Q trunk link between two Cisco Catalyst 2960 switches to allow same-VLAN traffic across switches without a router.

### 🌐 Topology & IP Scheme
- **Switches:** `SW1` and `SW2` connected via inter-switch trunk link (`Gig0/1`).
- **VLAN 10 (Sales):** `192.168.10.0/24`
  - `PC0` (SW1 `Fa0/1`): `192.168.10.10/24`
  - `PC2` (SW2 `Fa0/1`): `192.168.10.20/24`
- **VLAN 20 (HR):** `192.168.20.0/24`
  - `PC1` (SW1 `Fa0/2`): `192.168.20.10/24`
  - `PC3` (SW2 `Fa0/2`): `192.168.20.20/24`

### 🛠️ Key CLI Configurations

#### SW1 & SW2 VLAN Setup
enable
configure terminal
vlan 10
 name Sales
vlan 20
 name HR
exit

#### Access Ports Assignment (SW1 & SW2)
interface FastEthernet0/1
 switchport mode access
 switchport access vlan 10
exit
interface FastEthernet0/2
 switchport mode access
 switchport access vlan 20
exit

#### Trunk Port Setup (SW1 & SW2)
interface GigabitEthernet0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20
exit

### ✅ Verification & Validation Tests
- **Intra-VLAN Test:** `ping 192.168.10.20` from `PC0` (VLAN 10) -> **SUCCESS**.
- **Inter-VLAN Isolation Test:** `ping 192.168.20.10` from `PC0` (VLAN 10 to VLAN 20) -> **FAILED** (as expected without Layer 3 routing).
- Executed `show vlan brief` and `show interfaces trunk` to confirm VLAN assignments and trunk status.
-
