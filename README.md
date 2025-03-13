# Multi-Protocol-Network-OSPF-EIGRP-and-BGP

This lab demonstrates the configuration of a multi-protocol environment using OSPF, EIGRP, and BGP. The goal was to ensure seamless communication between devices across different routing protocols, including route redistribution and troubleshooting connectivity issues.
---
![image](https://github.com/user-attachments/assets/77d73f66-5586-4129-9fd2-d8810fb03b85)

### Lab Topology
- **R1**: OSPF Area (connected to PC1 via `192.168.10.0/24`).
- **R2**: OSPF and EIGRP redistribution (connected to R1 and R3).
- **R3**: EIGRP and BGP (connected to R2 and R4).
- **R4**: BGP (connected to R3 via `10.1.34.0/30`).
- **PC1**: Connected to R1 (`192.168.10.10/24`).
- **PC2**: Connected to R3 (`192.168.20.10/24`).
---
 ### Configuration Steps
## OSPF Configuration
On R1
```bash
router ospf 1
 network 192.168.10.0 0.0.0.255 area 0
 network 10.1.12.0 0.0.0.3 area 0
```
On R2
```bash
router ospf 1
 network 10.1.12.0 0.0.0.3 area 0
```
## EIGRP Configurations
On R2
```bash
router eigrp 100
 network 10.1.23.0 0.0.0.3
 redistribute ospf 1 metric 10000 100 255 1 1500
```
On R3
```bash
router eigrp 100
 network 10.1.23.0 0.0.0.3
 network 192.168.20.0
```
##BGP Configurations
On R3 
```bash
router bgp 65001
 network 192.168.20.0
 neighbor 10.1.34.2 remote-as 65002
```
On R3
```bash
router bgp 65001
 network 192.168.20.0
 neighbor 10.1.34.2 remote-as 65002
```
On R4
```bash
router bgp 65002
 network 0.0.0.0
 neighbor 10.1.34.1 remote-as 65001
```
