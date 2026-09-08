# Cisco Network Engineering & Infrastructure Lab Portfolio

A collection of network design, configuration, and troubleshooting simulations built in **Cisco Packet Tracer**. This repository covers core enterprise network engineering concepts, including Layer 2 switching optimization, dynamic Layer 3 routing, wireless LAN integration, IPv6 addressing, access security, and automated IP services.

---

## 🗺️ Lab Index & Technical Coverage

| Lab Category | Core Concepts & Protocols | Key Hardware / Devices |
| :--- | :--- | :--- |
| **Layer 2 Redundancy** | EtherChannel (LACP/PAgP), Link Aggregation, STP Load Balancing | Cisco Catalyst 2960
| **Segmentation & Trunking** | 802.1Q Trunking, VLAN Segmentation, VTP, Inter-VLAN Routing (ROAS) | ISR 4321, Catalyst 2960
| **IP Services & Security** | Cisco IOS DHCP Server, Helper Addresses, Password Recovery, ROMMON | Cisco Routers & Switches
| **WLAN Integration** | WPA2-Personal Security, SSID Deployment, Wireless Router Setup | WRT300N Wireless AP, Endpoints
| **Next-Gen Addressing** | IPv6 Global Unicast, SLAAC, Link-Local, Neighbor Discovery (NDP) | Cisco Routers, Dual-Stack PCs
| **Enterprise Capstone** | Multi-Branch WAN, OSPF, VLSM, PAT, Standard & Extended ACLs, SSH Hardening | Multi-site ISR 4321s, Switches, Server

---

## 🛠️ Key Skills Demonstrated

* **Routing Protocols & Topologies:** Single-area OSPF, default route injection to simulated ISPs, point-to-point serial and gigabit connections, Router-on-a-Stick (RoAS).
* **Switching & Layer 2 Technologies:** VLAN access/trunk assignment, VTP server/client domains, 802.1Q encapsulation, Port Aggregation (EtherChannel) for high-bandwidth fault tolerance.
* **Network Security & Access Policies:** Standard and Extended Access Control Lists (ACLs) to filter traffic by IP, host, and port level (e.g., HTTP/tcp port 80); NAT/PAT (Port Address Translation) overload.
* **Administrative Device Hardening:** Remote access via SSH v2 and Telnet with VTY timeouts and password encryption, privilege level controls, disabled domain lookup, and login warning banners.
* **IP Services & Management:** VLSM subnet planning, centralized Cisco IOS DHCP pool creation, static route configuration, configuration archival (`copy run start`).

---

## 📂 Featured Project: Enterprise Multi-Site Branch Network

The capstone project inside `enterprise-capstone/multi-site-enterprise-network` models an enterprise connecting four branches (Coimbatore, Chennai, Bangalore, and Delhi) across an ISP edge:

* **Efficient Addressing:** Implements a strict VLSM scheme derived from a `192.168.x.0/24` block, sizing subnets specifically for host counts of 55, 25, 11, and 9 hosts.
* **Granular Access Rules:** Enforces inter-departmental security policies, including isolating specific hosts across regions and blocking select hosts from reaching internal web servers while permitting standard ping verification.
* **Edge Address Translation:** Uses PAT to dynamically translate internal traffic across public WAN edges.

---

## 🚀 How to Use & Review These Labs

1. **Clone the Repository:**
   ```bash
   git clone [https://github.com/](https://github.com/)<your-username>/cisco-packet-tracer-labs.git
2. **Running the Labs:**
   * Open Cisco Packet Tracer (v8.0 or newer recommended).
   * Open the `.pkt` file inside the desired lab directory.
   * Run verification commands in CLI (`show ip route`, `show ip interface brief`, `show running-config`, or run end-to-end ping tests).
