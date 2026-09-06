# Lab 08: Configuring DNS Server and Web Browsing

## Overview
This laboratory project demonstrates the configuration and integration of **Domain Name System (DNS)** and **HTTP Web Services** within a Cisco Packet Tracer network topology. 

By mapping human-readable Domain Names (e.g., `www.cisco.lab`) to explicit IPv4 addresses, DNS resolves the necessity of remembering numeric host addresses. This lab covers setting up a dynamic DHCP network alongside a dedicated Server handling both Web hosting and DNS resolution.

---

## Topology & Requirements
* **Devices:**
  * 1x Cisco 2911 Router (`R1`)
  * 1x Cisco 2960 Switch (`SW1`)
  * 1x Cisco Server (`Server-Web-DNS`)
  * 1x PC Client (`PC1`)
* **Physical Connections:**
  * `R1 GigabitEthernet0/0` <---> `SW1 GigabitEthernet0/1`
  * `SW1 FastEthernet0/10` <---> `Server-Web-DNS`
  * `SW1 FastEthernet0/1` <---> `PC1`
* **Subnet:** `192.168.10.0/24`
* **Static Gateway:** `192.168.10.1` (`R1 g0/0`)
* **Static Server IP:** `192.168.10.100` (`Server-Web-DNS`)
* **Domain Record:** `www.cisco.lab` ---> `192.168.10.100`

---

## Step-by-Step Configuration Commands

### 1. Configure Router R1 (Gateway & DHCP Server with DNS Option)
```text
enable
configure terminal
hostname R1

! Configure Interface Gateway
interface GigabitEthernet 0/0
 ip address 192.168.10.1 255.255.255.0
 no shutdown
 exit

! Exclude Router and Server Static IPs from Dynamic Pool
ip dhcp excluded-address 192.168.10.1
ip dhcp excluded-address 192.168.10.100

! Configure DHCP Pool with DNS Parameters
ip dhcp pool LAN_OFFICE
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 dns-server 192.168.10.100
 exit
end
write
