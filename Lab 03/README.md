# Electric Vehicle Charging Navigation System: Open MPI & OpenMP Hybrid Architecture
[![Open MPI](https://img.shields.io/badge/Open%20MPI-4169E1?style=for-the-badge&logo=c&logoColor=white)](https://img.shields.io/badge/<LABEL>-<HEX_COLOR>?style=for-the-badge&logo=<LOGO_NAME>&logoColor=white)
[![OpenMP](https://img.shields.io/badge/OpenMP-033963?style=for-the-badge&logo=c&logoColor=white)](https://img.shields.io/badge/<LABEL>-<HEX_COLOR>?style=for-the-badge&logo=<LOGO_NAME>&logoColor=white)

## 📌 Overview
This project extends from the [Applied 1](https://github.com/CheeHsian1120/Parallel-Computing/tree/main/Applied%2001) and uses a hybrid architecture combining **distributed-memory parallelism (Open MPI)** and **shared-memory parallelism (OpenMP)** to **simulate an Electric Vehicle Charging Navigation System (EVCNS)**. The system consists of multiple distributed charging nodes (configured in **2D Mesh** or **Line topologies**) and a centralized Base Station. The project evaluates different message-passing architectures—**Scheduled Blocking**, **Communication Overlap**, and **Fully Asynchronous Event-Driven**—assessing their performance and communication overhead during network congestion, node inquiries, base station routing recommendations, and graceful shutdown sequences.

## 🚀 Key Achievements & Methodologies

### 1. Scheduled Blocking Architecture (Version 1)

* **Centralized Token Scheduling:** The Base Station issues a `MSG_QUERY_GRANT` token via round-robin, ensuring that congestion checks and communication between nodes are strictly serialized to guarantee a zero-deadlock, safe message-passing environment.
* **Multi-Threaded Port Simulation:** Each charging node deploys 7 OpenMP threads (6 port workers + 1 communication thread), utilizing shared memory and mutex locks to independently simulate dynamic port occupancy (default 70% busy / 30% free) without blocking network operations.

### 2. Communication Overlap Architecture (Version 2)

* **Concurrent Non-Blocking Inquiries:** Deprecates the V1 token system, allowing nodes to utilize non-blocking `MPI_Isend` and `MPI_Irecv` to concurrently poll all physical neighbors for availability.
* **Hiding Network Latency:** While waiting for their own network requests to resolve (via `MPI_Testall`), nodes can simultaneously intercept and respond to inquiries from other peers, effectively hiding communication latency.

### 3. Fully Asynchronous Event-Driven Architecture (Version 3)

* **Persistent Peer State Machine:** Implements a persistent Peer structure array at the Base Station, utilizing a fully non-blocking event loop (`MPI_Iprobe`, `MPI_Test`) to independently track and process all concurrent requests across the grid without stalling execution.
* **Complete Asynchronous Progression:** Achieves full computation-communication overlap for congestion reporting and routing recommendations; data transmission from any node will not block the progression of the main control loop.

### 4. Manhattan Diamond Search Optimization
* **Layer-by-Layer Spatial Expansion:** When a local area is severely congested, the Base Station expands outward in a diamond pattern using Manhattan distance (starting from `d = 2`) to locate the absolute closest available alternative nodes.
* **Dynamic Shortcut Jumping:** If all immediate neighbors of the reporting node have filed their own congestion reports within a predefined period (default 1.5 seconds), the Base Station skips redundant neighbor availability checks to save CPU cycles.

## 🖥️ CAAS HPC Architecture & Constraints

The primary testing environment is the Cluster As A Service (CAAS) platform provided by Monash eSolutions.
* **Hardware:** The cluster consists of 14 compute nodes running on AMD Epyc host servers, connected via a Gigabit network. Each compute node contains 16 CPU cores and a shared GPU.
* **Access:** Connection to CAAS strictly requires an active Monash VPN connection. Users log in to the headnode (`student-caas-headnode.rep.monash.edu`) using their Monash authcate credentials.
* **Workload Management:** SLURM is used as the job scheduler; direct execution of computationally heavy programs on the headnode is strictly prohibited.
* **Resource Limits:** Jobs submitted to the `defq` partition are constrained to a maximum of 32 tasks per job, a 10-minute maximum execution time, and memory allocation up to 2 GB per core (or 500 MB per process/core when maxing out 32 tasks). The user's `$HOME` directory is limited to a 5 GB quota.

## 💻 Environment Access & File Transfer

The following instructions cover accessing and transferring files across Docker and CAAS environments.
| Environment | Command | Notes |
| :--- | :--- | :--- |
| **Docker** | `docker exec -it <ID> bash` | Local testing. |
| **CAAS HPC** | `ssh <credential>@student-caas-headnode.rep.monash.edu` | Requires Monash VPN. |

### Secure File Transfer
Use `scp -r` to copy directories instead of single files.
*   **To CAAS:** 
    ```bash
    scp <target_file> <credential>@student-caas-headnode.rep.monash.edu:<filename>
    ```
*   **From CAAS:** 
    ```bash
    scp <credential>@student-caas-headnode.rep.monash.edu:<filename> ./<new_name>
    ```

## 🛠 Compilation & Execution Guide
Before compiling, ensure you are in the target working directory. The system requires `rows * cols + 1` MPI processes (including one base station) and exactly 7 OpenMP threads per node.
* **Load Module (CAAS):**
    ```
    module load openmpi/4.1.5-gcc-11.2.0-ux65npg
    ```

* **Compiling:**
    ```
    mpicc -fopenmp evcns_<VERSION>.c utils.c -o Out -lm
    ```

* **Running:**
    ```
    srun -n <NUMBER OF PROCESSES> ./Out <NUMBER OF ROWS> <NUMBER OF COLUMNS>
    ```
    *Number of processes = (Number of rows * Number of columns) + 1*

## 📊 Presentation & Documentation

For a detailed breakdown of performance metrics, latency comparisons across different grid sizes (Mesh and Line topologies), and architectural evolution diagrams, view the presentation slides:

👉 **[Presentation Slide.pdf](./Presentation%20Slide.pdf)**

## 👨‍💻 Author

Shee Seng Cheng  
Tay Chee Hsian