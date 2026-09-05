# LAB 06: INTER-VLAN ROUTING VIA LAYER 3 SWITCH (SVI)

## [LAB SCENARIO]
- **The Problem:** In Lab 05, Inter-VLAN Routing was configured using the Router-on-a-Stick (ROAS) topology. While ROAS works well for small networks, it introduces a severe bottleneck because all inter-VLAN traffic must flow through a single physical interface and trunk link to an external router. In enterprise environments, this single point of congestion severely degrades network performance and throughput.
- **The Solution & Goal:** Replace the external router with a Layer 3 Switch (Multilayer Switch). A Layer 3 Switch combines high-speed hardware switching with wire-speed Layer 3 IP routing capabilities. By creating Switched Virtual Interfaces (SVIs) inside the switch, the Multilayer Switch acts as the default gateway for each VLAN and routes traffic between VLAN 10 (Sales) and VLAN 20 (HR) directly in hardware (ASIC level), completely eliminating the need for an external router and preventing traffic bottlenecks.

---

## [TOPOLOGY & IP ADDRESSING]
- **PC0 (Sales):** IP: `192.168.10.10` | Subnet Mask: `255.255.255.0` | Default Gateway: `192.168.10.1` | VLAN 10
- **PC1 (HR):** IP: `192.168.20.10` | Subnet Mask: `255.255.255.0` | Default Gateway: `192.168.20.1` | VLAN 20
- **MLS1 (3560 Multilayer Switch):**
  - Port `Fa0/1` connected to `PC0` (Access VLAN 10)
  - Port `Fa0/2` connected to `PC1` (Access VLAN 20)
  - `SVI Interface VLAN 10`: `192.168.10.1 /24`
  - `SVI Interface VLAN 20`: `192.168.20.1 /24`

---

## [STEP-BY-STEP CONFIGURATION]

### Step 1: Initial Switch Setup, VLAN Creation, and Port Assignment

enable
   └─ [ Description ]: Navigate to Privileged EXEC Mode.

configure terminal
   └─ [ Description ]: Enter Global Configuration Mode.

hostname MLS1
   └─ [ Description ]: Set the hostname of the Multilayer Switch to MLS1.

vlan 10
   └─ [ Description ]: Create VLAN 10.

name Sales
   └─ [ Description ]: Name VLAN 10 as "Sales".

exit
   └─ [ Description ]: Return to Global Configuration Mode.

vlan 20
   └─ [ Description ]: Create VLAN 20.

name HR
   └─ [ Description ]: Name VLAN 20 as "HR".

exit
   └─ [ Description ]: Return to Global Configuration Mode.

interface fastEthernet 0/1
   └─ [ Description ]: Enter configuration mode for interface Fa0/1.

switchport mode access
   └─ [ Description ]: Set the interface operational mode to static access.

switchport access vlan 10
   └─ [ Description ]: Assign interface Fa0/1 to VLAN 10.

exit
   └─ [ Description ]: Return to Global Configuration Mode.

interface fastEthernet 0/2
   └─ [ Description ]: Enter configuration mode for interface Fa0/2.

switchport mode access
   └─ [ Description ]: Set the interface operational mode to static access.

switchport access vlan 20
   └─ [ Description ]: Assign interface Fa0/2 to VLAN 20.

exit
   └─ [ Description ]: Return to Global Configuration Mode.

---

### Step 2: Creating Switched Virtual Interfaces (SVIs) and Enabling IP Routing

interface vlan 10
   └─ [ Description ]: Create and enter the Switched Virtual Interface (SVI) for VLAN 10.

ip address 192.168.10.1 255.255.255.0
   └─ [ Description ]: Assign an IP address to act as the Default Gateway for VLAN 10 devices.

no shutdown
   └─ [ Description ]: Administratively enable the SVI interface.

exit
   └─ [ Description ]: Return to Global Configuration Mode.

interface vlan 20
   └─ [ Description ]: Create and enter the Switched Virtual Interface (SVI) for VLAN 20.

ip address 192.168.20.1 255.255.255.0
   └─ [ Description ]: Assign an IP address to act as the Default Gateway for VLAN 20 devices.

no shutdown
   └─ [ Description ]: Administratively enable the SVI interface.

exit
   └─ [ Description ]: Return to Global Configuration Mode.

ip routing
   └─ [ Description ]: Enable Layer 3 IP routing on the Multilayer Switch to allow packet forwarding between SVIs.

---

### Step 3: Verification and Saving Configuration

do show ip interface brief
   └─ [ Description ]: Verify that SVI VLAN 10 and VLAN 20 are in an "up/up" status with correct IP addresses.

do show ip route
   └─ [ Description ]: Display the IP routing table to verify connected routes (C) for 192.168.10.0/24 and 192.168.20.0/24.

end
   └─ [ Description ]: Return to Privileged EXEC Mode.

copy running-config startup-config
   └─ [ Description ]: Save active configuration to NVRAM.

---

## [VERIFICATION & PING TEST]
1. Open the Command Prompt on **PC0** (`192.168.10.10`).
2. Execute the ICMP Ping command to test connectivity to **PC1** in VLAN 20:
   `ping 192.168.20.10`
3. **Expected Result:** Ping is successful with 0% packet loss (Replies received from `192.168.20.10`). Traffic travels from PC0 to interface Fa0/1, gets routed internally via the Layer 3 Switch engine between SVI 10 and SVI 20 at wire-speed, and is forwarded out of Fa0/2 directly to PC1 without leaving the switch.
٤.
