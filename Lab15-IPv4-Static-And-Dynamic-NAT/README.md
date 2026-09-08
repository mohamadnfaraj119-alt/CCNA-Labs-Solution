# Lab 15: IPv4 Static and Dynamic NAT Configuration

## Overview
This laboratory project demonstrates the configuration, verification, and operation of **Static Network Address Translation (Static NAT)** and **Dynamic Network Address Translation (Dynamic NAT)** using Cisco Packet Tracer.

Because RFC 1918 private IPv4 addresses are non-routable over the public Internet, Network Address Translation (NAT) acts as an essential border security and translation mechanism at the enterprise edge. This lab covers two core NAT mechanisms on the perimeter router (`R1`):
* **Static NAT (1:1 Mapping):** Binds a permanent private IP address to a dedicated public IP address. This enables inbound reachability for internal servers (e.g., `Server-LAN`) from external public networks.
* **Dynamic NAT (Many:Many Pool Mapping):** Translates internal private addresses dynamically using a pool of public IPv4 addresses (`my_pool`) constrained by a Standard Access Control List (`ACL 1`), allowing internal end-user workstations (e.g., `PC1`) to reach external destinations.

---

## Topology & Components
* **Core Routing Devices:**
  * **Perimeter/Edge Router (`R1`):** Border router handling inside/outside NAT boundary operations.
  * **ISP Router (`ISP`):** Service Provider router simulating public WAN transport.
* **Endpoints & Servers:**
  * **Internal Workstation (`PC1`):** Simulates internal enterprise end-user traffic.
  * **Internal Web/App Server (`Server-LAN`):** Local server published externally via Static NAT.
  * **External Public Server (`Server-Internet`):** Simulates a public internet destination (IP: `8.8.8.8`).
* **Addressing Scheme:**
  * **LAN 1 Subnet (PC1 Segment):** `192.168.10.0/24`
  * **LAN 2 Subnet (Server-LAN Segment):** `192.168.20.0/24`
  * **Public WAN Transit Subnet (R1-to-ISP):** `200.1.1.0/24`
  * **Public Internet Subnet (ISP-to-External Server):** `8.0.0.0/8`
  * **PC1:** IP `192.168.10.10/24`, Default Gateway `192.168.10.1`
  * **Server-LAN:** IP `192.168.20.100/24`, Default Gateway `192.168.20.1`
  * **Server-Internet:** IP `8.8.8.8/8`, Default Gateway `8.8.8.1`
  * **R1 LAN Gateway 1 (G0/0):** `192.168.10.1/24`
  * **R1 LAN Gateway 2 (G0/2):** `192.168.20.1/24`
  * **R1 Public WAN (G0/1):** `200.1.1.254/24`
  * **ISP WAN Interface (G0/1):** `200.1.1.1/24`
  * **ISP Public Interface (G0/0):** `8.8.8.1/8`
  * **Static NAT Mapped Address (Server-LAN):** `200.1.1.100`
  * **Dynamic NAT Pool Range (`my_pool`):** `200.1.1.10` to `200.1.1.20`

---

## Step-by-Step Configuration Parameters

### 1. Perimeter Edge Router (R1) Configuration
* **Access Level:** Global Configuration Mode (`configure terminal`)
  * **Hostname Setup:** `hostname R1`
  * **Internal LAN 1 Interface Setup (G0/0):**
    ```text
    interface GigabitEthernet0/0
     ip address 192.168.10.1 255.255.255.0
     ip nat inside
     no shutdown
    ```
  * **Internal LAN 2 Interface Setup (G0/2):**
    ```text
    interface GigabitEthernet0/2
     ip address 192.168.20.1 255.255.255.0
     ip nat inside
     no shutdown
    ```
  * **External WAN Interface Setup (G0/1):**
    ```text
    interface GigabitEthernet0/1
     ip address 200.1.1.254 255.255.255.0
     ip nat outside
     no shutdown
    ```
  * **Default Gateway Route to ISP:**
    ```text
    ip route 0.0.0.0 0.0.0.0 200.1.1.1
    ```
  * **Static NAT Implementation (1:1 Mapping for Internal Server):**
    ```text
    ip nat inside source static 192.168.20.100 200.1.1.100
    ```
  * **Dynamic NAT Implementation (Pool & ACL Binding for LAN Users):**
    ```text
    ip nat pool my_pool 200.1.1.10 200.1.1.20 netmask 255.255.255.0
    access-list 1 permit 192.168.10.0 0.0.0.255
    ip nat inside source list 1 pool my_pool
    ```

### 2. Service Provider Router (ISP) Configuration
* **Access Level:** Global Configuration Mode (`configure terminal`)
  * **Hostname Setup:** `hostname ISP`
  * **External Public Server Interface Setup (G0/0):**
    ```text
    interface GigabitEthernet0/0
     ip address 8.8.8.1 255.0.0.0
     no shutdown
    ```
  * **Perimeter WAN Interface Setup (G0/1):**
    ```text
    interface GigabitEthernet0/1
     ip address 200.1.1.1 255.255.255.0
     no shutdown
    ```
  * **Reverse Static Route for Mapped Public NAT Subnet:**
    ```text
    ip route 0.0.0.0 0.0.0.0 200.1.1.254
    ```

---

## Verification & Testing Commands

### 1. Dynamic NAT Outreach Test (Internal PC to Public Server)
From the Command Prompt of `PC1`, execute:
```text
ping 8.8.8.8
