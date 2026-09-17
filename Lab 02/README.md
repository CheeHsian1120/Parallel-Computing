# Prime Number Generation: Open MPI & Hybrid (Open MPI + OpenMP)
[![Open MPI](https://img.shields.io/badge/Open%20MPI-00599C?style=for-the-badge&logo=c&logoColor=white)](#)
[![OpenMP](https://img.shields.io/badge/OpenMP-033963?style=for-the-badge&logo=c&logoColor=white)](#)

## 📌 Overview
This project benchmarks and optimizes prime number generation (strictly less than `n`) utilizing distributed-memory parallelism (Open MPI) and a Hybrid approach (Open MPI + OpenMP). The objective is to evaluate algorithmic efficiency, network communication latency, and multi-core scalability across high-performance computing (HPC) environments. By comparing empirical performance against theoretical limits modeled by an extended Amdahl's Law, the project identifies hardware constraints and the impact of workload distribution strategies.

## 🚀 Key Achievements & Methodologies

### 1. Open MPI Distributed-Memory Implementation
*   **Block-Cyclic Partitioning:** Mitigated fine-grained network latency by distributing odd-number candidate chunks to balance the higher computational cost of larger numbers.
*   **Zero-Sorting Reduction:** Implemented boolean arrays across ranks and utilized `MPI_Reduce(..., MPI_LOR)` to merge results directly into the master node, completely eliminating serial sorting overhead.
*   **Chunk Size Optimization:** Identified a chunk size of 32 as the optimal configuration for $N = 40,000,000$, balancing computation-time equality and minimizing candidate-count imbalance across 64 processes.

### 2. Hybrid Implementation (MPI + OpenMP)
*   **Two-Tiered Parallelism:** Utilized MPI to distribute blocks of candidate numbers across network nodes, while deploying OpenMP threads to dynamically schedule and process those chunks within each node's shared memory.
*   **Overcoming Network Bottlenecks:** Demonstrated that while pure MPI scales poorly due to communication overhead, the Hybrid model scales effectively by adding threads, reaching an 8.90x empirical speedup at 16 threads.

### 3. Theoretical vs. Empirical Analysis
*   **Extended Amdahl's Law:** Modeled theoretical speedup using the formula $S = \frac{1}{s + \frac{p}{N} + k}$, which explicitly incorporates the communication overhead fraction ($k$).
*   **The Reality Gap:** Proved that theoretical scaling diverges massively from empirical reality. While theoretical models climb indefinitely, actual Open MPI performance crashes after 32 processes due to physical network latency and hardware switch saturation. 

## 🖥️ CAAS HPC Architecture & Constraints
The primary testing environment is the Cluster As A Service (CAAS) platform provided by Monash eSolutions. 
*   **Hardware:** The cluster consists of 14 compute nodes running on AMD Epyc host servers, connected via a Gigabit network. Each compute node contains 16 CPU cores and a shared GPU. 
*   **Access:** Connection to CAAS strictly requires an active Monash VPN connection. Users log in to the headnode (`student-caas-headnode.rep.monash.edu`) using their Monash authcate credentials.
*   **Workload Management:** SLURM is used as the job scheduler; direct execution of computationally heavy programs on the headnode is strictly prohibited.
*   **Resource Limits:** Jobs submitted to the `defq` partition are constrained to a maximum of 32 tasks per job, a 10-minute maximum execution time, and memory allocation up to 2 GB per core (or 500 MB per process/core when maxing out 32 tasks). The user's `$HOME` directory is limited to a 5 GB quota.

## 💻 Environment Access & File Transfer
The following instructions cover accessing and transferring files across Docker, CAAS, and AWS environments.

### System Access
| Environment | Command | Notes |
| :--- | :--- | :--- |
| **Docker** | `docker exec -it <ID> bash` | Local testing. |
| **CAAS HPC** | `ssh <credential>@student-caas-headnode.rep.monash.edu` | Requires Monash VPN. |
| **AWS EC2** | `pcluster ssh --cluster-name mycluster --region ap-southeast-5 -i <keyname>.pem` | Activate environment first: `C:\Users\<user>\apc-ve\Scripts\activate` |

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
*   **To AWS:** 
    ```bash
    scp -i <keyname>.pem <target_file> <username>@<IP>:<filename>
    ```
*   **From AWS:** 
    ```bash
    scp -i <keyname>.pem <username>@<IP>:<filename> ./<new_name>
    ```

## 🛠 Compilation & Execution Guide
Before compiling, ensure you are in the target working directory.

### 1. Serial Implementation
*   **Compile:** 
    ```bash
    gcc task/task1_Serial.c -o compile/task1_Serial -lm
    ```
*   **Run (CAAS):** 
    ```bash
    srun --nodes=1 --ntasks=1 --cpus-per-task=1 --partition=defq ./compile/task1_Serial <N>
    ```
*   **Run (AWS):** 
    ```bash
    ./compile/task1_Serial <N>
    ```

### 2. POSIX Threads
*   **Compile:** 
    ```bash
    gcc task/task1_POSIX.c -o compile/task1_POSIX -lm -lpthread
    ```
*   **Run (CAAS):** 
    ```bash
    srun --nodes=1 --ntasks=1 --cpus-per-task=<threads> --partition=defq ./compile/task1_POSIX <N> <threads>
    ```
*   **Run (AWS):** 
    ```bash
    ./compile/task1_POSIX <N> <threads>
    ```

### 3. OpenMP
*   **Compile:** 
    ```bash
    gcc task/task1_OpenMP.c -o compile/task1_OpenMP -lm -fopenmp
    ```
*   **Run (CAAS):** 
    ```bash
    srun --nodes=1 --ntasks=1 --cpus-per-task=<threads> --partition=defq ./compile/task1_OpenMP <N> <threads>
    ```
*   **Run (AWS):** 
    ```bash
    export OMP_NUM_THREADS=<threads>
    ./compile/task1_OpenMP <N>
    ``` 

### 4. Open MPI
*   **Load Module (CAAS):** 
    ```bash
    module load openmpi/4.1.5-gcc-11.2.0-ux65npg
    ```
*   **Compile:** 
    ```bash
    mpicc task/task1_MPI.c -o compile/task1_MPI -lm
    ```
*   **Run (CAAS):** 
    ```bash
    srun --nodes=1 --ntasks=<processes> --cpus-per-task=1 --partition=defq ./compile/task1_MPI <N> <chunksize>
    ```
*   **Run (AWS):** 
    ```bash
    mpirun --oversubscribe -np <processes> ./compile/task1_MPI <N> <chunksize>
    ```

### 5. Hybrid (Open MPI + OpenMP)
*   **Load Module (CAAS):** 
    ```bash
    module load openmpi/4.1.5-gcc-11.2.0-ux65npg
    ```
*   **Compile:** 
    ```bash
    mpicc task/task2_Hybrid.c -o compile/task2_Hybrid -lm -fopenmp
    ```
*   **Run (CAAS):** 
    ```bash
    export OMP_NUM_THREADS=<threads>
    srun --nodes=<nodes> --ntasks=<processes> --cpus-per-task=<threads> --cpu-bind=none --partition=defq ./compile/task2_Hybrid <N> <chunksize> <threads>
    ```
    *(Note: `--cpu-bind=none` ensures threads can properly distribute across available cores instead of binding to a single core[cite: 1].)*
*   **Run (AWS):** 
    ```bash
    export OMP_NUM_THREADS=<threads>
    mpirun --oversubscribe -np <processes> ./compile/task2_Hybrid <N> <chunksize> <threads>
    ```

## 📈 Automated Benchmark Testing (SLURM)
To run automated benchmarks via Slurm queue scripts, copy the relevant `.slurm` file to your environment and submit the job to the cluster.

**Transfer Script (CAAS Example):**
```bash
scp <slurm file> <credential>@student-caas-headnode.rep.monash.edu:<slurm file>
```
Submit Job:
```bash
sbatch <slurm file>
```
Monitor & Retrieve:
* Check queue status: `squeue` or `squeue -u ${USER}`
* View results: `cat <output_filename>.out`
* *Troubleshooting:* If you encounter hidden return carriage errors `(\r)` preventing execution, sanitize the script: `sed -i 's/\r$//' <slurm file>`.

## 📊 Presentation & Documentation
For a detailed breakdown of performance metrics, theoretical bottlenecks, and architectural comparisons, view the presentation slides:

👉 **[Presentation Slide](./Presentation%20Slide.pdf)**

## 👨‍💻 Author
Shee Seng Cheng  
Tay Chee Hsian