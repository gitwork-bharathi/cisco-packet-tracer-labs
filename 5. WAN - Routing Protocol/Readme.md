# Multi-Site WAN Architecture: Static, Default, and Dynamic (OSPF) Routing

A multi-phase WAN routing lab configured in Cisco Packet Tracer. Built within a unified multi-branch physical topology, this lab demonstrates the step-by-step evolution of Layer 3 IP forwarding across regional sites: starting from explicit **Standard Static Routes**, transitioning to optimized **Default Routing** with an upstream ISP gateway, and finalizing with auto-discovering, scalable **OSPF Dynamic Routing**.

---

## 📌 Physical Topology & WAN Interconnects

The underlying topology connects three regional corporate offices (**LAN A, LAN B, and LAN C**) via point-to-point serial links, with LAN C providing an uplink to a simulated external ISP gateway:

* **Site A:** Cisco ISR 4321, 2960 Switch, 2 PCs | Terminates `192.168.10.0/29`
* **Site B:** Cisco ISR 4321, 2960 Switch, 2 PCs | Terminates `192.168.20.0/29`
* **Site C:** Cisco ISR 4321, 2960 Switch, 2 PCs | Terminates `192.168.30.0/29`
* **ISP Router:** Upstream Edge Gateway connected to Site C | `100.0.0.0/30`

---

## 📊 Subnet & WAN Addressing Plan

| Segment / Link | Subnet ID | Subnet Mask | Usable Range | Interface Assignments |
| :--- | :--- | :--- | :--- | :--- |
| **LAN A** | `192.168.10.0/29` | `255.255.255.248` | `192.168.10.1 - 192.168.10.6` | Router-A G0/0/0: `.1`, PCs: `.2 - .3` |
| **LAN B** | `192.168.20.0/29` | `255.255.255.248` | `192.168.20.1 - 192.168.20.6` | Router-B G0/0/0: `.1`, PCs: `.2 - .3` |
| **LAN C** | `192.168.30.0/29` | `255.255.255.248` | `192.168.30.1 - 192.168.30.6` | Router-C G0/0/0: `.1`, PCs: `.2 - .3` |
| **WAN 1 (A ↔ B)** | `192.168.100.0/30`| `255.255.255.252` | `192.168.100.1 - 192.168.100.2`| Router-A Se0/1/0: `.1`, Router-B Se0/1/0: `.2` |
| **WAN 2 (B ↔ C)** | `192.168.200.0/30`| `255.255.255.252` | `192.168.200.1 - 192.168.200.2`| Router-B Se0/1/1: `.1`, Router-C Se0/1/0: `.2` |
| **WAN 3 (C ↔ ISP)** | `100.0.0.0/30`    | `255.255.255.252` | `100.0.0.1 - 100.0.0.2`        | Router-C Se0/1/1: `.1`, ISP Se0/1/0: `.2` |

---

## 🛠️ Implementation Phases & Routing Logic

### Phase 1: Standard Static Routing
![Standard Static Routing](Standard%20Static%20Routing%20Protocol.png)

Every destination network is explicitly defined in each router's routing table using its direct next-hop IP address:

* **Router A (Stub Site):**
  ```text
  ip route 192.168.20.0 255.255.255.248 192.168.100.2
  ip route 192.168.30.0 255.255.255.248 192.168.100.2
  ip route 192.168.200.0 255.255.255.252 192.168.100.2
  ```
* **Router B (Transit Site):**
  ```text
  ip route 192.168.10.0 255.255.255.248 192.168.100.1
  ip route 192.168.30.0 255.255.255.248 192.168.200.2
  ```
* **Router C (Stub Site):**
  ```text
  ip route 192.168.10.0 255.255.255.248 192.168.200.1
  ip route 192.168.20.0 255.255.255.248 192.168.200.1
  ip route 192.168.100.0 255.255.255.252 192.168.200.1
  ```
### Phase 2: Default Static Routing (Table Reduction)
![Default Static Routing](Default%20Static%20Routing%20Protocol.png)

Reduces memory consumption and routing table size on stub and transit routers by substituting specific remote network entries with Quad-Zero (0.0.0.0/0) default routes toward upstream hubs and the border ISP:

* **Router A (Relies strictly on a default route pointing to Router B):**
  ```text
  ip route 0.0.0.0 0.0.0.0 192.168.100.2
  ```
* **Router B (Retains a static path to LAN A, with a default route forwarding all outside traffic to Router C):**
  ```text
  ip route 192.168.10.0 255.255.255.248 192.168.100.1
  ip route 0.0.0.0 0.0.0.0 192.168.200.2
  ```
* **Router C (Routes internal corporate traffic back into LAN A/B and injects a default route to the ISP):**
  ```text
  ip route 192.168.10.0 255.255.255.248 192.168.200.1
  ip route 192.168.20.0 255.255.255.248 192.168.200.1
  ip route 192.168.100.0 255.255.255.252 192.168.200.1
  ip route 0.0.0.0 0.0.0.0 100.0.0.2
  ```
### Phase 3: Dynamic Routing with Single-Area OSPFv2
![Dynamic Routing Protocol - OSPF](Dynamic%20Routing%20Protocol%20-%20OSPF.png)

Replaces manual static route administration with link-state dynamic discovery over Area 0. Eliminates human configuration errors, auto-calculates shortest path metrics using Dijkstra's algorithm, and secures edge host segments with passive interfaces:

* **Router A:**
  ```text
  router ospf 1
  router-id 1.1.1.1
  network 192.168.10.0 0.0.0.7 area 0
  network 192.168.100.0 0.0.0.3 area 0
  passive-interface GigabitEthernet0/0/0
  ```
* **Router B:**
  ```text
  router ospf 1
  router-id 2.2.2.2
  network 192.168.20.0 0.0.0.7 area 0
  network 192.168.100.0 0.0.0.3 area 0
  network 192.168.200.0 0.0.0.3 area 0
  passive-interface GigabitEthernet0/0/0
  ```
* **Router C:**
  ```text
  router ospf 1
  router-id 3.3.3.3
  network 192.168.30.0 0.0.0.7 area 0
  network 192.168.200.0 0.0.0.3 area 0
  passive-interface GigabitEthernet0/0/0
  ```
## 🔍 Comparative Analysis: Static vs. Default vs. Dynamic

| Metric / Feature | Standard Static | Default Static | Dynamic (OSPF) |
| :--- | :--- | :--- | :--- |
| **Routing Table Size** | Grows with every remote subnet | Minimal (1 default route on stubs) | Contains full link-state database for Area 0 |
| **CPU / RAM Overhead** | Negligible | Negligible | Moderate (Dijkstra SPF calculations) |
| **Network Scalability** | Low (Manual updates per subnet) | Medium (Best for simple hub-and-spoke) | High (Zero-touch automated discovery) |
| **Link Failure Handling** | None (Requires manual reroute) | None (Requires manual intervention) | Dynamic convergence & auto-failover |
| **Administrative Distance** | 1 | 1 | 110 |