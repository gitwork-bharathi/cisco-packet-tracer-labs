# Network Address Translation (NAT) & Port Address Translation (PAT)

A comparative IPv4 translation architecture simulated in Cisco Packet Tracer. Demonstrates how private RFC 1918 addresses are translated into routable public IP space using **Static NAT** (one-to-one), **Dynamic NAT** (pooled many-to-many), and **Port Address Translation (PAT / NAT Overload)** (many-to-one using Layer 4 port multiplexing).

---

## 📌 Topology Overview

![Topology](NAT%20&%20PAT.png)

The topology evaluates three distinct translation scenarios across identical physical layouts connecting a local access segment to an upstream ISP gateway (`100.0.0.0/30`):

1. **Static NAT (Left):** One-to-one permanent mapping for servers or dedicated workstations.
2. **Dynamic NAT (Center):** Shared address pool allocating public IPs on a first-come, first-served basis.
3. **PAT / NAT Overload (Right):** Single public interface address translating an entire subnet simultaneously using Layer 4 ephemeral ports.

---

## 📊 IP Addressing & Subnet Baseline

* **Local Private Subnet (Inside Local):** `192.168.50.0/29` (Mask: `255.255.255.248`)
* **Gateway IP:** `192.168.50.1`
* **Local Hosts:** `192.168.50.3`, `192.168.50.4`, `192.168.50.5`, `192.168.50.6`
* **Public WAN Link (Edge ↔ ISP):** `100.0.0.0/30` (Router: `100.0.0.1`, ISP: `100.0.0.2`)
* **Public Address Block (Inside Global):** `30.0.0.0/24`

---

## 🛠️ Implementation & Configuration Details

### 1. Static NAT (One-to-One Mapping)
Maps dedicated internal private IPs permanently to individual public IPs:

* `192.168.50.3` $\rightarrow$ `30.0.0.1`
* `192.168.50.4` $\rightarrow$ `30.0.0.2`
* `192.168.50.5` $\rightarrow$ `30.0.0.3`
* `192.168.50.6` $\rightarrow$ `30.0.0.4`

```text
Router(config)# interface GigabitEthernet0/0/0
Router(config-if)# ip nat inside
Router(config)# interface Serial0/1/0
Router(config-if)# ip nat outside

Router(config)# ip nat inside source static 192.168.50.3 30.0.0.1
Router(config)# ip nat inside source static 192.168.50.4 30.0.0.2
Router(config)# ip nat inside source static 192.168.50.5 30.0.0.3
Router(config)# ip nat inside source static 192.168.50.6 30.0.0.4
```
### 2. Dynamic NAT (Pooled Address Allocation)
Uses a defined pool of public addresses (30.0.0.1 - 30.0.0.2) assigned dynamically to internal hosts permitted by an Access Control List. Only two hosts can access the internet simultaneously:

```text
Router(config)# interface GigabitEthernet0/0/0
Router(config-if)# ip nat inside
Router(config)# interface Serial0/1/0
Router(config-if)# ip nat outside

Router(config)# access-list 1 permit 192.168.50.0 0.0.0.7
Router(config)# ip nat pool DYN_POOL 30.0.0.1 30.0.0.2 netmask 255.255.255.0
Router(config)# ip nat inside source list 1 pool DYN_POOL
```
### 3. Port Address Translation / PAT (Overload)
Multiplexes all internal hosts through a single public exit interface IP (Serial0/1/0 or a single global IP) using Layer 4 source port numbers, maximizing public IPv4 conservation:

```text
Router(config)# interface GigabitEthernet0/0/0
Router(config-if)# ip nat inside
Router(config)# interface Serial0/1/0
Router(config-if)# ip nat outside

Router(config)# access-list 1 permit 192.168.50.0 0.0.0.7
Router(config)# ip nat inside source list 1 interface Serial0/1/0 overload
```
## 🔍 Comparative Analysis
| Feature | Static NAT | Dynamic NAT | PAT |
| :--- | :--- | :--- | :--- |
| Mapping Ratio | 1:1 (Fixed) | 1:1 (Dynamic from Pool) | Many:1 (Port Multiplexed) |
| Public IP Utilization | High (1 per private host) | High (Pool size limits concurrency) | Extremely Low (1 public IP for many hosts) |
| Inbound Connection | Yes (Bidirectional) | No (Outbound-triggered only) | No (Requires Port Forwarding) |
| Primary Use Case | Web/Mail servers, public hosts | Temporary client sessions | Standard enterprise/home egress |