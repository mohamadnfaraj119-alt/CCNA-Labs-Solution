# Lab 19: Port Security and Switch Protection Configuration

## Overview
This laboratory project demonstrates the configuration, verification, and operational logic of **Port Security** on Cisco Catalyst switches using Cisco Packet Tracer.

In enterprise networks, unsecured physical switchports present a significant security vulnerability. An unauthorized user or rogue device can simply plug an Ethernet cable into an open wall jack or replace an employee's computer to gain access to the internal network infrastructure. **Port Security** mitigates this risk by securing Layer 2 switch interfaces based on the connected host's **MAC Address**. By enforcing strict MAC address limits, binding trusted hardware addresses via `sticky` learning, and defining dynamic penalty actions (`shutdown`), the switch effectively blocks unauthorized access attempts and isolates potential security breaches automatically.

---

## Topology & Components
* **Switching Device:**
  * **Access Switch (`SW1`):** Cisco Catalyst 2960 Switch enforcing Layer 2 security policies on access ports.
* **End-User Workstations:**
  * **Authorized Employee Host (`PC-Legit`):** Trusted workstation allowed to transmit traffic on interface `FastEthernet 0/1`.
  * **Unauthorized Attacker Host (`PC-Attacker`):** Rogue device used to simulate an unauthorized port-access attempt.
* **Addressing & Port Scheme:**
  * **Subnet:** `192.168.1.0/24`
  * **Target Secured Interface:** `FastEthernet 0/1`
  * **PC-Legit IP Address:** `192.168.1.10/24`
  * **PC-Attacker IP Address:** `192.168.1.20/24`

---

## Step-by-Step Configuration Parameters

### 1. Authorized Host Setup (PC-Legit)
* **Static IP Parameters (Desktop -> IP Configuration):**
  * **IP Address:** `192.168.1.10`
  * **Subnet Mask:** `255.255.255.0`

### 2. Access Switch Port Security Configuration (SW1)
* **Access Level:** Interface Configuration Mode (`interface FastEthernet 0/1`)
  * **Force Access Mode:**
    * `switchport mode access`
  * **Enable Port Security Feature:**
    * `switchport port-security`
  * **Define Maximum Allowed MAC Addresses:**
    * `switchport port-security maximum 1`
  * **Enable Dynamic MAC Learning & Binding (Sticky):**
    * `switchport port-security mac-address sticky`
  * **Configure Violation Penalty Action:**
    * `switchport port-security violation shutdown`

---

## Verification & Attack Simulation Commands

### 1. Initial Port Security State Inspection
From Privileged EXEC Mode of **`SW1`**, execute:
`show port-security interface FastEthernet 0/1`

* **Expected Result:**
  * **Port Security:** `Enabled`
  * **Port Status:** `Secure-up`
  * **Violation Mode:** `Shutdown`
  * **Maximum MAC Addresses:** `1`

### 2. Sticky MAC Learning Trigger
From Command Prompt of **`PC-Legit`**, generate traffic:
`ping 192.168.1.10`

Re-check **`SW1`** status:
`show port-security interface FastEthernet 0/1`
* **Expected Result:** `Total MAC Addresses` and `Sticky MAC Addresses` increment to `1`, indicating that `PC-Legit`'s hardware address has been learned and dynamically saved to the running configuration.

### 3. Rogue Access Attack Simulation
1. Disconnect `PC-Legit` from interface `FastEthernet 0/1`.
2. Connect `PC-Attacker` to interface `FastEthernet 0/1`.
3. From Command Prompt of **`PC-Attacker`**, attempt to send traffic:
   `ping 192.168.1.10`

### 4. Violation Enforcement Verification
From Privileged EXEC Mode of **`SW1`**, execute:
`show port-security interface FastEthernet 0/1`

* **Expected Result:**
  * **Port Status:** `Secure-shutdown` (Interface link LED turns RED).
  * **Security Violation Count:** `1` (or higher), confirming that the unauthorized frames triggered an immediate port shutdown (`err-disable` state).

### 5. Port Recovery Procedure (Post-Violation)
To restore a shutdown port after removing the unauthorized host:
```text
SW1(config)# interface FastEthernet 0/1
SW1(config-if)# shutdown
SW1(config-if)# no shutdown
