# Lab 13: IPv4 Default Static Route Configuration

## Overview
This laboratory project demonstrates the configuration, verification, and operation of an **IPv4 Default Static Route (`0.0.0.0/0`)** on an Edge Router (`R1`) connected to a simulated Internet Service Provider (ISP) / Central HQ Router (`R2`) using Cisco Packet Tracer.

While explicit static routes require manual entries for every remote subnet, a default static route acts as a "Gateway of Last Resort." It instructs the router to forward any IPv4 packet with an unknown destination network IP address toward a specified next-hop address (`10.1.1.2`).

---

## Topology & Components
* **Core Routing Devices:**
  * 2x Cisco 2911 Routers (`R1` as Edge Router, `R2` as HQ/ISP Router)
* **Endpoints:**
  * 2x Generic PCs (`PC1` on Local LAN, `PC2` on Remote Network/Internet)
* **Addressing Scheme:**
  * **LAN 1 Subnet (R1 Site):** `192.168.10.0/24`
  * **LAN 2 Subnet (R2 Site):** `172.16.1.0/24`
  * **Transit WAN Subnet:** `10.1.1.0/30`
  * **PC1:** IP `192.168.10.10/24`, Default Gateway `192.168.10.1`
  * **PC2:** IP `172.16.1.10/24`, Default Gateway `172.16.1.1`
  * **R1 LAN Gateway (G0/0):** `192.168.10.1/24`
  * **R1 WAN Interface (G0/1):** `10.1.1.1/30`
  * **R2 WAN Interface (G0/1):** `10.1.1.2/30`
  * **R2 LAN Gateway (G0/0):** `172.16.1.1/24`

---

## Step-by-Step Configuration Parameters

### 1. Edge Router (R1) Configuration
* **Access Level:** Global Configuration Mode (`configure terminal`)
  * **Hostname Setup:** `hostname R1`
  * **LAN Interface Setup (G0/0):**
    * `interface GigabitEthernet0/0`
    * `ip address 192.168.10.1 255.255.255.0`
    * `no shutdown`
  * **WAN Interface Setup (G0/1):**
    * `interface GigabitEthernet0/1`
    * `ip address 10.1.1.1 255.255.255.252`
    * `no shutdown`
  * **Default Static Route Injection:**
    * `ip route 0.0.0.0 0.0.0.0 10.1.1.2`

### 2. HQ / ISP Router (R2) Configuration
* **Access Level:** Global Configuration Mode (`configure terminal`)
  * **Hostname Setup:** `hostname R2`
  * **LAN Interface Setup (G0/0):**
    * `interface GigabitEthernet0/0`
    * `ip address 172.16.1.1 255.255.255.0`
    * `no shutdown`
  * **WAN Interface Setup (G0/1):**
    * `interface GigabitEthernet0/1`
    * `ip address 10.1.1.2 255.255.255.252`
    * `no shutdown`
  * **Return Static Route Setup:**
    * `ip route 192.168.10.0 255.255.255.0 10.1.1.1`

---

## Verification & Troubleshooting Commands

### 1. Routing Table & Gateway of Last Resort Verification
From the Privileged EXEC Mode of `R1`, execute:
show ip route

* **Expected Result:** Output displays `Gateway of last resort is 10.1.1.2 to network 0.0.0.0` along with candidate default entry denoted by **`S*`**:
  `S*  0.0.0.0/0 [1/0] via 10.1.1.2`

### 2. End-to-End Connectivity Testing (Ping Test)
From `PC1` Command Prompt, execute:
ping 172.16.1.10

* **Expected Result:** **100% Success (0% packet loss)** — Validates that unmapped destinations on `R1` are successfully forwarded using the default static route to `R2` and replies are routed back.

---

## Key Takeaways & CCNA Concepts
* **Default Static Route (`0.0.0.0 0.0.0.0`):** A special static route used when no specific route matches the destination IP address in the routing table.
* **Gateway of Last Resort:** The egress path designated by a router to handle all unmatched traffic, commonly used on stub routers connecting to the Internet.
* **Quad-Zero Notation (`0.0.0.0/0`):** Matches any network IP address (`0.0.0.0`) with any subnet mask (`0.0.0.0`), functioning as a wild-card match for Layer 3 forwarding.
*
