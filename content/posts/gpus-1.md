---

title: "Developing a reasonable understanding of GPUs for ML Workloads"
date: 2022-12-26T11:00:00+05:00
tags: ["MLOps","DevOps", "Nvidia", "GPU", "Operations", "GPUs", "Machine Learning", "Deep Learning"]
author: "Umer Jamil"

---
![GPU picture ](https://static.techspot.com/articles-info/2486/images/2022-06-21-image-16-j_1100.webp)

This blog is intended to develop a “breadth” level understanding of GPUs, and not a “depth” level. My aim will be to build a “reasonable” understanding that will help in our hardware decision-making for managing ML/DL workloads and pipelines from the perspective of an ML/MLOps Engineer. This blog does not contain comparisons of different GPU architectures for ML workloads.

Before diving, let's quickly recap some key terminologies which will be used in upcoming paras. 
| No | Term | Descripion  | 
|--|--|--|
| 1 | Latency | Latency is a time delay between the moment something is initiated, and the moment one of its effects begins or becomes detectable | 
| 2 | Throughput |Throughput is the amount of work done in a given amount of time | 
| 3 | FLOPS | floating-point operations per second. Used to calculate the computing performance of the ALUs. |
|4  | Device | GPU = set of multiprocessor |
| 5 | Device memory | VRAM of GPU |
| 6 | Host | CPU of the host system|
| 7 | Multiprocessor |set of processors & shared memory|
|8 | Kernel | GPU program (do not confuse it with Linux kernel)
|11 | SIMD | Single Instruction, Multiple Data (SIMD) units refer to hardware components that perform the same operation on multiple data operands concurrently. Used in GPUs
|12 | GP-GPUs | General Purpose Graphics Processing Units
|13 | CUDA | Compute Unified Device Architecture. A parallel computing platform and programming model for general computing on graphical processing units (GPUs)
|14 | ALUs | Arithmetic Logic Units. Part of a central processing unit that carries out arithmetic and logic operations on the operands in computer instruction words. 
| 15 |Cache |A cache is a smaller, faster memory, located closer to a processor core, which stores copies of the data from frequently used main memory locations.
|16| Registers|They hold a small amount of data, computer instructions or the storage address of any particular information
|17|Clock Speed | The number of cycles that the processor executes per second. 

## What is a GPU
The GPUs architecture, in the beginning, was highly tuned for performing graphics pipeline operations and rendering real-time graphics. Today, their fame is not due to their ability to render 3D graphics but because of their massive computing power. GPUs have evolved from domain-specific hardware (graphics rendering) to highly efficient general-purpose high-compute capability hardware.

The initial GPUs had a very fixed function, performing different pipeline stages such as follows: 
![GPU Architecture](https://i.postimg.cc/zv30c7yQ/gpu1.png)

A simplified architecture of early GPU is as follows: 
![GPU Architecture](https://i.postimg.cc/RCjDYrTQ/gpu2.png)

For each fixed function the GPUs were supposed to perform, dedicated API calls existed and were implemented in hardware. For using these GPUs for computational workloads, all tasks have to be mapped to the computer graphics domain. For early GPUs intended for graphics operations, the programmer needed to write the program using a graphics language such as OpenGL. This approach was not scalable to problem domains that do not map well to computer graphics. This leads to the design of general-purpose GPU architectures and corresponding programming models such as (CUDA, CAL, and OpenCL).

### Key Difference of GPU and GP-GPU
| No | GPU | GP-GPU  |
|--|--|--|
|1.|Fixed functions| Programmable processor |
|2.|Early GPUs were focused on integer arithmetic, not for floating point operations| Floating point arithmetic |
|3.|No instruction set or memory| Instruction set and memory for high throughput |

Modern-day GP-GPU have high FLOPS (NVIDIA A100 delivers 312 teraFLOPS (TFLOPS)) and programmability.

## Hardware Components
To understand the hardware components of GPUs, we need to understand the hardware components of the CPU first. 

### CPU Hardware
Let's consider a very simple architecture for CPU:

![CPU Architecture](https://i.postimg.cc/tCJFF76B/1-sh8h-Dj-O4x0-S0-Q-JGx-GTKc-Q.png)

It has the following components:
| No | Component | Purpose  |
|--|--|--|
|1.| ALU | Performs the arithmetic and logical functions |
|2. | Registers | Used to store data during/after the operation, facilitate data movement|
| 3. | Control Units| Decides which instruction to execute and then which data to pick up and send to ALU for execution|
|4|Cache| The CPU never reads data from RAM. They use layers of Cache (L1, L2, etc). The reason is cache memory is faster than system RAM and closer to the CPU because it is on a processing chip.Predictive algorithms are used to move data from RAM into the cache. There exist a cache hierarchy (L1, L2, L3 cache) L3, L2 designed to predict what data and program instruction will be needed next, move that data from RAM and move it closer to the CPU to be ready when required. L1 is Closet to CPU and ​​used for storing the microprocessor's recently accessed information.|
|5 |RAM|A temporary storage bank used to store data so that it can be retrieved quickly.|
#### Process of running a program on a multicore CPU

The steps are as follows:
 - Operating System (OS) loads the program into RAM 
 - OS selects CPU execution context OS interrupts the processor
 - Prepares execution context (sets contents of registers, program counter, etc. to prepare
   execution context) 
- Start trigger The processor starts executing instructions

Now we understand what is a CPU and how it works, let us dive into the difference between CPU and GPU

### GPU Hardware
A cartoon diagram of Nvidai GPU is shown below:
![GPU nvidia architecture](https://i.postimg.cc/PfRCsyLM/gpu3.png)

The three main components of GPUs are:

 1.  ALUs (steam multiprocessor in case of Nvidia)
	 - ALUs are the smallest unit of processing. Instead, we will talk about
	   it in terms of cores. Each core can have multiple ALUs. 
	  - For example, each CUDA core includes 32 ALUs. There are 6912 CUDA cores in A100.
	   (More than six thousand!) NVIDIA also has introduced Tensor cores in
	   the latest Ampere architecture. 
	  - Tensor cores are more suitable for Deep Learning workloads which are essentially large dimensions matrix multiplications.  
	  - Quick difference between CUDA and Tensor Cores:
	   CUDA cores perform one operation per clock cycle, whereas tensor
	   cores can perform multiple operations per clock cycle.  
	  - Tensor Cores are so fast that computation is no longer a bottleneck. The only
	   bottleneck is getting data to the Tensor Cores (Memory Bandwidth). 
	  - So for DL workloads, it is preferable to get NVIDIA GPU with Tensor Cores.

2. Memory bandwidth
   - This is often the overlooked metric when it comes to GPUs. 
   - Memory bandwidth is defined as how fast data from/to memory (VRAM) can be moved to computation cores. 
   - So if GPU memory bandwidth is not good, but its computation cores are good, the computation cores will be unutilized most of the time because of slower data travel speed.
   - We know that tensor cores have a high number of TFLOPS. However, during training of the BERT Large model, we have tensor core TFLOPS utilization of about 30% which means that tensor cores are sitting idle for 70% of the time just because there is a delay in the availability of data. 
   - We can say that GPU memory bandwidth is the single best indicator for each GPU’s performance is their memory bandwidth. For example, The A100 GPU has 1,555 GB/s memory bandwidth vs the 900 GB/s of the V100. As such, a basic estimate of the speedup of an A100 vs V100 is 1555/900 = 1.73x.
   - Overcoming bandwidth limits are a common challenge for GPU compute application developers.

3. Shared Memory / L1 Cache Size / Registers
   - Since memory bandwidth is a limiting factor in GPU performance, memory hierarchy is used to enable faster data transfer to Tensor cores. 
   - Overall, we can see that the Ampere architecture is optimized to make the available memory bandwidth more effective by using an improved memory hierarchy: from global memory to shared memory tiles, to register tiles for Tensor Cores.
   - A generic diagram of GPU memory hierarchy is as follows
![GPU Memory Hierarchy](https://i.postimg.cc/BZgdX75x/gpu4.png)
   - We can see that the bandwidth amplifies as we move to left side, to the computing cores. Cache hierarchy reduces throughput demand on main memory

#### Process of running a program on a GPU 
##### Prior 2007 (GPU)

Task: Drawing a picture using a GPU 
 1. Application (via graphics driver) provides GPU vertex and fragment shader program binaries
 2. Application sets graphics pipeline parameters (e.g., output image
    size)
  3. Application provides hardware a buffer of vertices
  4. drawPrimitives(vertex_buffer)

Early GPU hardware could only execute graphics pipeline computations.
##### After 2007 (GP-GPU)
Task: running a computational workload (non-graphic) on GPU
1. Application allocates buffers in GPU memory
2. Copy data to/from buffers
3. Application (via graphics driver) provides GPU a single kernel program binary
4. Application tells GPU to run the kernel in an SPMD fashion (“run N instances”)
5. launch(myKernel, N)

## Comparison: CPU vs GPU
Now we know about CPU and GPU hardware components. Let us do a quick comparison for both.

### For CPU:
 1. ALUs
	 - Less (at max 40 cores) CPU cores are more complex (have more
	   instructions) and thus larger than GPU cores
	  - They handle a variety of tasks.
2. Control Units (CU)
	 - Large number of CU
	 - Runs a large number of multiple tasks such as OS, App1, App2, etc
3. Cache
	 - Large cache (in MBs)
	 - Helps the CPU core to access data at a higher speed and hence increase the execution speed
	 - The processor core can access what is in cache in a few clock cycles. If what it needs is not there, it takes about 100 cycles to get it from memory.
	 - Latency is optimized via large caches
	 - CPUs are designed to maximize running operations out of cache, therefore they need large cache
3. Clock Speed
	 - High clock speed (GHz)
	 - Because the CPU is designed to handle a variety of tasks
	 - The computations on the CPU are single-threaded, so to increase the single thread performance, the cloak speed is higher
4. RAM
	 - Very large, in TBs
	 - A temporary storage bank used to store data so that it can be retrieved quickly
	 - We can open many programs, etc.


### For GPU:
1. ALUs
	 - Large (6912 tensor cores  for A100)
	 - GPUs perform the same operation on multiple data items in parallel. 
	 - Result: GPU can process vast amounts of data in a single workload.
	 - Compute units are based on SIMD hardware.
2. Control Units
	 - Small number of CU
	 - In GPU, we often have to perform similar tasks (computing matrix or modeling complex numbers e.g.), so not a large number of CU are required
3. Cache
	 - Small cache (in KBs)
	 - GPUs do not need a large cache, they can dedicate more of the transistor area to computation horsepower
4. Clock speed
	 - Slower (MHz)
	 - Incase of GPU, we will be performing the same set of operation on data repeatedly, because not a variety of tasks are required
5. VRAM, video memory, device memory
	 - In GBs
	 - Used to exchange data between CPU and GPU
	 - It is allocated and managed by the host (CPU)
	 - Accessible to both host and GPU
	 - It is significantly larger than the L1, L2 cache registers
	 - High bandwidth should be considered for VRAM

### Quick summary: GPU vs CPU

Let's do a quick summary comparison.

|   | CPU  | GPU | 
|--|--|--|
|Strengths | Very large main memory (RAM) | High bandwidth main memory
| | Very fast clock speed | More compute resources (Cores) |
| | Latency optimized via large caches | High throughput |
| Weakness |  Relatively low memory bandwidth
| | Cache misses very costly | Relatively low memory capacity|
| | Low performance/ watt | Low per thread performance each core runs at ~1 Ghz i.e. 4 times slower than CPU core |

In short:

 - CPUs are low latency low throughput processors 
 - GPUs are high latency high throughput processors
 - CPUs use task parallelism (Multiple tasks map to multiple threads)
 - GPUs use data parallelism (Same instruction on different data)

## Which GPUs to choose for Deep Learning?
I will cover this question in another detailed post, right now lets discuss briefly the facotrs to consider when choosing GPUs for ML Workloads. These factors are as follows: 

 1. Tensor cores (explained previously)
 2. Memory bandwidth 
     - If we take the Tesla A100 GPU bandwidth vs Tesla V100 bandwidth, we get a speedup of 1555/900 = 1.73x
 3. Shared memory size
     - For example, We have the following shared memory sizes on the following architectures:
     - Volta: 96kb shared memory / 32 kb L1
     - Turing: 64kb shared memory / 32 kb L1
     - Ampere: 164 kb shared memory / 32 kb L1


## Summary
In this post, we dived in a breadth and depth manner to develop a reasonable understanding of GPUs. We develop familiarity with GPU/CPU hardware, their key differences, and what technical specifications to look for when buying GPUs. We also briefly discussed how to choose a GPU for Deep Learning. I will cover more on it in a systematic manner in my next posts.

## References
1.  [https://download.nvidia.com/developer/cuda/seminar/TDCI_Arch.pdf](https://download.nvidia.com/developer/cuda/seminar/TDCI_Arch.pdf)
2.  [https://www.cs.cmu.edu/afs/cs/academic/class/15418-s18/www/lectures/06_gpuarch.pd](https://www.cs.cmu.edu/afs/cs/academic/class/15418-s18/www/lectures/06_gpuarch.pdf)
3.  [http://haifux.org/lectures/267/Introduction-to-GPUs.pdf](http://haifux.org/lectures/267/Introduction-to-GPUs.pdf)    
4.  [https://ec.europa.eu/programmes/erasmus-plus/project-result-content/52dfac24-28e9-4379-8f28-f8ed05e225e0/lec03_gpu_architectures.pdf](https://ec.europa.eu/programmes/erasmus-plus/project-result-content/52dfac24-28e9-4379-8f28-f8ed05e225e0/lec03_gpu_architectures.pdf)    
5.  [https://download.nvidia.com/developer/cuda/seminar/TDCI_Arch.pdf](https://download.nvidia.com/developer/cuda/seminar/TDCI_Arch.pdf)    
6.  [https://hdms.bsz-bw.de/frontdoor/deliver/index/docId/4500/file/gpgpu-origins-and-gpu-hardware-architecture.pdf](https://hdms.bsz-bw.de/frontdoor/deliver/index/docId/4500/file/gpgpu-origins-and-gpu-hardware-architecture.pdf)    
7.  [https://www.cs.cmu.edu/afs/cs/academic/class/15418-s18/www/lectures/06_gpuarch.pdf](https://www.cs.cmu.edu/afs/cs/academic/class/15418-s18/www/lectures/06_gpuarch.pdf)    
8.  [http://www.irisa.fr/alf/downloads/collange/cours/ada2020_gpu_1.pdf](http://www.irisa.fr/alf/downloads/collange/cours/ada2020_gpu_1.pdf)    
9.  [https://homes.luddy.indiana.edu/achauhan/Teaching/B649/2011-Fall/StudentPresns/gpu-arch.pdf](https://homes.luddy.indiana.edu/achauhan/Teaching/B649/2011-Fall/StudentPresns/gpu-arch.pdf)    
10.  [http://www.ziti.uni-heidelberg.de/ziti/uploads/ce_group/seminar/2014-Daniel_Schlegel-presentation.pdf](http://www.ziti.uni-heidelberg.de/ziti/uploads/ce_group/seminar/2014-Daniel_Schlegel-presentation.pdf)   
11.  [https://www.control.lth.se/fileadmin/control/Education/DoctorateProgram/DeepLearning/2016/presentation.pdf](https://www.control.lth.se/fileadmin/control/Education/DoctorateProgram/DeepLearning/2016/presentation.pdf)    
12.  [https://www.nvidia.co.uk/docs/IO/147844/Deep-Learning-With-GPUs-MaximMilakov-NVIDIA.pdf](https://www.nvidia.co.uk/docs/IO/147844/Deep-Learning-With-GPUs-MaximMilakov-NVIDIA.pdf)    
13.  [https://repository.library.northeastern.edu/files/neu:m046sc381/fulltext.pdf](https://repository.library.northeastern.edu/files/neu:m046sc381/fulltext.pdf)    
14.  [https://www.comp.nus.edu.sg/~cs2100/2_resources/AppendixA_Graphics_and_Computing_GPUs.pdf](https://www.comp.nus.edu.sg/~cs2100/2_resources/AppendixA_Graphics_and_Computing_GPUs.pdf)    
15.  [http://www.compsci.hunter.cuny.edu/~sweiss/course_materials/csci360/lecture_notes/gpus.pdf](http://www.compsci.hunter.cuny.edu/~sweiss/course_materials/csci360/lecture_notes/gpus.pdf)    
16.  [https://www.cs.wm.edu/~kemper/cs654/slides/nvidia.pdf](https://www.cs.wm.edu/~kemper/cs654/slides/nvidia.pdf)    
17.  [http://passlab.github.io/CSE436536/notes/lecture19_GPUArchCUDA01.pdf](http://passlab.github.io/CSE436536/notes/lecture19_GPUArchCUDA01.pdf)    
18.  [https://www.cs.utah.edu/~jeffp/teaching/MCMD/S20-GPU.pdf](https://www.cs.utah.edu/~jeffp/teaching/MCMD/S20-GPU.pdf)    
19.  [http://courses.cms.caltech.edu/cs101gpu/Old/2015_lectures/cs179_2015_lec05.pdf](http://courses.cms.caltech.edu/cs101gpu/Old/2015_lectures/cs179_2015_lec05.pdf)    
20.  [http://haifux.org/lectures/267/Introduction-to-GPUs.pdf](http://haifux.org/lectures/267/Introduction-to-GPUs.pdf)    
21.  [https://www.nvidia.com/en-us/geforce/graphics-cards/compare/](https://www.nvidia.com/en-us/geforce/graphics-cards/compare/)    
22.  [https://blog.paperspace.com/gpu-memory-bandwidth/](https://blog.paperspace.com/gpu-memory-bandwidth/)    
23.  [https://www.cs.cmu.edu/afs/cs/academic/class/15418-s18/www/lectures/06_gpuarch.pdf](https://www.cs.cmu.edu/afs/cs/academic/class/15418-s18/www/lectures/06_gpuarch.pdf)
