# Lab 16: Port Address Translation (PAT / NAT Overload) Configuration

## Overview
This laboratory project demonstrates the configuration, verification, and operation of **Port Address Translation (PAT)**, also known as **NAT Overload**, using Cisco Packet Tracer.

While standard Dynamic NAT requires a dedicated pool of public IPv4 addresses (1:1 dynamic mapping), Port Address Translation (PAT) solves severe IP exhaustion by allowing multiple internal private hosts to share a single public IPv4 address simultaneously. PAT achieves this Many:1 mapping by tracking unique **Layer 4 Source Port Numbers** on the border router (`R1`), enabling scalable Internet access for enterprise local area networks.

---

## Topology & Components
* **Core Routing Devices:**
  * **Perimeter Edge Router (`R1`):** Border router configured with PAT (`overload`) on its exit WAN interface.
  * **ISP Router (`ISP`):** Service Provider router simulating public WAN transport.
* **Core Switch:**
  * **LAN Switch (`SW1`):** Cisco Catalyst 2960 Layer 2 switch interconnecting internal endpoints.
* **Endpoints & Servers:**
  * **Internal Workstations (`PC1` & `PC2`):** Simulates internal enterprise hosts sharing a single public IP.
  * **External Public Server (`Server-Internet`):** Simulates a public internet destination (IP: `8.8.8.8`).
* **Addressing Scheme:**
  * **Internal LAN Subnet:** `192.168.10.0/24`
  * **Public WAN Transit Subnet (R1-to-ISP):** `200.1.1.0/24`
  * **Public Internet Subnet (ISP-to-External Server):** `8.0.0.0/8`
  * **PC1:** IP `192.168.10.10/24`, Default Gateway `192.168.10.1`
  * **PC2:** IP `192.168.10.20/24`, Default Gateway `192.168.10.1`
  * **Server-Internet:** IP `8.8.8.8/8`, Default Gateway `8.8.8.1`
  * **R1 LAN Gateway (G0/0):** `192.168.10.1/24`
  * **R1 Public WAN (G0/1):** `200.1.1.254/24` (Shared Outside Public IP)
  * **ISP WAN Interface (G0/1):** `200.1.1.1/24`
  * **ISP Public Interface (G0/0):** `8.8.8.1/8`

---

## Step-by-Step Configuration Parameters

### 1. Perimeter Edge Router (R1) Configuration
* **Access Level:** Global Configuration Mode (`configure terminal`)
  * **Hostname Setup:** `hostname R1`
  * **Internal LAN Interface Setup (G0/0):**
    * `interface GigabitEthernet0/0`
    * `ip address 192.168.10.1 255.255.255.0`
    * `ip nat inside`
    * `no shutdown`
  * **External WAN Interface Setup (G0/1):**
    * `interface GigabitEthernet0/1`
    * `ip address 200.1.1.254 255.255.255.0`
    * `ip nat outside`
    * `no shutdown`
  * **Default Gateway Route to ISP:**
    * `ip route 0.0.0.0 0.0.0.0 200.1.1.1`
  * **Standard Access Control List for Internal LAN:**
    * `access-list 1 permit 192.168.10.0 0.0.0.255`
  * **PAT (NAT Overload) Binding to Public Interface:**
    * `ip nat inside source list 1 interface GigabitEthernet0/1 overload`

### 2. Service Provider Router (ISP) Configuration
* **Access Level:** Global Configuration Mode (`configure terminal`)
  * **Hostname Setup:** `hostname ISP`
  * **External Public Server Interface Setup (G0/0):**
    * `interface GigabitEthernet0/0`
    * `ip address 8.8.8.1 255.0.0.0`
    * `no shutdown`
  * **Perimeter WAN Interface Setup (G0/1):**
    * `interface GigabitEthernet0/1`
    * `ip address 200.1.1.1 255.255.255.0`
    * `no shutdown`
  * **Reverse Static Route for Public Interface IP:**
    * `ip route 0.0.0.0 0.0.0.0 200.1.1.254`

---

## Verification & Testing Commands

### 1. Concurrent Outreach Test (Multiple Hosts to Internet)
From the Command Prompt of **`PC1`**, execute:
`ping 8.8.8.8`

Simultaneously, from the Command Prompt of **`PC2`**, execute:
`ping 8.8.8.8`

* **Expected Result:** Both endpoints receive successful ICMP echo replies from `8.8.8.8`, validating that multiple distinct internal hosts can access public destinations at the same time.

### 2. PAT / Port Translation Table Inspection
From Privileged EXEC Mode of **`R1`**, execute:
`show ip nat translations`

* **Expected Result:** The NAT table displays active dynamic translations where both `192.168.10.10` and `192.168.10.20` are translated to the **same** Inside Global IPv4 address (`200.1.1.254`), differentiated uniquely by their assigned Layer 4 port numbers (e.g., `200.1.1.254:1024` and `200.1.1.254:1025`).
*
