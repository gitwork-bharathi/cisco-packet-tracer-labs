# Multi-Switch VLAN Segmentation, VTP Pruning & Router-on-a-Stick Inter-VLAN Routing

A dual-scenario Layer 2/3 enterprise campus switching simulation in Cisco Packet Tracer. Demonstrates database propagation using **VLAN Trunking Protocol (VTP)**, 802.1Q encapsulation trunking across Catalyst 2960 switches, and full Layer 3 inter-departmental routing via **Router-on-a-Stick (RoAS)** subinterfaces on a Cisco ISR 4321.

---

## 📌 Topology Overview

![Topology](VLAN%20&%20Inter-VLAN.png)

The topology contrasts isolated Layer 2 switched domains with an integrated Layer 3 routed infrastructure:

1. **Isolated VLAN Domain (Left):**
   * **VTP Hierarchy:** 1 VTP Server switch and 2 VTP Client switches chained via 802.1Q trunks.
   * **Isolation Principle:** Strict Layer 2 broadcast boundary enforcement; same-VLAN devices communicate across switches, while cross-VLAN traffic is blocked without a Layer 3 gateway.
2. **Inter-VLAN Routed Domain (Right):**
   * **Router-on-a-Stick (RoAS):** A Cisco ISR 4321 terminating multiple 802.1Q subinterfaces over a single physical trunk link to the VTP Server switch.
   * **Inter-Department Routing:** Enables controlled Layer 3 transit between IT, HR, and Admin departments.

---

## 📊 Subnet & VLAN Allocation Plan

All subnets are engineered within classless `/29` variable-length blocks ($2^3 - 2 = 6$ usable host addresses):

| Department / Role | VLAN ID | Subnet / CIDR | Subnet Mask | Usable Host Range | Default Gateway |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **IT Department** | VLAN 10 | `192.168.10.0/29` | `255.255.255.248` | `192.168.10.1 - 192.168.10.6` | `192.168.10.1` |
| **HR Department** | VLAN 20 | `192.168.20.0/29` | `255.255.255.248` | `192.168.20.1 - 192.168.20.6` | `192.168.20.1` |
| **Admin Department**| VLAN 30 | `192.168.30.0/29` | `255.255.255.248` | `192.168.30.1 - 192.168.30.6` | `192.168.30.1` |
| **Management SVI** | VLAN 100| `192.168.100.0/29`| `255.255.255.248` | `192.168.100.1 - 192.168.100.6`| `192.168.100.1` |

---

## 🛠️ Configuration Details

### 1. VTP Domain & VLAN Database Provisioning
Configured on the **VTP Server** switch to automatically propagate VLANs 10, 20, 30, and 100 to all Client switches:

```text
VTP-Server(config)# vtp domain CCNA
VTP-Server(config)# vtp mode server
VTP-Server(config)# vtp password 123
VTP-Server(config)# enable secret server

VTP-Server(config)# vlan 10
VTP-Server(config-vlan)# name IT
VTP-Server(config)# vlan 20
VTP-Server(config-vlan)# name HR
VTP-Server(config)# vlan 30
VTP-Server(config-vlan)# name Admin
VTP-Server(config)# vlan 100
VTP-Server(config-vlan)# name Management
```

### 2. VTP Client Switches (Client 1 & Client 2)

```text
VTP-Client(config)# vtp domain CCNA
VTP-Client(config)# vtp mode client
VTP-Client(config)# vtp password 123
VTP-Client(config)# enable secret client1   ! (or client2 on Client 2)
```

### 3. Switch Trunking & Access Port Membership
Applied across all switches to tag traffic across inter-switch links and bind access interfaces to departments:

```text
! Inter-switch Trunk Links
Switch(config)# interface range GigabitEthernet0/1 - 2
Switch(config-if-range)# switchport mode trunk
Switch(config-if-range)# switchport trunk allowed vlan 10,20,30,100

! Access Edge Ports
Switch(config)# interface FastEthernet0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10

Switch(config)# interface FastEthernet0/2
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 20

Switch(config)# interface FastEthernet0/3
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 30
```

### 4. Router-on-a-Stick (RoAS) Inter-VLAN Routing
Configured on the ISR 4321 to route packets across subinterfaces using IEEE 802.1Q tags:

```text
ISR4321(config)# interface GigabitEthernet0/0/0
ISR4321(config-if)# no ip address
ISR4321(config-if)# no shutdown

ISR4321(config)# interface GigabitEthernet0/0/0.10
ISR4321(config-subif)# encapsulation dot1Q 10
ISR4321(config-subif)# ip address 192.168.10.1 255.255.255.248

ISR4321(config)# interface GigabitEthernet0/0/0.20
ISR4321(config-subif)# encapsulation dot1Q 20
ISR4321(config-subif)# ip address 192.168.20.1 255.255.255.248

ISR4321(config)# interface GigabitEthernet0/0/0.30
ISR4321(config-subif)# encapsulation dot1Q 30
ISR4321(config-subif)# ip address 192.168.30.1 255.255.255.248

ISR4321(config)# interface GigabitEthernet0/0/0.100
ISR4321(config-subif)# encapsulation dot1Q 100
ISR4321(config-subif)# ip address 192.168.100.1 255.255.255.248
```

## 🔍 Behavioral & Protocol Comparison
| Scenario | Intra-VLAN Traffic (e.g., IT to IT) | Inter-VLAN Traffic (e.g., IT to HR) | Routing Mechanism |
| :--- | :--- | :--- | :--- |
| VLAN Domain (Left) | Allowed: Tagged across 802.1Q trunks | Blocked: No Layer 3 routing engine | None (Layer 2 Switched Only) |
| VLAN Domain (Right) | Allowed: Local Layer 2 switching | Allowed: Routed via RoAS subinterfaces | Cisco ISR 4321 Router-on-a-Stick |