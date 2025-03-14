# Multi-Protocol Network: OSPF, EIGRP, and BGP

In this lab, I set up a **multi-protocol network** using **OSPF, EIGRP, and BGP** to explore how different routing protocols can coexist in a real-world scenario. The primary goal was to ensure smooth communication between all devices, despite them running different protocols. To achieve this, I configured **route redistribution** between OSPF, EIGRP, and BGP while troubleshooting potential connectivity issues along the way.
---
![image](https://github.com/user-attachments/assets/77d73f66-5586-4129-9fd2-d8810fb03b85)

## Lab Topology

- **R1**: Runs **OSPF** and connects to **PC1** (`192.168.10.0/24`).
- **R2**: Acts as a **redistribution point** between **OSPF and EIGRP**, ensuring both protocols can exchange routes (connected to R1 and R3).
- **R3**: Runs **EIGRP and BGP**, redistributing routes between them (connected to R2 and R4).
- **R4**: Runs **BGP**, forming a BGP peering with R3 over the **10.1.34.0/30** network.
- **PC1**: Connected to R1 (`192.168.10.10/24`).
- **PC2**: Connected to R3 (`192.168.20.10/24`).

This setup mimics a **hybrid enterprise network**, where different departments or business units might use different routing protocols that need to interoperate seamlessly.
---
 ## Configuration Steps
 ### Step 1: Configure OSPF

#### On R1
```bash
router ospf 1
 network 192.168.10.0 0.0.0.255 area 0
 network 10.1.12.0 0.0.0.3 area 0
```
#### On R2
```bash
router ospf 1
 network 10.1.12.0 0.0.0.3 area 0
```
### Step 2: Configure EIGRP
#### On R2
```bash
router eigrp 100
 network 10.1.23.0 0.0.0.3
 redistribute ospf 1 metric 10000 100 255 1 1500
```
#### On R3
```bash
router eigrp 100
 network 10.1.23.0 0.0.0.3
 network 192.168.20.0
```
### Step 3: Configure BGP
#### On R3 
```bash
router bgp 65001
 network 192.168.20.0
 neighbor 10.1.34.2 remote-as 65002
```

#### On R4
```bash
router bgp 65002
 network 0.0.0.0
 neighbor 10.1.34.1 remote-as 65001
```
### Step 4: Redistribution Configurations
#### On R2 (OSPF to EIGRP Rediistribution)
```bash
router eigrp 100
 redistribute ospf 1 metric 10000 100 255 1 1500
```
#### On R3 (EIGRP to BGP Redistribution)
```bash
router bgp 65001
 redistribute eigrp 100
```
---
## Troubleshooting Steps
During the lab, I encountered a critical issue where PC1 could not ping R4 (10.1.34.2). Here’s how I resolved it:
#### Issue: PC1 Cannot Ping R4

- Root Cause: R4 did not have a route for 192.168.10.0/24 (PC1’s subnet).
- Solution: Added network 192.168.10.0 to BGP on R3 to ensure R4 learned the route.

![image](https://github.com/user-attachments/assets/36c68f69-0a16-4def-9bf9-134cf10fe876)
---
## Key Takeaways
 - Route Redistribution:
   - OSPF and EIGRP are redistributed on R2.
   - EIGRP and BGP are redistributed on R3.
   - This allows for seamless communication across different routing domains.

- Connectivity Verification:
   - Used ping and traceroute to verify end-to-end connectivity.
   - Checked the routing tables (show ip route) to confirm that all routes were properly learned.
   
- Troubleshooting Techniques:
   - Used show ip protocols to verify routing processes.
   - Ran debug ip routing to troubleshoot any redistribution issues.
   - Ensured no routing loops occurred by applying proper administrative distances where needed.
---
## Conclusion
This lab simulates a real-world multi-protocol network where OSPF, EIGRP, and BGP need to interconnect. Through route redistribution and careful troubleshooting, I was able to establish full connectivity across the entire network. This type of setup is commonly seen in enterprise environments where different teams or vendors use different routing protocols, requiring proper integration to maintain network stability and efficiency.



