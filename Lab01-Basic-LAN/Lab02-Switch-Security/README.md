## Lab 02: Switch Basic Configuration & Security

### 📌 Overview
Implemented basic management configurations and hardened security controls on a Cisco Catalyst 2960 switch to secure administrative access and protect network infrastructure.

### 🌐 Topology & Components
- **Device:** Cisco Catalyst 2960 Switch (`SW1`)
- **Management Console:** Admin PC connected via Console Cable (`RS232` to `Console Port`)

### 🛠️ Key CLI Configurations

#### 1. Basic Switch Identity & Banner Notice
enable
configure terminal
hostname SW1
banner motd # AUTHORIZED ACCESS ONLY! UNAUTHORIZED LOGINS WILL BE PROSECUTED. #

#### 2. Securing Management Access (Passwords & Encryption)
! Secure Privileged EXEC Mode
enable secret Cisco123!

! Secure Console Port
line console 0
 password ConsolePass123
 login
 exit

! Secure VTY Lines (Telnet/SSH Access)
line vty 0 15
 password RemotePass123
 login
 exit

! Encrypt All Plaintext Passwords
service password-encryption

#### 3. Configuring Switch Virtual Interface (SVI) for Management
interface vlan 1
 ip address 192.168.1.2 255.255.255.0
 no shutdown
exit

! Configured Default Gateway for Remote Subnet Access
ip default-gateway 192.168.1.1

#### 4. Unused Ports Security
! Disable Unused Interfaces
interface range FastEthernet 0/3 - 24, GigabitEthernet 0/1 - 2
 shutdown

### ✅ Verification & Validation Tests
- **Banner MOTD Test:** Verified warning message displays upon accessing console connection.
- **Password Verification:** Confirmed password prompts for both Console and `enable` mode.
- **Configuration Encryption:** Executed `show running-config` and confirmed all plain-text passwords are encrypted (`type 7`).
- **Management Ping Test:** Successfully pinged SVI `192.168.1.2` from administrative PC.
-
