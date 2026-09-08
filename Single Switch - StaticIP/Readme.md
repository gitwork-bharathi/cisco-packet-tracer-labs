# Single-Switch Subnetted LAN with Static IP Assignment

A foundational Local Area Network (LAN) deployment modeled in Cisco Packet Tracer. Demonstrates custom Class C Variable Length Subnet Masking (VLSM / CIDR), gateway configuration, Switch Virtual Interface (SVI) management, and static IP allocation across client/server endpoints.

---

## 📌 Topology Overview

![Topology](One%20Switch%20-%20StaticIPs.png)

* **Router (Cisco ISR 4321):** Acts as the default gateway terminating the local broadcast domain.
* **Switch (Cisco Catalyst 2960-24TT):** Layer 2 distribution switch with an active in-band management interface (VLAN 1).
* **Endpoints:** 1 local application server, 3 desktop PCs, and 1 laptop statically assigned within the designated subnet block.

---

## 📊 Subnet & Addressing Plan

* **Network Address:** `192.168.12.0/28`
* **Subnet Mask:** `255.255.255.240` (CIDR: `/28`)
* **Host Bits Borrowed:** 4 bits ($2^4 - 2 = 14$ usable addresses)
* **Usable Host Range:** `192.168.12.1` – `192.168.12.14`
* **Broadcast Address:** `192.168.12.15`

| Device | Interface | IP Address | Subnet Mask | Default Gateway | Function |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **ISR4321** | G0/0/0 | `192.168.12.1` | `255.255.255.240` | N/A | Default Gateway (1st Valid IP) |
| **2960-24TT** | VLAN 1 (SVI)| `192.168.12.2` | `255.255.255.240` | `192.168.12.1` | Switch In-band Management |
| **Server-PT** | FastEthernet0 | `192.168.12.3` | `255.255.255.240` | `192.168.12.1` | Central Application/File Server |
| **PC-PT (1)** | FastEthernet0 | `192.168.12.4` | `255.255.255.240` | `192.168.12.1` | Local Host Workstation |
| **PC-PT (2)** | FastEthernet0 | `192.168.12.5` | `255.255.255.240` | `192.168.12.1` | Local Host Workstation |
| **PC-PT (3)** | FastEthernet0 | `192.168.12.6` | `255.255.255.240` | `192.168.12.1` | Local Host Workstation |
| **Laptop-PT** | FastEthernet0 | `192.168.12.7` | `255.255.255.240` | `192.168.12.1` | Mobile Staff Station |

---

## 🛠️ Key Technical Implementations

* **Classless IPv4 Subnetting:** Right-sized address block (`/28`) minimizing unused host address wastage.
* **SVI Management:** Configured VLAN 1 IP addressing on the Layer 2 switch with an active default-gateway pointer for reachable in-band telemetry.
* **Full Layer 2 / Layer 3 Connectivity:** Deterministic point-to-point Layer 2 switching across Access ports with default gateway routing egress.
