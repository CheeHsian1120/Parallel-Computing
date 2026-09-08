# EV Charging Network Simulation: Network Topology, Open MPI & OpenMP
[![Open MPI](https://img.shields.io/badge/Open%20MPI-4169E1?style=for-the-badge&logo=c&logoColor=white)](https://img.shields.io/badge/<LABEL>-<HEX_COLOR>?style=for-the-badge&logo=<LOGO_NAME>&logoColor=white)
[![OpenMP](https://img.shields.io/badge/OpenMP-033963?style=for-the-badge&logo=c&logoColor=white)](https://img.shields.io/badge/<LABEL>-<HEX_COLOR>?style=for-the-badge&logo=<LOGO_NAME>&logoColor=white)

## 📌 Overview

This project simulates an EV charging network by designing and analyzing a highly scalable hybrid network topology. It combines a 2D Cartesian Mesh for inter-node communication with a Star Topology for base station coordination. The architecture evaluates communication delays, bandwidth requirements, and multi-core scalability by integrating **Open MPI** for distributed global routing and **OpenMP** for localized parallel processing.

## 🚀 Key Achievements & Methodologies

### 1. Hybrid Network Topology Design

* **Geographical Realism:** Selected a 2D Mesh topology connecting adjacent EV charging nodes for localized status probing, limiting hardware connections to a maximum of 4 neighbors per node.
* **Low-Latency Alerts:** Utilized a Star topology overlay to connect all nodes directly to the Base Station for global redirection alerts, ensuring the powerful centralized server handles heavy traffic.
* **Topology Comparison:** Excluded pure Complete or Torus networks due to unrealistic physical wiring requirements, and rejected pure Star/Tree topologies to prevent fragile bottlenecks and sub-network disconnections.

### 2. Hybrid Architecture (Open MPI + OpenMP)

* **Distributed Global Routing:** Mapped the Base Station as a centralized server at Rank 0 and independent EV Charging Nodes to Ranks 1~N using Open MPI.
* **Zero-Overhead Local Processing:** Implemented OpenMP threads within each node to concurrently monitor charging port occupancy, completely eliminating network overhead for internal state checks via shared memory.
* **Smart Congestion Management:** Designed a multi-phase escalation protocol where nodes first probe immediate neighbors to resolve minor traffic locally, only alerting the Base Station for global redirection when completely overloaded.

### 3. Hardware & Bandwidth Optimization

* **Dedicated Core Allocation:** Assigned 91 dedicated CPU cores (90 for OpenMP threads across 9 nodes, 1 for the Base Station) to bypass OS context-switching overhead and maximize parallel efficiency.
* **Bandwidth Profiling:** Calculated maximum theoretical traffic during a simultaneous network-wide overload (66 messages/second). Determined that a minimal 1 Mbps Ethernet link easily accommodates the required ~0.34 Mbps throughput, even with a 10x safety margin.

### 4. Transmission Delay Analysis

* **O(1) Local Scalability:** Proved that local 2D Mesh probing transmission delay is bound by O(1) complexity (max 4.096 μs) because requests and replies are parallelized and strictly limited by the maximum node degree.
* **O(N/S) Global Bottleneck Resolution:** Identified a fan-in queueing bottleneck in the centralized Star topology O(N). Demonstrated that distributing the workload across S servers achieves an O(N/S) delay curve, finding an optimal balance of N/4 base stations to minimize latency without excessive hardware cost.

## 🔮 Future Work

* **Network Traffic Profiling:** Compare different alert frequencies to study how increased real-world network traffic and congestion events affect aggregate transmission times.
* **Payload & Bandwidth Scaling:** Test various message sizes and network bandwidth capacities to empirically evaluate their impact on physical transmission delays.

## 📊 Presentation & Documentation

For a more detailed breakdown of the topology matrices, UML class diagrams, delay visualizations, and hardware requirements, please check out the presentation slides:

👉 **[Presentation Slide.pdf](./Presentation%20Slide.pdf)**

## 👨‍💻 Author

Shee Seng Cheng  
Tay Chee Hsian
