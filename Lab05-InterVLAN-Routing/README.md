## Lab 05: Inter-VLAN Routing (Router-on-a-Stick)

### 📌 Overview
Configured Router-on-a-Stick (ROAS) to enable Layer 3 communication between isolated VLANs. Utilized 802.1Q encapsulation on subinterfaces of a single Cisco Router interface connected to a Cisco Switch trunk port.

### 🌐 Topology & IP Scheme
- **Router:** `R1` connected via `Gig0/0/0` to Switch `SW1` (`Gig0/1`).
- **VLAN 10 (Sales):** `192.168.10.0/24` (Gateway: `192.168.10.1`)
  - `PC0` (SW1 `Fa0/1`): `192.168.10.10/24`
- **VLAN 20 (HR):** `192.168.20.0/24` (Gateway: `192.168.20.1`)
  - `PC1` (SW1 `Fa0/2`): `192.168.20.10/24`

### 🛠️ Key CLI Configurations

#### Switch Trunk Port Setup (SW1)
enable
configure terminal
vlan 10
 name Sales
vlan 20
 name HR
exit
interface FastEthernet0/1
 switchport mode access
 switchport access vlan 10
exit
interface FastEthernet0/2
 switchport mode access
 switchport access vlan 20
exit
interface GigabitEthernet0/1
 switchport mode trunk
exit

#### Router Subinterface Setup (R1)
enable
configure terminal
interface GigabitEthernet0/0/0
 no shutdown
exit
interface GigabitEthernet0/0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0
exit
interface GigabitEthernet0/0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
exit

### ✅ Verification & Validation Tests
- **Inter-VLAN Connectivity Test:** Executed `ping 192.168.20.10` from `PC0` (VLAN 10 to VLAN 20) -> **SUCCESS**.
- Executed `show ip route` on `R1` to confirm directly connected subinterface networks.
- Executed `show interface trunk` on `SW1` to verify active trunking.
-
