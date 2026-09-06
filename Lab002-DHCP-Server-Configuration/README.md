# Lab 002: Configuring Cisco Router as a DHCP Server

## Overview
This laboratory project demonstrates the configuration and verification of a **Cisco Router (R1) functioning as a DHCP Server** in Cisco Packet Tracer. 

By eliminating the need for manual IP address configuration on client devices, this setup enables automated network parameters distribution (IP Address, Subnet Mask, Default Gateway, and DNS Server) across local network hosts, mitigating human error and preventing IP address conflicts.

---

## Topology & Requirements
* **Devices:** 
  * 1x Cisco 2911 Router (`R1`)
  * 1x Cisco 2960 Switch (`SW1`)
  * 3x PC Clients (`PC1`, `PC2`, `PC3`)
* **Physical Connections:**
  * `R1 GigabitEthernet0/0` <---> `SW1 GigabitEthernet0/1`
  * `SW1 FastEthernet0/1` <---> `PC1`
  * `SW1 FastEthernet0/2` <---> `PC2`
  * `SW1 FastEthernet0/3` <---> `PC3`
* **Subnet:** `192.168.10.0/24`
* **Reserved IP:** `192.168.10.1` (Default Gateway)

---

## Configuration Steps

### Configure Router 1 (R1)
R1> enable
R1# configure terminal
R1(config)# hostname R1

! Configure Default Gateway Interface
R1(config)# interface GigabitEthernet 0/0
R1(config-if)# ip address 192.168.10.1 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit

! Reserve IP Address from DHCP Pool
R1(config)# ip dhcp excluded-address 192.168.10.1

! Configure DHCP Pool Parameters
R1(config)# ip dhcp pool LAN_OFFICE
R1(dhcp-config)# network 192.168.10.0 255.255.255.0
R1(dhcp-config)# default-router 192.168.10.1
R1(dhcp-config)# dns-server 8.8.8.8
R1(dhcp-config)# exit
R1(config)# end
R1# write

---

## Verification & Troubleshooting Commands

### 1. Verify Active DHCP IP Bindings
Execute the following command on R1 to list all leased IP addresses and client MAC addresses:
R1# show ip dhcp binding

### 2. Verify Interface Status
Check the status of the Default Gateway interface:
R1# show ip interface brief

### 3. Client Verification (Packet Tracer Desktop)
1. Open `PC1`, navigate to **Desktop** -> **IP Configuration**.
2. Select **DHCP** and verify receipt of IP configuration (e.g., `192.168.10.2`).
3. Repeat for `PC2` and `PC3`.

---

## Key Takeaways & CCNA Concepts
* **`ip dhcp excluded-address`:** Crucial command executed prior to defining pools to prevent assigning static router/server IPs to DHCP clients, avoiding IP conflicts.
* **`default-router` & `dns-server`:** Defines essential network parameters delivered dynamically to end-hosts alongside the IP/Subnet mask.
* **Dynamic IP Assignment:** Enables scalable network operations where client devices automatically configure themselves upon joining the LAN.
*
