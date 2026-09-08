# Dual-Switch Multi-Subnet Inter-LAN Routing

A dual-broadcast domain local network designed and simulated in Cisco Packet Tracer. Demonstrates multi-interface Layer 3 routing across discrete `/28` subnets, Switch Virtual Interface (SVI) management on Layer 2 Catalyst switches, and static default gateway traversal.

---

## 📌 Topology Overview

![Topology](Two%20Switch%20-%20StaticIPs.png)

* **Central Router (Cisco ISR 4321):** Operates as the Layer 3 boundary between two isolated subnets (`LAN-A` and `LAN-B`) using physical routed interfaces.
* **Access Switches (2x Cisco Catalyst 2960-24TT):** Provide Layer 2 switching for endpoints in each broadcast domain with in-band management SVIs (VLAN 1).
* **End Devices:** Six workstations split evenly across both subnets, configured with static addressing and default gateway pointers for inter-subnet communication.

---

## 📊 IP Addressing & Subnet Plan

Both LANs utilize a `/28` mask (`255.255.255.240`), providing 14 usable host addresses per broadcast domain:

| Segment | Subnet / Mask | Usable Range | Broadcast | Gateway Interface |
| :--- | :--- | :--- | :--- | :--- |
| **LAN-A (Left)** | `192.168.10.0/28` (`255.255.255.240`) | `192.168.10.1 - 192.168.10.14` | `192.168.10.15` | Router `Gig0/0/0` (`192.168.10.1`) |
| **LAN-B (Right)** | `192.168.20.0/28` (`255.255.255.240`) | `192.168.20.1 - 192.168.20.14` | `192.168.20.15` | Router `Gig0/0/1` (`192.168.20.1`) |

### Device Assignment Table

| Device Identifier | Physical / Logical Interface | Assigned IP Address | Subnet Mask | Default Gateway |
| :--- | :--- | :--- | :--- | :--- |
| **ISR4321 (Router)** | GigabitEthernet0/0/0 | `192.168.10.1` | `255.255.255.240` | N/A |
| **ISR4321 (Router)** | GigabitEthernet0/0/1 | `192.168.20.1` | `255.255.255.240` | N/A |
| **Switch 1 (LAN-A)** | VLAN 1 (Management SVI) | `192.168.10.2` | `255.255.255.240` | `192.168.10.1` |
| **Switch 2 (LAN-B)** | VLAN 1 (Management SVI) | `192.168.20.2` | `255.255.255.240` | `192.168.20.1` |
| **PC-PT (LAN-A)** | FastEthernet0 | `192.168.10.3` | `255.255.255.240` | `192.168.10.1` |
| **PC-PT (LAN-A)** | FastEthernet0 | `192.168.10.4` | `255.255.255.240` | `192.168.10.1` |
| **PC-PT (LAN-A)** | FastEthernet0 | `192.168.10.5` | `255.255.255.240` | `192.168.10.1` |
| **PC-PT (LAN-B)** | FastEthernet0 | `192.168.20.3` | `255.255.255.240` | `192.168.20.1` |
| **PC-PT (LAN-B)** | FastEthernet0 | `192.168.20.4` | `255.255.255.240` | `192.168.20.1` |
| **PC-PT (LAN-B)** | FastEthernet0 | `192.168.20.5` | `255.255.255.240` | `192.168.20.1` |

---

## 🛠️ Key Technical Implementations

* **Multi-Interface Inter-Subnet Routing:** Direct routed links on `Gig0/0/0` and `Gig0/0/1` provide hardware-speed default gateway services between separate broadcast domains.
* **In-Band Layer 2 Management:** Configured SVIs (`Vlan1`) and `ip default-gateway` statements on each Catalyst 2960, enabling remote management and diagnostics across subnet boundaries.
* **Deterministic Access Switching:** Configured dedicated Layer 2 access ports with automated address resolution (ARP) and Layer 3 forwarding.
