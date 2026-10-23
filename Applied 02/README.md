# Image Rotation with GPU and HPC Ethical Implications
[![CUDA](https://img.shields.io/badge/CUDA-76B900?style=for-the-badge&logo=nvidia&logoColor=white)](https://img.shields.io/badge/CUDA-76B900?style=for-the-badge&logo=nvidia&logoColor=white)

## 📌 Overview

This project explores hardware-accelerated image rotation utilizing the CUDA programming model, alongside a critical analysis of the ethical implications associated with scaling High-Performance Computing (HPC) for AI. The technical implementation focuses on optimizing data transfers between host memory and device memory, evaluating execution configurations, and minimizing latency through Inverse Mapping and GPUDirect Storage. The theoretical component investigates the environmental, security, and accessibility challenges of massive GPU cluster deployment.

## 🚀 Key Achievements & Methodologies

### 1. CUDA Programming & Execution Configuration

* **Thread/Block Hierarchy:** Organized threads into a local cooperative block scheduled onto a Streaming Multiprocessor, grouping them into a global grid that tiles the entire 2D image coordinate space.
* **SM Occupancy Optimization:** Utilized 256 threads per block to guarantee execution in 8 full 32-thread hardware warps, maximizing SM occupancy and avoiding idle execution lanes.
* **Hardware-Level Trigonometry:** Calculated trigonometric angles in hardware at the Special Function Unit (SFU) to maximize compute throughput, bypassing slower software math functions.

### 2. Memory Optimization & Data Transfer

* **Inverse Mapping Implementation:** Replaced Forward Mapping with Inverse Mapping to ensure destination addresses remain consecutive. This allowed the GPU's memory controller to group operations into a single coalesced memory transaction, boosting effective memory throughput.
* **PCIe vs. VRAM Bottleneck Resolution:** Addressed the bandwidth disparity between the PCIe x16 bus (~16-64 GB/s) and internal GPU memory (~100-1000+ GB/s) by analyzing host-to-device memory allocation routines (`cudaMalloc`, `cudaMemcpy`).
* **GPUDirect Storage (GDS) Evaluation:** Evaluated GDS for continuous high-resolution streaming, showing how it eliminates redundant CPU bounce buffers and cuts transit latency by feeding GPU compute engines at full NVMe line rates.

### 3. Ethical Implications of Scaling HPC

* **Environmental & Infrastructure Cost:** Identified that larger AI models require parallel GPU clusters, driving massive electricity consumption (projected 945 TWh by 2030) and creating local grid and water pressure.
* **Unequal Global Access:** Highlighted that emerging economies house ~50% of global internet users but less than 10% of global data center capacity, leading to dependence on overseas providers and lack of local compute control.
* **Responsible HPC Framework:** Proposed a framework to measure and limit resource use, protect data privacy, test fairness against subgroup errors, and support compute access for smaller institutions.

## 🔮 Future Work

* **End-to-End Benchmarking:** Benchmark end-to-end GPU performance across complete image processing pipelines.
* **Impact Measurement:** Measure and quantify the wider socio-environmental impacts of physical HPC deployments on local communities.

## 📊 Presentation & Documentation

For a more detailed breakdown of the GPU architecture, memory mapping visualizations, and the responsible HPC framework, please check out the presentation slides:

👉 **[Presentation Slide.pdf](./Presentation%20Slide.pdf)**

## 👨‍💻 Author

Shee Seng Cheng  
Tay Chee Hsian
