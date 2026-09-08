# Cisco IOS Management Plane Security: Telnet vs. SSH Configuration

A comparative infrastructure security lab simulated in Cisco Packet Tracer. Evaluates unencrypted in-band management (**Telnet**) versus cryptographically protected remote administration (**SSH v2**) across independent LAN environments using a custom classless IPv4 subnet block (`/29`).

---

## 📌 Topology Overview

![Topology](TELNET%20&%20SSH.png)

The topology consists of two distinct segments to demonstrate legacy vs. secured management plane controls:
1. **Telnet Segment (Left):** Cisco ISR 4321, 2960 switch, and 2 endpoint PCs configured with plain-text remote terminal sessions.
2. **SSH Segment (Right):** Cisco ISR 4321, 2960 switch, and 2 endpoint PCs configured with asymmetric RSA encryption and local database authentication.

---

## 📊 IP Addressing & Subnet Plan

* **Network Address:** `192.168.10.0/29`
* **Subnet Mask:** `255.255.255.248` (`/29`)
* **Host Bits Borrowed:** 5 bits ($2^3 - 2 = 6$ usable host addresses)
* **Usable Host Range:** `192.168.10.1` – `192.168.10.6`
* **Broadcast Address:** `192.168.10.7`

| Segment / Device | Interface | IP Address | Subnet Mask | Default Gateway | Management Type |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Telnet Router** | G0/0/0 | `192.168.10.1` | `255.255.255.248` | N/A | Telnet Server (Insecure) |
| **Telnet Switch** | VLAN 1 (SVI) | `192.168.10.2` | `255.255.255.248` | `192.168.10.1` | In-Band Switch Access |
| **Telnet PC-01** | Fa0 | `192.168.10.3` | `255.255.255.248` | `192.168.10.1` | Telnet Client Station |
| **Telnet PC-02** | Fa0 | `192.168.10.4` | `255.255.255.248` | `192.168.10.1` | Telnet Client Station |
| **SSH Router** | G0/0/0 | `192.168.10.1` | `255.255.255.248` | N/A | Secure Shell (SSH v2) |
| **SSH Switch** | VLAN 1 (SVI) | `192.168.10.2` | `255.255.255.248` | `192.168.10.1` | In-Band Switch Access |
| **SSH PC-01** | Fa0 | `192.168.10.3` | `255.255.255.248` | `192.168.10.1` | SSH Client Station |
| **SSH PC-02** | Fa0 | `192.168.10.4` | `255.255.255.248` | `192.168.10.1` | SSH Client Station |

---

## 🛠️ Security Configurations & Protocol Comparison

| Parameter | Telnet Implementation (Left) | SSH Implementation (Right) |
| :--- | :--- | :--- |
| **Transport Layer Security** | Cleartext (Vulnerable to packet sniffing) | Encrypted (RSA asymmetric key exchange) |
| **Authentication Scheme** | Line-level shared password | Local user database (`username`/`secret`) |
| **VTY Line Directives** | `transport input telnet` | `transport input ssh` |
| **Key Generation** | Not Required | `crypto key generate rsa` (1024-bit modulus) |
| **Credentials** | Line password: `telnet` | User: `admin`, Secret: `12345` |
| **Privileged EXEC** | `enable password 12345` | `enable password 12345` |