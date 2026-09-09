# Lab 17: DHCP Relay Agent Configuration Across Remote Subnets

## Overview
This laboratory project demonstrates the configuration, verification, and operation of a **DHCP Relay Agent** using Cisco Packet Tracer.

By default, clients issue DHCP discovery requests as Layer 2/3 **Broadcast messages** (`255.255.255.255`), which are inherently dropped by border routers to isolate broadcast domains. A DHCP Relay Agent resolves this limitation by intercepting broadcast DHCP requests on a local client-facing interface, embedding the relaying interface IP into the packet header (`Giaddr` field), and forwarding the request as a targeted **Unicast message** directly to a centralized DHCP Server located on a remote subnet.

---

## Topology & Components
* **Core Routing Device:**
  * **Inter-Subnet Router (`R1`):** Border router configured with the `ip helper-address` mechanism to forward DHCP broadcast traffic across subnets.
* **Core Switch:**
  * **LAN Switch (`SW1`):** Cisco Catalyst 2960 Layer 2 switch interconnecting internal endpoints.
* **Endpoints & Centralized Server:**
  * **Internal Workstation (`PC1`):** Dynamic client endpoint residing in LAN1 requesting an IP address.
  * **Centralized DHCP Server (`DHCP-Server`):** Dedicated server residing in LAN2 hosting IP address pools for remote subnets.
* **Addressing Scheme:**
  * **Client LAN Subnet (LAN1):** `192.168.10.0/24`
  * **Server Subnet (LAN2):** `192.168.20.0/24`
  * **PC1:** Dynamic IPv4 allocation via DHCP (Scope: `192.168.10.10` - `192.168.10.254`)
  * **DHCP Server:** Static IP `192.168.20.2/24`, Default Gateway `192.168.20.1`
  * **R1 LAN1 Gateway (G0/0):** `192.168.10.1/24` (Client Default Gateway & Relay Interface)
  * **R1 LAN2 Gateway (G0/1):** `192.168.20.1/24` (Server-Facing Transit Gateway)

---

## Step-by-Step Configuration Parameters

### 1. Inter-Subnet Router (R1) Configuration
* **Access Level:** Global Configuration Mode (`configure terminal`)
  * **Hostname Setup:** `hostname R1`
  * **Client LAN Interface Setup & Relay Agent Setup (G0/0):**
    * `interface GigabitEthernet0/0`
    * `ip address 192.168.10.1 255.255.255.0`
    * `ip helper-address 192.168.20.2`
    * `no shutdown`
  * **Server-Facing Interface Setup (G0/1):**
    * `interface GigabitEthernet0/1`
    * `ip address 192.168.20.1 255.255.255.0`
    * `no shutdown`

### 2. Centralized DHCP Server Configuration
* **Static IP Parameters (Desktop -> IP Configuration):**
  * **IP Address:** `192.168.20.2`
  * **Subnet Mask:** `255.255.255.0`
  * **Default Gateway:** `192.168.20.1`
* **DHCP Service & Remote Scope Setup (Services -> DHCP):**
  * **Service State:** `On`
  * **Pool Name:** `LAN1-POOL`
  * **Default Gateway:** `192.168.10.1`
  * **DNS Server:** `8.8.8.8`
  * **Start IP Address:** `192.168.10.10`
  * **Subnet Mask:** `255.255.255.0`

---

## Verification & Testing Commands

### 1. Dynamic IPv4 Allocation Test (PC1)
From the graphical configuration of **`PC1`** (Desktop -> IP Configuration):
Select **DHCP** mode.

* **Expected Result:** The endpoint successfully transitions from APIPA (`169.254.x.x`) and displays **`DHCP request successful`**, populating:
  * **IPv4 Address:** `192.168.10.10`
  * **Subnet Mask:** `255.255.255.0`
  * **Default Gateway:** `192.168.10.1`
  * **DNS Server:** `8.8.8.8`

### 2. Inter-Subnet End-to-End Connectivity Test
From the Command Prompt of **`PC1`**, execute:
`ping 192.168.20.2`

* **Expected Result:** Successful ICMP Echo replies from the remote DHCP server (`192.168.20.2`), verifying full Layer 3 connectivity across the border router `R1`.
*
