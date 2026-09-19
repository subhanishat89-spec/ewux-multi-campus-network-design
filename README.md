
### EWUX University — Multi-Campus Network Design & Implementation

[![Cisco Packet Tracer](https://img.shields.io/badge/Simulator-Cisco%20Packet%20Tracer-blue)](https://www.netacad.com/about-networking-academy/packet-tracer)
[![Routing Protocol](https://img.shields.io/badge/Routing-RIPv2-green)](https://en.wikipedia.org/wiki/Routing_Information_Protocol)
[![Course](https://img.shields.io/badge/Course-CSE%20405%20Computer%20Networks-orange)](https://www.ewubd.edu/)

A complete multi-campus enterprise wired network infrastructure designed, implemented, and simulated in Cisco Packet Tracer for **CSE 405: Computer Networks** at East West University.

---

## 📌 Project Overview

This project models a centralized enterprise university network connecting seven geographically separated campus locations. The design utilizes a **Hub-and-Spoke (Star) topology** powered by a central core router and dynamic **RIP v2** routing to enable seamless inter-campus communication and optimal IP address allocation.

### Key Technical Highlights
* **Centralized Core Architecture:** Central Hub Router interconnects 7 remote campus routers via point-to-point serial links.
* **HWIC-2T Interface Expansion:** Expanded the Core Hub Router with `HWIC-2T` serial interface expansion modules to support multi-WAN connectivity.
* **Subnetting Strategy:** Class C `/24` subnets per campus LAN (`192.168.X.0/24`) paired with point-to-point `/30` WAN subnets (`10.0.X.0/30`) to eliminate IPv4 address waste.
* **Dynamic Routing:** RIP v2 configured across all 8 routers for automatic route discovery and inter-campus packet forwarding.
* **End-to-End Validation:** Fully verified via ICMP echo requests (`ping`), route propagation (`show ip route`), and path tracing (`tracert`).

---

## 📐 Network Architecture & Topology

```text
                            [ Campus 1 LAN ]
                            (192.168.1.0/24)
                                   |
                             ( Router 1 )
                                   |
                             10.0.1.0/30
                                   |
[ Campus 7 LAN ]                   |                   [ Campus 2 LAN ]
(192.168.7.0/24)                   |                   (192.168.2.0/24)
   ( Router 7 ) --- 10.0.7.0/30 ---+--- 10.0.2.0/30 --- ( Router 2 )
                                  / \
                                 /   \
 [ Campus 6 LAN ]               /     \               [ Campus 3 LAN ]
 (192.168.6.0/24) --- 10.0.6.0/30 [ HUB ROUTER ] --- 10.0.3.0/30 --- (192.168.3.0/24)
    ( Router 6 )                ( Router0 )              ( Router 3 )
                               /           \
                     10.0.5.0/30           10.0.4.0/30
                             /               \
              [ Campus 5 LAN ]               [ Campus 4 LAN ]
              (192.168.5.0/24)               (192.168.4.0/24)
                 ( Router 5 )                   ( Router 4 )

```

### Hardware Inventory & Device Summary

| Device Type | Model / Module | Quantity | Function / Description |
| --- | --- | --- | --- |
| **Core Hub Router** | Cisco Router + `HWIC-2T` | 1 | Central core node aggregating all 7 WAN connections. |
| **Campus Routers** | Cisco Router | 7 | Edge routers connecting campus LANs to the Core Hub. |
| **LAN Switches** | Cisco 2960 Switch | 7 | Access switches providing connectivity within each campus. |
| **Workstations** | Generic PC | 21 | 3 statically configured PCs per campus LAN. |
| **WAN Media** | Serial Cables | 7 | Point-to-point WAN links operating on `/30` subnets. |

---

## 🌐 IP Addressing Scheme

### 1. Campus Local Area Networks (LAN - `/24`)

| Campus | Network Subnet | Subnet Mask | Default Gateway | Static PC Host Range |
| --- | --- | --- | --- | --- |
| **Campus 1** | `192.168.1.0/24` | `255.255.255.0` | `192.168.1.1` | `192.168.1.10 – 192.168.1.12` |
| **Campus 2** | `192.168.2.0/24` | `255.255.255.0` | `192.168.2.1` | `192.168.2.10 – 192.168.2.12` |
| **Campus 3** | `192.168.3.0/24` | `255.255.255.0` | `192.168.3.1` | `192.168.3.10 – 192.168.3.12` |
| **Campus 4** | `192.168.4.0/24` | `255.255.255.0` | `192.168.4.1` | `192.168.4.10 – 192.168.4.12` |
| **Campus 5** | `192.168.5.0/24` | `255.255.255.0` | `192.168.5.1` | `192.168.5.10 – 192.168.5.12` |
| **Campus 6** | `192.168.6.0/24` | `255.255.255.0` | `192.168.6.1` | `192.168.6.10 – 192.168.6.12` |
| **Campus 7** | `192.168.7.0/24` | `255.255.255.0` | `192.168.7.1` | `192.168.7.10 – 192.168.7.12` |

### 2. WAN Serial Point-to-Point Links (`/30`)

| WAN Segment | Subnet Address | Subnet Mask | Core Hub IP | Campus Edge IP |
| --- | --- | --- | --- | --- |
| **Hub ↔ Campus 1** | `10.0.1.0/30` | `255.255.255.252` | `10.0.1.1` | `10.0.1.2` |
| **Hub ↔ Campus 2** | `10.0.2.0/30` | `255.255.255.252` | `10.0.2.1` | `10.0.2.2` |
| **Hub ↔ Campus 3** | `10.0.3.0/30` | `255.255.255.252` | `10.0.3.1` | `10.0.3.2` |
| **Hub ↔ Campus 4** | `10.0.4.0/30` | `255.255.255.252` | `10.0.4.1` | `10.0.4.2` |
| **Hub ↔ Campus 5** | `10.0.5.0/30` | `255.255.255.252` | `10.0.5.1` | `10.0.5.2` |
| **Hub ↔ Campus 6** | `10.0.6.0/30` | `255.255.255.252` | `10.0.6.1` | `10.0.6.2` |
| **Hub ↔ Campus 7** | `10.0.7.0/30` | `255.255.255.252` | `10.0.7.1` | `10.0.7.2` |

---

## ⚙️ Cisco IOS Router Configuration

### 1. Central Core Hub Router (Router0)

```cisconetwork
Router0# enable
Router0# configure terminal

! Interface Configuration - Serial WAN Links
Router0(config)# interface Serial0/0/0
Router0(config-if)# ip address 10.0.1.1 255.255.255.252
Router0(config-if)# clock rate 64000
Router0(config-if)# no shutdown
Router0(config-if)# exit

Router0(config)# interface Serial0/0/1
Router0(config-if)# ip address 10.0.2.1 255.255.255.252
Router0(config-if)# clock rate 64000
Router0(config-if)# no shutdown
Router0(config-if)# exit

! Configure RIP v2 Routing Protocol
Router0(config)# router rip
Router0(config-router)# version 2
Router0(config-router)# network 10.0.0.0
Router0(config-router)# network 192.168.0.0
Router0(config-router)# no auto-summary
Router0(config-router)# end
Router0# write memory

```

### 2. Sample Campus Edge Router (Campus 1 / Router1)

```cisconetwork
Router1# enable
Router1# configure terminal

! Local LAN Interface
Router1(config)# interface FastEthernet0/0
Router1(config-if)# ip address 192.168.1.1 255.255.255.0
Router1(config-if)# no shutdown
Router1(config-if)# exit

! Serial WAN Interface to Core Hub
Router1(config)# interface Serial0/0/0
Router1(config-if)# ip address 10.0.1.2 255.255.255.252
Router1(config-if)# no shutdown
Router1(config-if)# exit

! Configure RIP v2 Routing Protocol
Router1(config)# router rip
Router1(config-router)# version 2
Router1(config-router)# network 10.0.0.0
Router1(config-router)# network 192.168.1.0
Router1(config-router)# no auto-summary
Router1(config-router)# end
Router1# write memory

```

---

## 🧪 Verification & Testing

### 1. ICMP Ping Verification

```text
C:\> ping 192.168.5.10

Pinging 192.168.5.10 with 32 bytes of data:
Reply from 192.168.5.10: bytes=32 time=2ms TTL=125
Reply from 192.168.5.10: bytes=32 time=2ms TTL=125
Reply from 192.168.5.10: bytes=32 time=2ms TTL=125
Reply from 192.168.5.10: bytes=32 time=2ms TTL=125

Ping statistics for 192.168.5.10:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 2ms, Maximum = 2ms, Average = 2ms

```

### 2. Path Discovery (`tracert`)

```text
C:\> tracert 192.168.5.10

Tracing route to 192.168.5.10 over a maximum of 30 hops:
  1    0 ms    0 ms    0 ms    192.168.1.1  (Campus 1 Gateway)
  2    1 ms    0 ms    1 ms    10.0.1.1     (Core Hub WAN In)
  3    0 ms   15 ms    0 ms    10.0.5.2     (Campus 5 Router WAN Out)
  4    1 ms    1 ms    1 ms    192.168.5.10 (Target Host PC)

Trace complete.

```

### 3. Routing Table Status (`show ip route`)

```text
Router1# show ip route
Codes: C - connected, S - static, R - RIP, O - OSPF

Gateway of last resort is not set

     10.0.0.0/8 is variably subnetted, 7 subnets, 2 masks
C       10.0.1.0/30 is directly connected, Serial0/0/0
R       10.0.2.0/30 [120/1] via 10.0.1.1, 00:00:05, Serial0/0/0
R       10.0.3.0/30 [120/1] via 10.0.1.1, 00:00:05, Serial0/0/0
C       192.168.1.0/24 is directly connected, FastEthernet0/0
R       192.168.2.0/24 [120/2] via 10.0.1.1, 00:00:05, Serial0/0/0
R       192.168.3.0/24 [120/2] via 10.0.1.1, 00:00:05, Serial0/0/0

```

---

## ⚠️ Key Challenges & Solutions

* **Hardware Interface Shortage on Core Router:**
* **Problem:** Standard Cisco router models lacked enough built-in serial ports to support 7 point-to-point links.
* **Solution:** Modularly installed `HWIC-2T` (High-Speed WAN Interface Card) expansion modules on the central router.


* **Serial Interface Activation & Synchronization:**
* **Problem:** Serial interfaces remained inactive (`administratively down`) or failed to pass traffic.
* **Solution:** Explicitly configured `no shutdown` on all active interfaces and applied `clock rate 64000` on DCE WAN links.



---

## 🚀 Future Enhancements

* **OSPF / EIGRP Dynamic Routing:** Upgrade from distance-vector RIP v2 to link-state OSPF for faster convergence and higher scalability.
* **VLAN Segmentation:** Implement 802.1Q VLANs per campus to isolate student, faculty, and administrative traffic domains.
* **Network Services Infrastructure:** Integrate centralized DHCP for dynamic IP management and DNS for domain resolution.
* **Security Policies:** Configure Access Control Lists (ACLs) to filter traffic and enforce security parameters between campuses.

---

## 👤 Author & Academic Details

* **Author:** Nishat Subha Mithela



```

```
