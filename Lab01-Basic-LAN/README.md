## Lab 01: Basic LAN Configuration & Connectivity Test

### 📌 Overview
Configured a basic Local Area Network (LAN) using a Cisco 2960 Switch and two host PCs to establish and verify fundamental Layer 2 and Layer 3 end-to-end connectivity within the same IP subnet.

### 🌐 Topology & IP Scheme
- **Switch:** Cisco Catalyst 2960 (`SW1`)
- **Host PCs:**
  - `PC0`: IP `192.168.1.10/24` connected to interface `FastEthernet 0/1`
  - `PC1`: IP `192.168.1.20/24` connected to interface `FastEthernet 0/2`

### 🛠️ Key CLI Configurations

#### Switch Basic Setup (SW1)
! Access Privileged EXEC Mode & Global Config
enable
configure terminal

! Assign Hostname
hostname SW1

! Save Running Configuration
exit
copy running-config startup-config

### ✅ Verification & Validation Tests
- **Physical Link Verification:** Confirmed link status LEDs are green on interfaces `Fa0/1` and `Fa0/2`.
- **End-to-End Connectivity Test (PC0 to PC1):** 
  Executed `ping 192.168.1.20` from `PC0` Command Prompt -> **SUCCESS** (`Reply from 192.168.1.20`).
- **MAC Address Table Verification (`show mac address-table`):** Confirmed `SW1` successfully learned the dynamic MAC addresses of `PC0` and `PC1` on their respective ports.
-
