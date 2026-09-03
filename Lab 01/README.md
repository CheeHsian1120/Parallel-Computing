# Prime Number Generation: Performance Benchmarking with Serial, POSIX Threads, and OpenMP

## 📌 Overview
This project explores and benchmarks the optimization of prime number generation (strictly less than <code>n</code>). It compares a highly optimized Serial C implementation against parallel architectures using **POSIX Threads (Pthreads)** and **OpenMP**. The objective is to evaluate algorithmic efficiency, multi-core scalability, and memory cache utilization under different hardware configurations (8-core vs. 32-core processors).

## 🚀 Key Achievements & Methodologies

### 1. Optimized Serial Baseline
- **Algorithmic Efficiency:** Reduced computation time by skipping all even numbers after 2 and limiting divisor checks strictly up to <code>sqrt(k)</code>.
- **Performance:** Achieved a ~2.30x speedup compared to an unoptimized baseline when <code>n = 40,000,000</code>.
- **Precision Timing:** Separated computation and I/O write times using <code>CLOCK_MONOTONIC</code> to ensure terminal/file outputs do not distort CPU benchmarking.

### 2. POSIX Threads (Pthreads) Implementation
- **Lock-Free Design:** Implemented a global boolean array shared across threads with independent indexing, completely eliminating Mutex overhead and race conditions.
- **Cyclic Partitioning:** Distributed the workload using thread striding (strides equal to the number of threads) for natural load balancing.
- **Hardware Scaling Limits:** Identified thread over-subscription bottlenecks; execution time plateaued after exceeding physical core counts due to OS context-switching. Reached an optimal 10.89x speedup (0.18s execution time) for <code>n = 10,000,000</code> at 64 threads on a 32-core machine.

### 3. OpenMP ImplementationDynamic
- **Load Balancing:** Utilized <code>#pragma omp parallel</code> for with <code>schedule(dynamic, 500)</code> to handle computationally unbalanced workloads, as larger numbers require more CPU-intensive divisor checks.
- **Cache Optimization:** Surpassed Pthreads by eliminating "false sharing" (cache-line contention). The 500-iteration chunks provided each core with continuous memory blocks, maximizing cache efficiency.
- **Peak Speedup:** Slashed computation time for <code>n = 40,000,000</code> from ~11.8s (Serial) to under 1s. Achieved a massive ~15.75x speedup on a 32-core machine, outperforming the Pthreads implementation.

## 🔮 Future WorkDynamic 
- **Chunk Scaling:** Automatically adjust OpenMP chunk sizes dynamically based on the CPU core count and problem size <code>n</code>, rather than relying on a static size of 500.
- **Thread Pinning:** Implement thread affinity to bind worker threads to specific physical CPU cores, preventing the OS scheduler from migrating threads and further reducing cache-miss penalties.

## 📊 Presentation & Documentation
For a more detailed breakdown of the performance metrics, hardware bottlenecks, and architectural comparisons, please check out the presentation slides:

👉 **[Presentation Slide](./Presentation%20Slide.pdf)**

## 👨‍💻 Author
Shee Seng Cheng  
Tay Chee Hsian
