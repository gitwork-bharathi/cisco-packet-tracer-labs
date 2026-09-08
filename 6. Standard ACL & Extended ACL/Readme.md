# Network Traffic Security: Standard vs. Extended Access Control Lists (ACLs)

A targeted policy-filtering lab built in Cisco Packet Tracer. Demonstrates granular traffic filtering at Layer 3 and Layer 4 using both **Standard Numbered ACLs** (source IP only) and **Extended Numbered ACLs** (source, destination, and Layer 4 port protocol), isolating endpoints and blocking specific web applications across routed subnets.

---

## 📌 Topology Overview

![Topology](Standard%20ACL,%20Extended%20ACL.png)

The topology models two distinct implementations across routed WAN connections (`100.0.0.0/30`):
1. **Standard ACL Implementation (Left):** Connects LAN A (`192.168.10.0/29`) to LAN B (`192.168.20.0/29`) to demonstrate destination-proximity source-based packet dropping.
2. **Extended ACL Implementation (Right):** Connects LAN A to LAN B (including `Server-PT` hosting Cisco Webpage over `tcp/80`) to demonstrate source-proximity Layer 3/4 filtering.

---

## 📊 Addressing & Security Policy Baseline

* **LAN A Subnet:** `192.168.10.0/29` (Subnet Mask: `255.255.255.248`)
* **LAN B Subnet:** `192.168.20.0/29` (Subnet Mask: `255.255.255.248`)
* **WAN Links:** `100.0.0.0/30` (Subnet Mask: `255.255.255.252`)

### Security Objectives
* **Standard ACL Policy:**
  * Host `192.168.10.3` must be **blocked** from accessing any host in LAN B.
  * All other devices in LAN A must retain full connectivity to LAN B.
* **Extended ACL Policies:**
  * Host `192.168.10.3` is **blocked** from communicating with host `192.168.20.3`.
  * Host `192.168.10.4` is **denied HTTP access** (`tcp/80`) to `Cisco Webpage` (`Server-PT`).
  * Host `192.168.10.3` is **permitted HTTP access** (`tcp/80`) to `Cisco Webpage`.

---

## 🛠️ Access Control Configurations & Architectural Placement

### 1. Standard ACL Implementation (Left Topology)
Standard ACLs (`1-99`) filter only on source IP addresses. By Cisco best practice, they are applied **closest to the destination** to avoid inadvertently blocking traffic destined for other networks.

Applied outbound on the destination router's LAN interface:

```text
Router-Dest(config)# access-list 10 deny host 192.168.10.3
Router-Dest(config)# access-list 10 permit any
Router-Dest(config)# interface GigabitEthernet0/0/0
Router-Dest(config-if)# ip access-group 10 out
```

### 2. Extended ACL Implementation (Right Topology)
Extended ACLs (100-199) inspect source IP, destination IP, and Layer 4 protocol ports (tcp/udp/icmp). They are placed closest to the source to drop unauthorized traffic immediately, preserving WAN bandwidth.

Applied inbound on the source router's local gateway interface:

```text
Router-Source(config)# access-list 100 deny ip host 192.168.10.3 host 192.168.20.3
Router-Source(config)# access-list 100 permit tcp host 192.168.10.3 host 192.168.20.2 eq 80
Router-Source(config)# access-list 100 deny tcp host 192.168.10.4 host 192.168.20.2 eq 80
Router-Source(config)# access-list 100 permit ip any any
Router-Source(config)# interface GigabitEthernet0/0/0
Router-Source(config-if)# ip access-group 100 in
```
## 🔍 Comparative Protocol Analysis

| Evaluation Metric | Standard ACL | Extended ACL |
| :--- | :--- | :--- |
| ACL Number Range | `1 - 99` and `1300 - 1999` | `100 - 199` and `2000 - 2699` |
| Criteria Evaluated | Source IP address only | Source IP, Destination IP, Protocol (TCP/UDP/ICMP), Port numbers |
| Filtering Granularity | Coarse (Entire subnet or host traffic dropped) | Deep packet inspection (Blocks HTTP, permits ICMP) |
| Placement Guideline | Closest to the destination | Closest to the source |
| WAN Bandwidth Impact | Inefficient (Unwanted traffic travels across WAN) | Efficient (Traffic discarded at edge before entering WAN) |