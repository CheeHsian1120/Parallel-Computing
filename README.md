# Parallel Computing

![C](https://img.shields.io/badge/C-00599C?style=for-the-badge&logo=c&logoColor=white)
![Bash](https://img.shields.io/badge/Bash-4EAA25?style=for-the-badge&logo=gnubash&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)

## 📌 Overview
This repository contains a collection of projects, labs, and applied simulations exploring parallel architectures, distributed-memory systems, and high-performance network topologies. The core focus is evaluating algorithmic efficiency, multi-core scalability, and network communication latency using **C, POSIX Threads, OpenMP, and Open MPI**. 

All implementations are benchmarked across high-performance environments, including Monash University's CAAS HPC Cluster and AWS ParallelCluster.

---

## 🗂️ Repository Structure & Modules

The repository is modularized into core conceptual labs and applied system simulations. *(Navigate to each directory for specific source code, SLURM scripts, and presentation slides).*

### 🧪 Core Labs (Coding Exercises)
*   [**Lab 01: Prime Generation (Shared Memory)**](./Lab_01/)
    *   **Tech:** Serial C, POSIX Threads, OpenMP.
    *   **Highlights:** Achieved a lock-free Pthreads design eliminating Mutex overhead. Surpassed Pthread limits using OpenMP `schedule(dynamic, 500)` to eliminate false cache-line sharing, achieving a 15.75x speedup on a 32-core architecture.
*   [**Lab 02: Prime Generation (Distributed Memory & Hybrid)**](./Lab_02/)
    *   **Tech:** Open MPI, Hybrid (Open MPI + OpenMP).
    *   **Highlights:** Implemented block-cyclic partitioning and zero-sorting reductions (`MPI_LOR`). Proved the reality gap in the Extended Amdahl's Law by demonstrating physical network switch saturation limits compared to theoretical scaling curves.
<!--
*   [**Lab 03: [Upcoming / Future Lab Placeholder]**](./Lab_03/)
    *   *Reserved for future distributed computing concepts.*
-->

### 🛠️ Applied Projects (Theory Exercises)
*   [**Applied 01: EV Charging Network Topology Simulation**](./Applied_01/)
    *   **Tech:** Hybrid Architecture (Open MPI + OpenMP).
    *   **Highlights:** Architected a highly scalable network combining a 2D Cartesian Mesh (local O(1) probing) with a Star Topology (global routing). Overcame OS context-switching by dedicating 91 CPU cores, and calculated exact multi-phase transmission delays and 1 Mbps bandwidth safety margins.
<!--
*   [**Applied 02: [Upcoming / Future Project Placeholder]**](./Applied_02/)
    *   *Reserved for future applied HPC system simulations.*
-->

---



### Secure File Transfer (SCP)
Use `scp -r` to securely copy directories between your local machine and the clusters.
```bash
# To CAAS HPC
scp -r ./Lab_01 <credential>@student-caas-headnode.rep.monash.edu:~/Lab_01

# To AWS
scp -i <keyname>.pem -r ./Lab_01 <username>@<IP>:~/Lab_01
