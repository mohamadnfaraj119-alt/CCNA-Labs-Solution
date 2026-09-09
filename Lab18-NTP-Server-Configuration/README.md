# Lab 18: Network Time Protocol (NTP) Server Configuration & Synchronization

## Overview
This laboratory project demonstrates the configuration, verification, and operational logic of **Network Time Protocol (NTP)** synchronization using Cisco Packet Tracer.

In enterprise networks, hardware clocks (real-time clocks) inside routers, switches, and security appliances drift over time, causing inconsistent timestamps across systems. Accurate time synchronization is critical for log correlation, cryptographic key validation, and security auditing (Syslog). Configured as an NTP client, the border router (`R1`) synchronizes its system clock with a centralized **NTP Server**, maintaining an accurate timestamp hierarchy (Stratum levels) across the network infrastructure.

---

## Topology & Components
* **Core Routing Device:**
  * **Border Router (`R1`):** Cisco 2911 ISR acting as an NTP client querying the reference time source.
* **Centralized Time Source:**
  * **Network Time Server (`NTP-Server`):** Dedicated server operating as an NTP reference server (Stratum 1 source).
* **Addressing Scheme:**
  * **Subnet:** `192.168.1.0/24`
  * **R1 LAN Interface (G0/0):** `192.168.1.1/24`
  * **NTP Server Interface (Fa0):** `192.168.1.2/24`, Default Gateway `192.168.1.1`

---

## Step-by-Step Configuration Parameters

### 1. Border Router (R1) Configuration
* **Access Level:** Global Configuration Mode (`configure terminal`)
  * **Hostname Setup:** `hostname R1`
  * **LAN Interface Setup (G0/0):**
    * `interface GigabitEthernet0/0`
    * `ip address 192.168.1.1 255.255.255.0`
    * `no shutdown`
  * **NTP Client Binding to Central Time Source:**
    * `ntp server 192.168.1.2`

### 2. Centralized NTP Server Configuration
* **Static IP Parameters (Desktop -> IP Configuration):**
  * **IP Address:** `192.168.1.2`
  * **Subnet Mask:** `255.255.255.0`
  * **Default Gateway:** `192.168.1.1`
* **NTP Service Setup (Services -> NTP):**
  * **NTP Service State:** `Enable`
  * **Authentication:** `Disable` (Unauthenticated simple polling)
  * **Date & Time Parameters:** Set manually to current valid UTC/local time.

---

## Verification & Testing Commands

### 1. Layer 3 Connectivity Verification
From Privileged EXEC Mode of **`R1`**, execute:
`ping 192.168.1.2`

* **Expected Result:** Success rate is 80% to 100% (`.!!!!` or `!!!!!`), confirming reachability between `R1` and the NTP server.

### 2. NTP Synchronization Status Inspection
From Privileged EXEC Mode of **`R1`**, execute:
`show ntp status`

* **Expected Result:** After allowing time for polling (or using **Fast Forward Time** in Packet Tracer), the router transitions from `Clock is unsynchronized` to:
  * **Synchronization State:** `Clock is synchronized`
  * **Stratum Level:** `stratum 2` (indicates one hop away from Stratum 1 server)
  * **Reference IP:** `192.168.1.2`

### 3. NTP Peer Associations Inspection
From Privileged EXEC Mode of **`R1`**, execute:
`show ntp associations`

* **Expected Result:** Displays `192.168.1.2` in the associations table prefixed with an asterisk (`*`), indicating that `192.168.1.2` is the active configured master time provider.

### 4. Router Clock Verification
From Privileged EXEC Mode of **`R1`**, execute:
`show clock`

* **Expected Result:** The internal software clock displays the updated time and date matching the configured reference time on `NTP-Server`.
*
