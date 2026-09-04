## Lab 03: Inter-Network Static Routing Basics

### 📌 Overview
Configured static routing on Cisco routers to enable end-to-end Layer 3 communication across two distinct subnets.

### 🌐 Topology & IP Scheme
- **Router R1:** Interface `Fa0/0` (`192.168.1.1/24`), Interface `Se0/1/0` (`10.0.0.1/30`)
- **Router R2:** Interface `Fa0/0` (`192.168.2.1/24`), Interface `Se0/1/0` (`10.0.0.2/30`)
- **Subnet A (PC0):** `192.168.1.10/24` (Gateway: `192.168.1.1`)
- **Subnet B (PC1):** `192.168.2.10/24` (Gateway: `192.168.2.1`)

### 🛠️ Key CLI Configurations

#### R1 Configuration
enable
configure terminal
hostname R1
interface FastEthernet0/0
 ip address 192.168.1.1 255.255.255.0
 no shutdown
exit
interface Serial0/1/0
 ip address 10.0.0.1 255.255.255.252
 clock rate 64000
 no shutdown
exit
ip route 192.168.2.0 255.255.255.0 10.0.0.2

#### R2 Configuration
enable
configure terminal
hostname R2
interface FastEthernet0/0
 ip address 192.168.2.1 255.255.255.0
 no shutdown
exit
interface Serial0/1/0
 ip address 10.0.0.2 255.255.255.252
 no shutdown
exit
ip route 192.168.1.0 255.255.255.0 10.0.0.1

### ✅ Verification & Validation Tests
- Executed `show ip route` on R1 and R2 to verify static routes (`S`).
- Executed `ping 192.168.2.10` from `PC0` -> **SUCCESS**.
-
