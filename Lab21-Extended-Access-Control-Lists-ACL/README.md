# Lab 21: Extended Access Control Lists (Extended ACL) Configuration

## Overview
This laboratory project demonstrates the configuration, verification, and operational logic of **Extended IPv4 Access Control Lists (Extended ACLs)** using Cisco Packet Tracer.

Unlike Standard ACLs which only filter traffic based on source IP addresses, Extended ACLs offer granular Layer 3 and Layer 4 control. They inspect the **Source IP**, **Destination IP**, **Transport Layer Protocol (TCP/UDP/ICMP)**, and **Port Numbers**. By deploying an Extended ACL near the source network, administrators can selectively block specific services (such as HTTP Web traffic on TCP Port 80) while permitting other traffic flows (such as ICMP ping) to pass unimpeded.

---

## Topology & Components
* **Core Routing Device:**
  * **Inter-VLAN / Border Router (`R1`):** Cisco 2911 ISR performing stateful/stateless packet filtering.
* **Switching Infrastructure:**
  * **Sales Access Switch (`SW-Sales`):** Cisco Catalyst 2960 connecting client hosts.
  * **Server Access Switch (`SW-Server`):** Cisco Catalyst 2960 connecting corporate server resources.
* **Network Endpoints:**
  * **Client Workstation (`PC-Sales`):** Host IP (`192.168.10.10/24`), Default Gateway (`192.168.10.1`).
  * **Web Server (`Server-Web`):** Target Server IP (`192.168.20.100/24`), Default Gateway (`192.168.20.1`).
* **Addressing & Interface Scheme:**
  * **Sales Subnet:** `192.168.10.0/24` connected via Interface `GigabitEthernet 0/0` (`192.168.10.1`).
  * **Server Subnet:** `192.168.20.0/24` connected via Interface `GigabitEthernet 0/1` (`192.168.20.1`).

---

## Step-by-Step Configuration Parameters

### 1. Interface Addressing Setup (R1)
* **Access Level:** Global Configuration Mode (`configure terminal`)
  * **Hostname Configuration:**
    * `hostname R1`
  * **Sales Subnet Gateway Interface (G0/0):**
    * `interface GigabitEthernet0/0`
    * `ip address 192.168.10.1 255.255.255.0`
    * `no shutdown`
  * **Server Subnet Gateway Interface (G0/1):**
    * `interface GigabitEthernet0/1`
    * `ip address 192.168.20.1 255.255.255.0`
    * `no shutdown`

### 2. Extended Access Control List Creation & Binding (R1)
* **Access Level:** Global Configuration Mode
  * **Deny HTTP Web Traffic (TCP Port 80) from Sales Subnet to Web Server:**
    * `access-list 100 deny tcp 192.168.10.0 0.0.0.255 host 192.168.20.100 eq 80`
  * **Permit All Remaining IP Traffic (Override Implicit Deny):**
    * `access-list 100 permit ip any any`
  * **Interface Binding (Inbound on Source Interface):**
    * `interface GigabitEthernet0/0`
    * `ip access-group 100 in`

---

## Verification & Testing Commands

### 1. Verification of HTTP Service Blocking
Open the **Web Browser** on `PC-Sales` (`192.168.10.10`) and navigate to `http://192.168.20.100`.

* **Expected Result:** Connection attempt times out (`Request Timeout`), verifying that HTTP/TCP port 80 traffic is dropped inbound at `R1`'s `G0/0` interface.

### 2. Verification of Allowed ICMP (Ping) Traffic
Open the **Command Prompt** on `PC-Sales` (`192.168.10.10`) and execute:
`ping 192.168.20.100`

* **Expected Result:** Successful echo replies (`Reply from 192.168.20.100: bytes=32 time<1ms TTL=127`), confirming non-HTTP IP traffic is allowed by `access-list 100 permit ip any any`.

### 3. Inspection of ACL Hit Counters
From Privileged EXEC Mode of **`R1`**, execute:
`show access-lists`

* **Expected Result:** Output displays `Extended IP access list 100` with hit counters matching filtered traffic:
  * `10 deny tcp 192.168.10.0 0.0.0.255 host 192.168.20.100 eq www (106 match(es))`
  * `20 permit ip any any (4 match(es))`
