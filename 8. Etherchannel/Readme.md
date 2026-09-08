# Layer 2 EtherChannel Bundling: PAgP vs. LACP Configuration

A comprehensive Layer 2 link aggregation lab simulated in Cisco Packet Tracer. Compares Cisco-proprietary **Port Aggregation Protocol (PAgP)** and IEEE-standard **Link Aggregation Control Protocol (LACP - 802.3ad)** by bundling multiple physical FastEthernet connections into high-bandwidth, redundant logical Port-Channels.

---

## 📌 Topology Overview

![Topology](Etherchannel%20-%20PAGP,%20LACP.png)

The topology consists of two isolated pairs of Cisco Catalyst 2960 distribution switches:

1. **PAgP Channel Group (Left):**
   * **Switch 1 (S1):** Configured in `desirable` mode to actively initiate PAgP negotiations.
   * **Switch 2 (S2):** Configured in `auto` mode to passively listen and respond to incoming negotiation requests.
2. **LACP Channel Group (Right):**
   * **Switch 1 (S1):** Configured in `active` mode to initiate standard IEEE 802.3ad negotiation frames[cite: 16].
   * **Switch 2 (S2):** Configured in `passive` mode to respond only upon receiving active LACP negotiation packets[cite: 16].

---

## 🛠️ Port-Channel & Negotiation Modes

### 1. PAgP (Port Aggregation Protocol - Cisco Proprietary)
Combines physical links (`FastEthernet0/1 - 3`) into logical `Port-channel 1` using directional PAgP control frames:

* **S1 (Initiator - Desirable):**
  ```text
  Switch-1# configure terminal
  Switch-1(config)# interface range FastEthernet0/1 - 3
  Switch-1(config-if-range)# channel-protocol pagp
  Switch-1(config-if-range)# channel-group 1 mode desirable
  Switch-1(config-if-range)# no shutdown
  Switch-1(config)# interface Port-channel 1
  Switch-1(config-if)# switchport mode trunk
  ``
* **S1 (Responder - Auto):**
  ```text
  Switch-2# configure terminal
  Switch-2(config)# interface range FastEthernet0/1 - 3
  Switch-2(config-if-range)# channel-protocol pagp
  Switch-2(config-if-range)# channel-group 1 mode auto
  Switch-2(config-if-range)# no shutdown
  Switch-2(config)# interface Port-channel 1
  Switch-2(config-if)# switchport mode trunk
  ```
  
### 2. LACP (Link Aggregation Control Protocol - IEEE 802.3ad)
Bundles physical links (`FastEthernet0/1 - 3`) into logical `Port-channel 2` following open vendor-neutral standards:

* **S1 (Initiator - Active):**
  ```text
  Switch-1# configure terminal
  Switch-1(config)# interface range FastEthernet0/1 - 3
  Switch-1(config-if-range)# channel-protocol lacp
  Switch-1(config-if-range)# channel-group 2 mode active
  Switch-1(config-if-range)# no shutdown
  Switch-1(config)# interface Port-channel 2
  Switch-1(config-if)# switchport mode trunk
  ```
* **S1 (Responder - Passive):**
  ```text
  Switch-1# configure terminal
  Switch-1(config)# interface range FastEthernet0/1 - 3
  Switch-1(config-if-range)# channel-protocol lacp
  Switch-1(config-if-range)# channel-group 2 mode active
  Switch-1(config-if-range)# no shutdown
  Switch-1(config)# interface Port-channel 2
  Switch-1(config-if)# switchport mode trunk
  ```