Lab08-Layer3-EtherChannel-LACP/README.md

# Lab 08: Layer 3 EtherChannel Configuration (LACP)

## Overview
This laboratory project demonstrates the configuration and verification of a **Layer 3 (Routed) EtherChannel** using the **Link Aggregation Control Protocol (LACP)** between two Cisco Catalyst 3650 Multilayer Switches in Cisco Packet Tracer. 

By binding multiple physical interfaces into a single logical `Port-channel` interface at the Network Layer (Layer 3), we achieve higher bandwidth aggregation, link redundancy, and fast failover without relying on Spanning Tree Protocol (STP) blocking mechanisms.

---

## Topology & Requirements
* **Devices:** 2x Cisco Catalyst 3650 Multilayer Switches (`MLS1`, `MLS2`)
* **Physical Connections:**
  * `GigabitEthernet1/0/1` <---> `GigabitEthernet1/0/1`
  * `GigabitEthernet1/0/2` <---> `GigabitEthernet1/0/2`
* **Protocol:** LACP (`mode active`)
* **Subnet:** `10.10.10.0/24`
  * **MLS1 (Port-channel 1):** `10.10.10.1/24`
  * **MLS2 (Port-channel 1):** `10.10.10.2/24`

---

## Configuration Steps

### 1. Configure Multilayer Switch 1 (MLS1)
Switch> enable
Switch# configure terminal
Switch(config)# hostname MLS1
MLS1(config)# ip routing

! Convert physical ports to Layer 3 routed interfaces and bind to LACP
MLS1(config)# interface range gigabitEthernet 1/0/1 - 2
MLS1(config-if-range)# no switchport
MLS1(config-if-range)# channel-group 1 mode active
MLS1(config-if-range)# exit

! Assign IP address to logical Port-channel interface
MLS1(config)# interface port-channel 1
MLS1(config-if)# ip address 10.10.10.1 255.255.255.0
MLS1(config-if)# exit
MLS1(config)# end
MLS1# write

### 2. Configure Multilayer Switch 2 (MLS2)
Switch> enable
Switch# configure terminal
Switch(config)# hostname MLS2
MLS2(config)# ip routing

! Convert physical ports to Layer 3 routed interfaces and bind to LACP
MLS2(config)# interface range gigabitEthernet 1/0/1 - 2
MLS2(config-if-range)# no switchport
MLS2(config-if-range)# channel-group 1 mode active
MLS2(config-if-range)# exit

! Assign IP address to logical Port-channel interface
MLS2(config)# interface port-channel 1
MLS2(config-if)# ip address 10.10.10.2 255.255.255.0
MLS2(config-if)# exit
MLS2(config)# end
MLS2# write

---

## Verification & Troubleshooting Commands

### 1. Verify EtherChannel Summary Status
Execute the following command on either switch to check channel integrity:
MLS2# show etherchannel summary

Expected Output Flags:
* `Po1(RU)`: R = Layer 3 (Routed), U = In Use.
* `Gig1/0/1(P)`, `Gig1/0/2(P)`: P = Bundled in Port-channel.

### 2. Test End-to-End L3 Connectivity
Verify direct ICMP reachability across the bundled link:
MLS2# ping 10.10.10.1

Result: Success rate is 80 to 100 percent (5/5).

---

## Key Takeaways & CCNA Concepts
* **`no switchport` Command:** Disables Layer 2 switching capabilities on physical member interfaces, turning them into dedicated Layer 3 interfaces before joining a routed EtherChannel.
* **LACP Bundling (`mode active`):** Enables IEEE 802.3ad negotiation actively on both sides to establish a dynamic link aggregation group.
* **Routing Activation (`ip routing`):** Enables global IP routing engine capabilities on Cisco Multilayer Switches.
*
