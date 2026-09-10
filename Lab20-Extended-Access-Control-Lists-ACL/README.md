# Lab 20: Standard Access Control Lists (ACL) Configuration

## Overview
This laboratory project demonstrates the configuration, verification, and operational logic of **Standard IPv4 Access Control Lists (ACLs)** using Cisco Packet Tracer.

In routed enterprise networks, all subnets can communicate natively without restrictions. To protect sensitive subnets (such as servers or finance departments), traffic filtering is mandatory. A **Standard Access Control List** operates as a stateless packet filter at Layer 3, inspecting packet headers exclusively based on the **Source IP Address**. By deploying an ACL near the destination network, administrators can grant or deny access to specific host devices or entire network segments, enforcing granular organizational security policies.

---

## Topology & Components
* **Core Routing Device:**
  * **Inter-VLAN / Border Router (`R1`):** Cisco 2911 ISR executing packet filtering policies.
* **Switching Infrastructure:**
  * **Sales Access Switch (`SW-Sales`):** Cisco Catalyst 2960 connecting the source hosts.
  * **Finance Access Switch (`SW-Finance`):** Cisco Catalyst 2960 connecting the protected target hosts.
* **End-User Workstations:**
  * **Blocked Host (`PC-Sales-Blocked`):** Unauthorized IP source (`192.168.10.50/24`).
  * **Allowed Host (`PC-Sales-Allowed`):** Authorized IP source (`192.168.10.10/24`).
  * **Target Host (`PC-Finance`):** Protected destination host (`192.168.20.10/24`).
* **Addressing & Interface Scheme:**
  * **Sales Subnet:** `192.168.10.0/24` via Interface `GigabitEthernet 0/0` (`192.168.10.1`)
  * **Finance Subnet:** `192.168.20.0/24` via Interface `GigabitEthernet 0/1` (`192.168.20.1`)

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
  * **Finance Subnet Gateway Interface (G0/1):**
    * `interface GigabitEthernet0/1`
    * `ip address 192.168.20.1 255.255.255.0`
    * `no shutdown`

### 2. Standard Access Control List Creation & Application (R1)
* **Access Level:** Global Configuration Mode
  * **Deny Specific Blocked Source Host:**
    * `access-list 1 deny host 192.168.10.50`
  * **Permit All Remaining Unmatched Traffic (Override Implicit Deny):**
    * `access-list 1 permit any`
  * **Interface Binding (Outbound on Target Interface):**
    * `interface GigabitEthernet0/1`
    * `ip access-group 1 out`

---

## Verification & Testing Commands

### 1. Verification of Blocked Host (PC-Sales-Blocked)
From Command Prompt of **`PC-Sales-Blocked`** (`192.168.10.50`), execute:
`ping 192.168.20.10`

* **Expected Result:** Ping fails with **`Reply from 192.168.10.1: Destination host unreachable`**, confirming ICMP packets are dropped by `R1`'s outbound filter.

### 2. Verification of Allowed Host (PC-Sales-Allowed)
From Command Prompt of **`PC-Sales-Allowed`** (`192.168.10.10`), execute:
`ping 192.168.20.10`

* **Expected Result:** Successful ICMP echo replies (`Reply from 192.168.20.10: bytes=32 time<1ms TTL=127`), confirming that `access-list 1 permit any` permits legitimate network traffic.

### 3. Inspection of ACL Hit Counters
From Privileged EXEC Mode of **`R1`**, execute:
`show access-lists`

* **Expected Result:** Output shows `Standard IP access list 1` with operational match counters:
  * `10 deny host 192.168.10.50 (4 match(es))`
  * `20 permit any`
================================================================================
