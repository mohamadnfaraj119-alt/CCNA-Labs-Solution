# Lab 14: IPv4 Floating Static Route Configuration

## Overview
This laboratory project demonstrates the configuration, verification, and operation of an **IPv4 Floating Static Route** using Cisco Packet Tracer. 

A floating static route acts as an automated backup path that remains dormant in the router's topology database until the primary static route fails. By manipulating the **Administrative Distance (AD)** parameter—assigning a higher metric (e.g., `AD = 10`) to the backup route relative to the default static route (`AD = 1`)—network engineers achieve seamless Layer 3 redundancy, high availability, and failover/failback protection without dynamic routing protocol overhead.

---

## Topology & Components
* **Core Routing Devices:**
  * 2x Cisco 2911 Routers (`R1` as Edge/Branch Router, `R2` as HQ/Core Router)
* **Endpoints:**
  * 2x Generic PCs (`PC1` on LAN 1, `PC2` on LAN 2)
* **Addressing Scheme:**
  * **LAN 1 Subnet (R1 Site):** `192.168.10.0/24`
  * **LAN 2 Subnet (R2 Site):** `192.168.20.0/24`
  * **Primary Transit WAN Subnet (G0/1):** `10.1.1.0/30`
  * **Backup Transit WAN Subnet (G0/2):** `10.2.2.0/30`
  * **PC1:** IP `192.168.10.10/24`, Default Gateway `192.168.10.1`
  * **PC2:** IP `192.168.20.10/24`, Default Gateway `192.168.20.1`
  * **R1 LAN Gateway (G0/0):** `192.168.10.1/24`
  * **R1 Primary WAN (G0/1):** `10.1.1.1/30`
  * **R1 Backup WAN (G0/2):** `10.2.2.1/30`
  * **R2 LAN Gateway (G0/0):** `192.168.20.1/24`
  * **R2 Primary WAN (G0/1):** `10.1.1.2/30`
  * **R2 Backup WAN (G0/2):** `10.2.2.2/30`

---

## Step-by-Step Configuration Parameters

### 1. Edge / Branch Router (R1) Configuration
* **Access Level:** Global Configuration Mode (`configure terminal`)
  * **Hostname Setup:** `hostname R1`
  * **LAN Interface Setup (G0/0):**
    * `interface GigabitEthernet0/0`
    * `ip address 192.168.10.1 255.255.255.0`
    * `no shutdown`
  * **Primary WAN Interface Setup (G0/1):**
    * `interface GigabitEthernet0/1`
    * `ip address 10.1.1.1 255.255.255.252`
    * `no shutdown`
  * **Backup WAN Interface Setup (G0/2):**
    * `interface GigabitEthernet0/2`
    * `ip address 10.2.2.1 255.255.255.252`
    * `no shutdown`
  * **Primary Static Route Injection (AD = 1):**
    * `ip route 192.168.20.0 255.255.255.0 10.1.1.2`
  * **Floating Static Route Injection (AD = 10):**
    * `ip route 192.168.20.0 255.255.255.0 10.2.2.2 10`

### 2. HQ / Core Router (R2) Configuration
* **Access Level:** Global Configuration Mode (`configure terminal`)
  * **Hostname Setup:** `hostname R2`
  * **LAN Interface Setup (G0/0):**
    * `interface GigabitEthernet0/0`
    * `ip address 192.168.20.1 255.255.255.0`
    * `no shutdown`
  * **Primary WAN Interface Setup (G0/1):**
    * `interface GigabitEthernet0/1`
    * `ip address 10.1.1.2 255.255.255.252`
    * `no shutdown`
  * **Backup WAN Interface Setup (G0/2):**
    * `interface GigabitEthernet0/2`
    * `ip address 10.2.2.2 255.255.255.252`
    * `no shutdown`
  * **Primary Static Route Injection (AD = 1):**
    * `ip route 192.168.10.0 255.255.255.0 10.1.1.1`
  * **Floating Static Route Injection (AD = 10):**
    * `ip route 192.168.10.0 255.255.255.0 10.2.2.1 10`

---

## Verification & Failover Testing Commands

### 1. Routing Table Primary State Verification
From Privileged EXEC Mode of `R1`, execute:
`show ip route`

* **Expected Result:** Output displays only the primary static route denoted by **`S`** via `10.1.1.2` (`AD = 1`). The backup route via `10.2.2.2` (`AD = 10`) remains floating/dormant and omitted from the active IP routing table.

### 2. Primary Link Failure Simulation (Failover Test)
Simulate an outage by shutting down the primary WAN interface on `R1`:
```text
configure terminal
interface GigabitEthernet0/1
 shutdown
end
