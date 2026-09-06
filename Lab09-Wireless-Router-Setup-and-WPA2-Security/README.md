# Lab 09: Wireless Router Setup and WPA2 Security Configuration

## Overview
This laboratory project demonstrates the installation, GUI configuration, and security hardening of a wireless local area network (WLAN) using the Cisco WRT300N Wireless Router in Packet Tracer.

The primary focus is setting up an Access Point / Wireless Router to broadcast a customized Service Set Identifier (SSID), enforce **WPA2-Personal (AES)** encryption with a strong Pre-Shared Key (PSK), and dynamically allocate local IP addresses to mobile endpoints via an integrated DHCP server.

---

## Topology & Components
* **Core Wireless Device:**
  * 1x Linksys WRT300N Wireless Router (`HomeRouter`)
* **Wireless Endpoints:**
  * 1x Laptop (`Laptop0` fitted with `WPC300N` Wireless NIC module)
  * 1x Smartphone (`Smartphone0`)
* **Addressing Scheme:**
  * **Network Subnet:** `192.168.0.0/24`
  * **Default Gateway (WRT300N LAN IP):** `192.168.0.1`
  * **DHCP Address Pool:** `192.168.0.100 - 192.168.0.149`

---

## Step-by-Step Configuration Parameters

### 1. Wireless Router (WRT300N) Setup
* **GUI Navigation:** `Setup` -> `Basic Setup`
  * **Local IP Address:** `192.168.0.1`
  * **Subnet Mask:** `255.255.255.0`
  * **DHCP Server:** `Enabled`
* **GUI Navigation:** `Wireless` -> `Basic Wireless Settings`
  * **Network Mode:** `Mixed`
  * **Network Name (SSID):** `Home-WiFi`
  * **SSID Broadcast:** `Enabled`
* **GUI Navigation:** `Wireless` -> `Wireless Security`
  * **Security Mode:** `WPA2 Personal`
  * **Encryption:** `AES`
  * **Passphrase (PSK):** `Cisco12345`

### 2. Endpoint Configuration
* **Laptop Setup:**
  1. Power off device, remove default RJ-45 `PT-LAPTOP-NM-1CFE` module, and insert `WPC300N` wireless card.
  2. Power on device.
  3. Navigate to `Desktop` -> `PC Wireless` -> `Connect`.
  4. Scan and select SSID `Home-WiFi`, then authenticate using key `Cisco12345`.
* **Smartphone Setup:**
  1. Open `Config` -> `Wireless0`.
  2. Set **SSID** to `Home-WiFi`.
  3. Select **WPA2-PSK** and enter passphrase `Cisco12345`.
  4. Verify dynamic IPv4 address acquisition via DHCP (`Desktop` -> `IP Configuration`).

---

## Verification & Troubleshooting Commands

### 1. DHCP IP Assignment Check
From the CLI / Command Prompt of any connected wireless client, execute:
ipconfig /all

* **Expected Result:** Device receives an IP in range `192.168.0.x`, subnet mask `255.255.255.0`, and Default Gateway `192.168.0.1`.

### 2. End-to-End Connectivity Testing (Ping Test)
From `Laptop0` Command Prompt, execute:
ping 192.168.0.1

* **Expected Result:** **100% Success (0% packet loss)** — Validates layer 1/2 wireless association, WPA2 key authentication, and layer 3 ICMP reachability to the default gateway.

---

## Key Takeaways & CCNA Concepts
* **SSID (Service Set Identifier):** The technical name of the WLAN broadcasted by Access Points so clients can discover and identify the wireless network.
* **WPA2-Personal (WPA2-PSK):** Uses Pre-Shared Keys and **AES (Advanced Encryption Standard)** encryption to protect data frames sent over radio frequencies against eavesdropping.
* **Integrated DHCP:** Simplifies network administration by automatically assigning IP configuration parameters to joining wireless hosts.
*
