# CCNA SRWE Resilient Campus Enterprise Network

![Project Status](https://img.shields.io/badge/status-completed-brightgreen)
![Cisco Packet Tracer](https://img.shields.io/badge/Cisco-Packet%20Tracer-blue)
![CCNA SRWE](https://img.shields.io/badge/course-CCNA%20SRWE-orange)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

## Project Overview

This project implements a highly available, multi-layer enterprise campus network designed and configured in **Cisco Packet Tracer**.

Developed as part of the **CCNA Switching, Routing, and Wireless Essentials (SRWE)** curriculum, the main objective was to build a resilient, secure, and scalable infrastructure capable of withstanding single-point-of-failure scenarios through **Layer 2 and Layer 3 redundancy**.

### Key Capabilities & Technologies

* **Layer 2 Security & Isolation**

  * VLAN segmentation
  * Port Security with Sticky MAC addresses
  * IEEE 802.1Q trunking
  * Native VLAN security using `VLAN 999`

* **High Availability & Redundancy**

  * Rapid PVST+ (RSTP)
  * Primary and Secondary Root Bridge configuration
  * EtherChannel link aggregation

* **Inter-VLAN Routing**

  * Multilayer switching using SVIs
  * `ip routing`
  * DHCP Relay using `ip helper-address`

* **Network Services**

  * Centralized IPv4 DHCP
  * DNS resolution
  * Internal HTTP web portal

* **Management & Access Control**

  * Secure SSH v2 remote access
  * Dedicated management VLAN
  * Secure network-device administration

---

## Network Architecture & VLAN Design

The enterprise campus follows a **Collapsed Core/Distribution Layer** architecture. Multilayer switches provide Layer 3 routing and redundancy, while access-layer switches provide endpoint connectivity and physical port allocation.

### VLAN Allocation

| VLAN ID | VLAN Name      | Description & Scope                     | Subnet / Gateway |
| :-----: | :------------- | :-------------------------------------- | :--------------- |
|  **10** | `ADMIN`        | Administration & Executive PCs          | `10.20.10.0/24`  |
|  **20** | `ENGINEERING`  | Engineering PCs & Local Network Printer | `10.20.20.0/24`  |
|  **30** | `HR`           | Human Resources Department              | `10.20.30.0/24`  |
|  **40** | `SERVERS`      | Central DHCP, DNS & Web Services        | `10.20.40.0/24`  |
|  **50** | `GUEST`        | Restricted Guest Access                 | `10.20.50.0/24`  |
|  **60** | `CORP-WIFI`    | Corporate Wireless Access Points        | `10.20.60.0/24`  |
|  **99** | `MANAGEMENT`   | Network Device Management & SSH         | `10.20.99.0/24`  |
| **999** | `NATIVE-BLACK` | Blackhole Native VLAN for Trunks        | Unused           |

---

## High Availability & Security

### Spanning Tree Protocol — Rapid PVST+

**Rapid PVST+** is implemented to prevent Layer 2 switching loops while providing rapid network convergence following topology changes.

The core switches are configured with primary and secondary root roles:

* **`DSW1-CORE`** is configured as the **Primary Root Bridge** for all VLANs:

  ```text
  spanning-tree vlan 1-999 root primary
  ```

* **`DSW2-BACKUP`** is configured as the **Secondary Root Bridge**, providing redundancy if the primary core switch or its associated links become unavailable.

* Access-facing ports use:

  * **PortFast** — accelerates endpoint port transition to the forwarding state.
  * **BPDU Guard** — protects access ports against unexpected BPDU frames and unauthorized switch connections.

This design provides faster convergence and reduces the impact of Layer 2 failures.

---

### EtherChannel

EtherChannel is used to aggregate multiple physical links into a single logical connection.

This provides:

* Increased available bandwidth
* Link-level redundancy
* Improved resilience against individual physical-link failures
* Simplified STP topology management

---

### Port Security

Access ports on **`ASW1-FLOOR1`** and **`ASW2-FLOOR2`** implement Port Security using Sticky MAC address learning.

Example configuration:

```text
switchport port-security
switchport port-security mac-address sticky
```

This allows the switches to dynamically learn and bind legitimate endpoint MAC addresses to their respective access ports.

Port-security violations can be configured to trigger restrictive actions such as interface shutdown or traffic restriction according to the security policy.

---

## Network Services

The centralized server infrastructure is located within **VLAN 40 — `SERVERS`**.

### DHCP

The DHCP server provides automatic IPv4 network configuration to clients across multiple VLANs.

The central DHCP server is:

```text
10.20.40.10
```

Because DHCP broadcasts do not normally cross Layer 3 boundaries, the multilayer switches use DHCP relay:

```text
ip helper-address 10.20.40.10
```

This allows clients in different VLANs to obtain their IP configuration from the centralized DHCP service.

---

### DNS

An internal DNS service provides name resolution for the enterprise network.

The configured domain used for testing is:

```text
campus.local
```

This allows internal resources to be accessed using hostnames instead of relying exclusively on IP addresses.

---

### HTTP Web Server

An internal HTTP server provides a web portal accessible from client devices across the network.

The service demonstrates successful:

* Layer 3 connectivity
* DNS resolution
* HTTP communication
* Inter-VLAN routing

---

## Management & Secure Access

Network infrastructure devices are managed through a dedicated management VLAN:

```text
VLAN 99 — MANAGEMENT
10.20.99.0/24
```

Remote administration is performed using **SSH version 2**, providing encrypted management sessions between administrators and network devices.

The secondary distribution switch, **`DSW2-BACKUP`**, was successfully tested through SSH to validate remote management functionality.

---

## Functional Verification

The project includes visual evidence demonstrating the operation and verification of the implemented infrastructure.

All verification screenshots are stored in the `evidences/` directory.

### Evidence Files

```text
evidences/
├── dhcp_service.png
├── dns_service.png
├── http_service.png
├── ev1_backup.png
├── pc1_admin-to_pc_eng.png
├── pc1_admin-to_pc_hr.png
├── pc1_eng-to_pc_admin.png
├── pc1_hr-to_pc_admin.png
└── ssh_dsw2.png
```

### Verification Summary

| Test                | Purpose                                  |   Result   |
| :------------------ | :--------------------------------------- | :--------: |
| DHCP Service        | Verify automatic IPv4 address allocation | ✅ Verified |
| DNS Service         | Verify `campus.local` name resolution    | ✅ Verified |
| HTTP Service        | Verify internal web portal accessibility | ✅ Verified |
| ADMIN → ENGINEERING | Verify inter-VLAN routing                | ✅ Verified |
| ADMIN → HR          | Verify inter-VLAN routing                | ✅ Verified |
| ENGINEERING → ADMIN | Verify bidirectional routing             | ✅ Verified |
| HR → ADMIN          | Verify bidirectional routing             | ✅ Verified |
| SSH → DSW2          | Verify secure remote management          | ✅ Verified |
| DSW2 Failover       | Verify redundancy and backup operation   | ✅ Verified |

---

## Key Verification Evidence

### 1. Network Services — DHCP & HTTP

Client computers dynamically obtain their IPv4 configuration from the centralized DHCP server located at:

```text
10.20.40.10
```

DHCP relay functionality enables clients in different VLANs to communicate with the centralized DHCP service.

The HTTP service was also tested through an internal client browser to verify web-server accessibility.

---

### 2. DNS Resolution

The internal DNS service was tested using the `campus.local` domain.

Successful name resolution confirms communication between clients and the centralized DNS infrastructure.

---

### 3. Inter-VLAN Connectivity

Layer 3 connectivity was validated between endpoints belonging to different VLANs.

The following communication paths were tested:

```text
ADMIN ↔ ENGINEERING
ADMIN ↔ HR
```

Successful ICMP communication confirms that the multilayer switches are correctly performing inter-VLAN routing.

---

### 4. Management & High Availability

SSH version 2 connectivity was tested against the secondary distribution switch:

```text
DSW2-BACKUP
```

The redundancy configuration was also tested to demonstrate the availability of the backup core infrastructure.

---

## Project Structure

```text
ccna-srwe-resilient-campus/
├── configs/
│   ├── ASW1/
│   │   ├── ASW1-FLOOR1_startup-config.md
│   │   └── ASW1-FLOOR1_startup-config.txt
│   ├── ASW2/
│   │   ├── ASW2-FLOOR2_startup-config.md
│   │   └── ASW2-FLOOR2_startup-config.txt
│   ├── ASW3/
│   │   ├── ASW3-SRV_startup-config.md
│   │   └── ASW3-SRV_startup-config.txt
│   ├── DSW1/
│   │   ├── DSW1-CORE_startup-config.md
│   │   └── DSW1-CORE_startup-config.txt
│   ├── DSW2/
│   │   ├── DSW2-BACKUP_startup-config.md
│   │   └── DSW2-BACKUP_startup-config.txt
│   └── R1/
│       ├── R1-EDGE_startup-config.md
│       └── R1-EDGE_startup-config.txt
├── evidences/
│   ├── dhcp_service.png          # Successful IP lease allocation via DHCP Relay
│   ├── dns_service.png           # Domain name resolution test (campus.local)
│   ├── ev1_backup.png            # Failover demonstration / DSW2 redundancy status
│   ├── http_service.png          # Portal Web HTTP rendering from internal browser
│   ├── pc1_admin-to_pc_eng.png   # Inter-VLAN ICMP reachability (ADMIN -> ENG)
│   ├── pc1_admin-to_pc_hr.png    # Inter-VLAN ICMP reachability (ADMIN -> HR)
│   ├── pc1_eng-to_pc_admin.png   # Inter-VLAN ICMP reachability (ENG -> ADMIN)
│   ├── pc1_hr-to_pc_admin.png    # Inter-VLAN ICMP reachability (HR -> ADMIN)
│   └── ssh_dsw2.png              # Remote management via SSH v2 to secondary core
├── .gitattributes                # Git configuration rules
├── README.md                     # Project documentation
└── resilient_campus.pkt          # Cisco Packet Tracer topology & configuration source
```

### File Description

| File / Directory       | Description                                    |
| :--------------------- | :--------------------------------------------- |
| `configs/`             | Device configurations (.txt and .md breakdowns)|
| `evidences/`           | Screenshots validating network functionality   |
| `.gitattributes`       | Git configuration rules                        |
| `resilient_campus.pkt` | Cisco Packet Tracer topology and configuration |
| `README.md`            | Project documentation                          |

---

## Technologies Used

The project was developed using the following technologies and networking concepts:

* **Cisco Packet Tracer**
* **Cisco IOS**
* **CCNA SRWE**
* **VLANs**
* **802.1Q Trunking**
* **Rapid PVST+**
* **EtherChannel**
* **Inter-VLAN Routing**
* **SVIs**
* **DHCP Relay**
* **IPv4 DHCP**
* **DNS**
* **HTTP**
* **SSH v2**
* **Port Security**
* **PortFast**
* **BPDU Guard**

---

## Learning Objectives

This project demonstrates practical knowledge of enterprise switching and routing concepts, including:

1. Designing a segmented enterprise campus network.
2. Implementing VLAN-based network isolation.
3. Configuring trunk links between switches.
4. Implementing redundant Layer 2 paths using Rapid PVST+.
5. Configuring primary and secondary root bridges.
6. Implementing EtherChannel for link aggregation and redundancy.
7. Configuring multilayer switching and inter-VLAN routing.
8. Implementing centralized DHCP services with DHCP relay.
9. Configuring internal DNS and HTTP services.
10. Securing access ports using Port Security.
11. Protecting access ports using PortFast and BPDU Guard.
12. Implementing secure remote management using SSH v2.
13. Testing network resilience and service availability.

---

## License

This project is open source and available under the [MIT License](LICENSE).

---

## Disclaimer

This project was developed for **educational purposes** as part of a portfolio demonstrating enterprise networking and Cisco networking skills.

Cisco, Cisco IOS, Cisco Networking Academy, and Cisco Packet Tracer are trademarks of Cisco Systems, Inc. This project is not affiliated with or endorsed by Cisco Systems, Inc.
