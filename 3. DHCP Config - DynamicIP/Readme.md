# Cisco IOS DHCP Server Deployment & Dynamic Host Configuration

A centralized DHCP server simulation configured on a Cisco ISR 4321 router in Cisco Packet Tracer. Demonstrates automated IPv4 host addressing within a classless subnet block (`/28`), default gateway assignment, in-band switch management, and fundamental device access hardening.

---

## 📌 Topology Overview

![Topology](DHCP%20Config%20-%20DynamicIPs.png)

* **Router (Cisco ISR 4321):** Acts as the default gateway and hosts the Cisco IOS DHCP server pool (`cisco`).
* **Switch (Cisco Catalyst 2960-24TT):** Layer 2 distribution switch with configured in-band SVI management.
* **End Devices:** 7 client workstations dynamically leasing IP parameters from the router's local DHCP scope.

---

## 📊 Subnet & DHCP Scope Plan

* **Network Address:** `192.168.16.0/28`
* **Subnet Mask:** `255.255.255.240` (`/28`)
* **Usable Host Range:** `192.168.16.1` – `192.168.16.14` (14 valid IPs)
* **Broadcast Address:** `192.168.16.15`
* **DHCP Pool Name:** `cisco`
* **Excluded Addresses:** `192.168.16.1` (Default Gateway) and `192.168.16.2` (Switch SVI)

| Device / Role | Interface | Assigned IP / Scope | Type | Purpose |
| :--- | :--- | :--- | :--- | :--- |
| **ISR 4321** | G0/0/0 | `192.168.16.1/28` | Static | Default Gateway (1st Valid IP) |
| **2960-24TT** | VLAN 1 (SVI) | `192.168.16.2/28` | Static | Switch Management Interface |
| **DHCP Pool** | Scope | `192.168.16.3` – `192.168.16.14` | Dynamic | Leased to PC-PT Endpoints |

---

## 🛠️ Key Technical Implementations

* **Cisco IOS DHCP Pool Configuration:** Automated network configuration provisioning default router (`192.168.16.1`) and optional DNS resolution (`8.8.8.8`).
* **DHCP Address Reservation:** Configured `ip dhcp excluded-address` to protect infrastructure IPs (gateway and switch SVI) from address duplication.
* **Device Security Baseline:**
  * Enabled encrypted privileged EXEC access (`enable password 12345`).
  * Secured Console line with local password authentication (`cisco`).