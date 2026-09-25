# CCNA SRWE Resilient Campus Enterprise Network

![Project Status](https://img.shields.io/badge/status-completed-brightgreen)
![Cisco Packet Tracer](https://img.shields.io/badge/Cisco-Packet%20Tracer-blue)
![CCNA SRWE](https://img.shields.io/badge/course-CCNA%20SRWE-orange)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

## Project Overview

This project implements a highly available, multi-layer enterprise campus network designed and configured in Cisco Packet Tracer. 

Developed as part of the **CCNA Switching, Routing, and Wireless Essentials (SRWE)** curriculum, the main focus was to build a resilient, secure, and scalable infrastructure capable of withstanding single-point-of-failure scenarios through Layer 2 and Layer 3 redundancy.

### Key Capabilities & Technologies
- **Layer 2 Security & Isolation:** VLAN segmentation, Port-Security with Sticky MAC bindings, 802.1Q Trunking with native VLAN hardening (`VLAN 999`), and explicit port shutdown on unused interfaces[cite: 7, 8, 9, 10].
- **High Availability & Redundancy:** Rapid PVST+ (RSTP) root bridge primary/secondary deployment, LACP EtherChannel link aggregation (`Port-channel1`), and PortFast with BPDU Guard[cite: 7, 8, 9, 10].
- **Inter-VLAN Routing & Core Services:** Multilayer Switching (SVI) with `ip routing`, DHCP Relay (`ip helper-address 10.20.40.10`), static default route to edge router (`10.20.254.1`), DNS resolution, and HTTP Web hosting.
- **Management & Access Control:** Remote SSH v2 encrypted access across dedicated management VLANs (`VLAN 999` native, `VLAN 99` IP management) with local authentication (`admin`) and MOTD banner restrictions[cite: 7, 8, 9, 10].

---

## Network Architecture & VLAN Design

The enterprise campus is structured around a **Collapsed Core/Distribution Layer** using multilayer switches (`DSW1-CORE`) for fast inter-VLAN routing and redundancy, feeding down to access layer switches (`ASW1-FLOOR1`, `ASW2-FLOOR2`, and `ASW3-SRV`) for physical port allocation[cite: 7, 8, 9, 10].

### VLAN Allocation Table

| VLAN ID | VLAN Name     | Description & Scope                     | Subnet / Gateway       | Access Ports / Devices |
|:-------:|:--------------|:----------------------------------------|:-----------------------|:-----------------------|
| **10**  | `ADMIN`       | Administration & Executive PCs          | `10.20.10.0/24`        | `ASW1`: Fa0/1-4, Fa0/9 |
| **20**  | `ENGINEERING` | Engineering PCs & PRN-FLOOR2 Printer    | `10.20.20.0/24`        | `ASW2`: Fa0/1-8, Fa0/9 |
| **30**  | `HR`          | Human Resources Department              | `10.20.30.0/24`        | `ASW1`: Fa0/5-8[cite: 8] |
| **40**  | `SERVERS`     | Central Services (DHCP, DNS, WEB)       | `10.20.40.0/24`        | `ASW3`: Fa0/1-2 |
| **50**  | `GUEST`       | Restricted Guest Access                 | `10.20.50.0/24`        | `ASW1`: Fa0/10[cite: 8] |
| **60**  | `CORP-WIFI`   | Corporate Wireless Access Points        | `10.20.60.0/24`        | `ASW1`/`ASW2`: Fa0/10[cite: 7, 8] |
| **99**  | `MANAGEMENT`  | Network Device Management (SSH)         | `10.20.99.0/24`        | `ASW3`: Fa0/3 \| SVIs[cite: 7, 8, 9, 10] |
| **999** | `NATIVE-BLACK`| Blackhole Native VLAN for Trunks/Unused | Unused                 | Unused Ports[cite: 7, 8, 9, 10] |

---

## Device Startup Configurations

### 1. DSW1-CORE Startup Configuration

```text
!
version 16.3.2
no service timestamps log datetime msec
no service timestamps debug datetime msec
service password-encryption
!
hostname DSW1-CORE
!
no profinet
enable secret 5 $1$mERr$16XWnNeROA.vjE/XZL8LP1
!
ip routing
!
username admin privilege 15 secret 5 $1$mERr$AFX/pZT1Lh7NP3Dp3P/qq/
!
ip ssh version 2
no ip domain-lookup
ip domain-name campus.local
!
spanning-tree mode rapid-pvst
spanning-tree vlan 10,20,30,40,50,60,99,999 priority 24576
!
interface Port-channel1
 switchport access vlan 999
 switchport trunk native vlan 999
 switchport trunk allowed vlan 10,20,30,40,50,60,99,999
 switchport mode trunk
!
interface GigabitEthernet1/0/1
 description Uplink routed to R1-EDGE
 no switchport
 ip address 10.20.254.2 255.255.255.252
 duplex auto
 speed auto
!
interface GigabitEthernet1/0/2
 description Trunk para ASW1-FLOOR1
 switchport trunk native vlan 999
 switchport trunk allowed vlan 10,30,50,99,999
 switchport mode trunk
!
interface GigabitEthernet1/0/3
 description Trunk para ASW2-FLOOR2
 switchport trunk native vlan 999
 switchport trunk allowed vlan 20,60,99,999
 switchport mode trunk
!
interface GigabitEthernet1/0/4
 description Trunk para ASW3-SERVERS
 switchport trunk native vlan 999
 switchport trunk allowed vlan 40,99,999
 switchport mode trunk
!
interface GigabitEthernet1/0/5
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet1/0/6
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet1/0/7
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet1/0/8
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet1/0/9
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet1/0/10
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet1/0/11
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet1/0/12
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet1/0/13
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet1/0/14
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet1/0/15
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet1/0/16
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet1/0/17
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet1/0/18
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet1/0/19
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet1/0/20
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet1/0/21
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet1/0/22
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet1/0/23
 switchport access vlan 999
 switchport trunk native vlan 999
 switchport trunk allowed vlan 10,20,30,40,50,60,99,999
 switchport mode trunk
 channel-protocol lacp
 channel-group 1 mode active
!
interface GigabitEthernet1/0/24
 switchport access vlan 999
 switchport trunk native vlan 999
 switchport trunk allowed vlan 10,20,30,40,50,60,99,999
 switchport mode trunk
 channel-protocol lacp
 channel-group 1 mode active
!
interface Vlan1
 no ip address
 shutdown
!
interface Vlan10
 mac-address 0005.5e5b.3501
 ip address 10.20.10.1 255.255.255.0
 ip helper-address 10.20.40.10
!
interface Vlan20
 mac-address 0005.5e5b.3502
 ip address 10.20.20.1 255.255.255.0
 ip helper-address 10.20.40.10
!
interface Vlan30
 mac-address 0005.5e5b.3503
 ip address 10.20.30.1 255.255.255.0
 ip helper-address 10.20.40.10
!
interface Vlan40
 mac-address 0005.5e5b.3504
 ip address 10.20.40.1 255.255.255.0
!
interface Vlan50
 mac-address 0005.5e5b.3505
 ip address 10.20.50.1 255.255.255.0
 ip helper-address 10.20.40.10
!
interface Vlan60
 mac-address 0005.5e5b.3506
 ip address 10.20.60.1 255.255.255.0
 ip helper-address 10.20.40.10
!
interface Vlan99
 mac-address 0005.5e5b.3507
 ip address 10.20.99.11 255.255.255.0
!
ip classless
ip route 0.0.0.0 0.0.0.0 10.20.254.1 
!
banner motd #Restricted access - just authorized people#
!
line con 0
 logging synchronous
!
line vty 0 4
 login local
 transport input ssh
line vty 5 15
 login local
 transport input ssh
!
end