# CCNA 200-301 Practical Labs

## Lab 01: Basic LAN Configuration & Connectivity Test
- **Topology**: 2 PCs connected to a Cisco 2960 Switch.
- **Goal**: Configure IPv4 addresses and verify end-to-end connectivity using `ping`.
- **IP Scheme**:
  - PC0: `192.168.1.10/24`
  - PC1: `192.168.1.20/24`
- **Result**: Successful reply received on ping execution.

---

## Lab 02: Switch Basic Configuration & Security
- **Topology**: Cisco 2960 Switch.
- **Goal**: Configure device identity, secure CLI access modes, enable encryption, and save running configuration to NVRAM.
- **Key Configurations Implemented**:
  - `hostname SW-Access1`
  - `enable secret cisco123`
  - `line console 0` -> `password adminpass` & `login`
  - `service password-encryption`
  - `banner motd # Authorized Access Only! #`
  - `copy running-config startup-config`
- **Validation**:
  - Console access successfully requested `adminpass`.
  - Privileged mode successfully restricted via `cisco123`.
  - Running configuration verified with encrypted password hashes.
