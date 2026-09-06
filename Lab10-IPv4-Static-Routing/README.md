# Lab 12: IPv4 Static Routing Configuration between Cisco Routers

## Overview
This laboratory project demonstrates the manual configuration, propagation, and verification of **IPv4 Static Routes** across two Cisco Integrated Services Routers (ISR 2911/1941) in Cisco Packet Tracer.

By default, routers only maintain routing information for directly connected networks. This lab establishes explicit static routing entries to enable end-to-end Layer 3 reachability between isolated end-user subnets (`192.168.10.0/24` and `192.168.20.0/24`) across a `/30` point-to-point transit link (`10.1.1.0/30`).

---

## Topology & Components
* **Core Routing Devices:**
  * 2x Cisco 2911 Routers (`R1` and `R2`)
* **Endpoints:**
  * 2x Generic PCs (`PC1` and `PC2`)
* **Addressing Scheme:**
  * **LAN 1 Subnet:** `192.168.10.0/24`
  * **LAN 2 Subnet:** `192.168.20.0/24`
  * **Transit WAN Subnet:** `10.1.1.0/30`
  * **PC1:** IP `192.168.10.10/24`, Default Gateway `192.168.10.1`
  * **PC2:** IP `192.168.20.10/24`, Default Gateway `192.168.20.1`
  * **R1 LAN Gateway (G0/0):** `192.168.10.1/24`
  * **R1 Transit Interface (G0/1):** `10.1.1.1/30`
  * **R2 Transit Interface (G0/1):** `10.1.1.2/30`
  * **R2 LAN Gateway (G0/0):** `192.168.20.1/24`

---

## Step-by-Step Configuration Parameters

### 1. Router R1 Configuration
* **Access Level:** Global Configuration Mode (`configure terminal`)
  * **Hostname Setup:** `hostname R1`
  * **LAN Interface Setup (G0/0):**
    * `interface GigabitEthernet0/0`
    * `ip address 192.168.10.1 255.255.255.0`
    * `no shutdown`
  * **WAN/Transit Interface Setup (G0/1):**
    * `interface GigabitEthernet0/1`
    * `ip address 10.1.1.1 255.255.255.252`
    * `no shutdown`
  * **Static Route Injection:**
    * `ip route 192.168.20.0 255.255.255.0 10.1.1.2`

### 2. Router R2 Configuration
* **Access Level:** Global Configuration Mode (`configure terminal`)
  * **Hostname Setup:** `hostname R2`
  * **LAN Interface Setup (G0/0):**
    * `interface GigabitEthernet0/0`
    * `ip address 192.168.20.1 255.255.255.0`
    * `no shutdown`
  * **WAN/Transit Interface Setup (G0/1):**
    * `interface GigabitEthernet0/1`
    * `ip address 10.1.1.2 255.255.255.252`
    * `no shutdown`
  * **Static Route Injection:**
    * `ip route 192.168.10.0 255.255.255.0 10.1.1.1`

---

## Verification & Troubleshooting Commands

### 1. Interface Operational Status Inspection
From the Privileged EXEC Mode of `R1` and `R2`, execute:
show ip interface brief

* **Expected Result:** Physical interfaces `G0/0` and `G0/1` display Status `up` and Protocol `up` with their assigned IPv4 addresses.

### 2. Routing Table Inspection
From the Privileged EXEC Mode of `R1`, execute:
show ip route

* **Expected Result:** Routing table displays static route entry denoted by code **`S`**:
  `S 192.168.20.0/24 [1/0] via 10.1.1.2`

### 3. End-to-End Connectivity Testing (Ping Test)
From `PC1` Command Prompt, execute:
ping 192.168.20.10

* **Expected Result:** **100% Success (0% packet loss)** — Validates full bidirectional ICMP reachability across disparate Layer 3 subnets via static next-hop forwarding.

---

## Key Takeaways & CCNA Concepts
* **Static Routing:** Manually configured routing entries explicitly defining the path packets must take to reach remote networks.
* **Next-Hop IPv4 Address:** The IP address of the adjacent router interface that receives and forwards traffic toward its destination.
* **Point-to-Point WAN Links:** Subnets configured with `/30` masks (`255.255.255.252`) to optimize IP address allocation on direct router-to-router connections.
* **Symmetrical Routing Requirement:** Bidirectional communication requires routing knowledge in both directions (forward path from R1 to R2, and return path from R2 to R1).
*
