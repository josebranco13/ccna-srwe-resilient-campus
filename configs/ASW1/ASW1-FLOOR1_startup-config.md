# CCNA SRWE Resilient Campus Enterprise Network

![Project Status](https://img.shields.io/badge/status-completed-brightgreen)
![Cisco Packet Tracer](https://img.shields.io/badge/Cisco-Packet%20Tracer-blue)
![CCNA SRWE](https://img.shields.io/badge/course-CCNA%20SRWE-orange)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

## Project Overview

This project implements a highly available, multi-layer enterprise campus network designed and configured in Cisco Packet Tracer. 

Developed as part of the **CCNA Switching, Routing, and Wireless Essentials (SRWE)** curriculum, the main focus was to build a resilient, secure, and scalable infrastructure capable of withstanding single-point-of-failure scenarios through Layer 2 and Layer 3 redundancy.

### Key Capabilities & Technologies
- **Layer 2 Security & Isolation:** VLAN segmentation, Port-Security with Sticky MAC bindings, 802.1Q Trunking with native VLAN hardening (`VLAN 999`), and explicit port shutdown on unused interfaces (`Fa0/11-24`)[cite: 7, 8].
- **High Availability & Redundancy:** Rapid PVST+ (RSTP) root bridge primary/secondary deployment, PortFast with BPDU Guard, and EtherChannel link aggregation[cite: 7, 8].
- **Inter-VLAN Routing & Services:** Multilayer Switching (SVI) with `ip routing`, DHCP Relay (`ip helper-address`), DNS resolution, and HTTP Web hosting.
- **Management & Access Control:** Remote SSH v2 encrypted access across dedicated management VLANs (`VLAN 999` native, `VLAN 99` IP management) with local authentication (`admin`) and MOTD banner restrictions[cite: 7, 8].

---

## Network Architecture & VLAN Design

The enterprise campus is structured around a **Collapsed Core/Distribution Layer** using multilayer switches for fast inter-VLAN routing and redundancy, feeding down to access layer switches (`ASW1-FLOOR1` and `ASW2-FLOOR2`) for physical port allocation[cite: 7, 8].

### VLAN Allocation Table

| VLAN ID | VLAN Name     | Description & Scope                     | Subnet / Gateway       | Access Ports / Devices |
|:-------:|:--------------|:----------------------------------------|:-----------------------|:-----------------------|
| **10**  | `ADMIN`       | Administration & Executive PCs          | `10.20.10.0/24`        | `ASW1`: Fa0/1-4, Fa0/9 |
| **20**  | `ENGINEERING` | Engineering PCs & PRN-FLOOR2 Printer    | `10.20.20.0/24`        | `ASW2`: Fa0/1-8, Fa0/9 |
| **30**  | `HR`          | Human Resources Department              | `10.20.30.0/24`        | `ASW1`: Fa0/5-8[cite: 8] |
| **40**  | `SERVERS`     | Central Services (DHCP, DNS, WEB)       | `10.20.40.0/24`        | Server Switch |
| **50**  | `GUEST`       | Restricted Guest Access                 | `10.20.50.0/24`        | `ASW1`: Fa0/10[cite: 8] |
| **60**  | `CORP-WIFI`   | Corporate Wireless Access Points        | `10.20.60.0/24`        | `ASW1`/`ASW2`: Fa0/10[cite: 7, 8] |
| **99**  | `MANAGEMENT`  | Network Device Management (SSH)         | `10.20.99.0/24`        | SVI Interfaces[cite: 7, 8] |
| **999** | `NATIVE-BLACK`| Blackhole Native VLAN for Trunks/Unused | Unused                 | `ASW1`/`ASW2`: Fa0/11-24[cite: 7, 8] |

---

## Device Startup Configurations

### 1. ASW1-FLOOR1 Startup Configuration

```text
!
version 15.0
no service timestamps log datetime msec
no service timestamps debug datetime msec
service password-encryption
!
hostname ASW1-FLOOR1
!
enable secret 5 $1$mERr$16XWnNeROA.vjE/XZL8LP1
!
ip ssh version 2
no ip domain-lookup
ip domain-name campus.local
!
username admin secret 5 $1$mERr$slUmKS/I4gB0NqnKSWU5y0
!
spanning-tree mode rapid-pvst
spanning-tree extend system-id
!
interface FastEthernet0/1
 switchport access vlan 10
 switchport mode access
 switchport port-security
 switchport port-security mac-address sticky 
 spanning-tree portfast
 spanning-tree bpduguard enable
!
interface FastEthernet0/2
 switchport access vlan 10
 switchport mode access
 switchport port-security
 switchport port-security mac-address sticky 
 spanning-tree portfast
 spanning-tree bpduguard enable
!
interface FastEthernet0/3
 switchport access vlan 10
 switchport mode access
 switchport port-security
 switchport port-security mac-address sticky 
 spanning-tree portfast
 spanning-tree bpduguard enable
!
interface FastEthernet0/4
 switchport access vlan 10
 switchport mode access
 switchport port-security
 switchport port-security mac-address sticky 
 spanning-tree portfast
 spanning-tree bpduguard enable
!
interface FastEthernet0/5
 switchport access vlan 30
 switchport mode access
 switchport port-security
 switchport port-security mac-address sticky 
 spanning-tree portfast
 spanning-tree bpduguard enable
!
interface FastEthernet0/6
 switchport access vlan 30
 switchport mode access
 switchport port-security
 switchport port-security mac-address sticky 
 spanning-tree portfast
 spanning-tree bpduguard enable
!
interface FastEthernet0/7
 switchport access vlan 30
 switchport mode access
 switchport port-security
 switchport port-security mac-address sticky 
 spanning-tree portfast
 spanning-tree bpduguard enable
!
interface FastEthernet0/8
 switchport access vlan 30
 switchport mode access
 switchport port-security
 switchport port-security mac-address sticky 
 spanning-tree portfast
 spanning-tree bpduguard enable
!
interface FastEthernet0/9
 switchport access vlan 10
 switchport mode access
 switchport port-security
 switchport port-security mac-address sticky 
 spanning-tree portfast
 spanning-tree bpduguard enable
!
interface FastEthernet0/10
 switchport access vlan 50
 switchport mode access
 switchport port-security
 switchport port-security maximum 10
 switchport port-security mac-address sticky 
 switchport port-security violation restrict 
 switchport port-security mac-address sticky 000A.411D.13CE
 spanning-tree portfast
 spanning-tree bpduguard enable
!
interface FastEthernet0/11
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface FastEthernet0/12
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface FastEthernet0/13
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface FastEthernet0/14
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface FastEthernet0/15
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface FastEthernet0/16
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface FastEthernet0/17
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface FastEthernet0/18
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface FastEthernet0/19
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface FastEthernet0/20
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface FastEthernet0/21
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface FastEthernet0/22
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface FastEthernet0/23
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface FastEthernet0/24
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet0/1
 switchport trunk native vlan 999
 switchport trunk allowed vlan 10,30,50,99,999
 switchport mode trunk
!
interface GigabitEthernet0/2
 switchport trunk native vlan 999
 switchport trunk allowed vlan 10,30,50,99,999
 switchport mode trunk
!
interface Vlan1
 no ip address
 shutdown
!
interface Vlan10
 no ip address
!
interface Vlan99
 ip address 10.20.99.21 255.255.255.0
!
ip default-gateway 10.20.99.11
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