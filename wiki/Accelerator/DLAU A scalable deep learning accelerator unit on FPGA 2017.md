# DLAU A scalable deep learning accelerator unit on FPGA 2017
IEEE TRANSACTIONS ON COMPUTER-AIDED DESIGN OF INTEGRATED CIRCUITS AND SYSTEMS, VOL. 36, NO. 3, MARCH 2017
## Problem
* Chen et al. [6] presented a ubiquitous machine-learning hardware accelerator called DianNao, which initiated the field of deep learning processor. But DianNao is not implemented using reconfigurable hardware like FPGA, therefore it cannot adapt to different application demands.
* Around FPGA acceleration researches, Ly and Chow [5] designed FPGA-based solutions to accelerate the restricted Boltzmann machine (RBM). They created dedicated hardware processing cores which are optimized for the RBM algorithm. Kim et al. [7], Yu et al. [8] etc have an FPGA-based accelerator, but it cannot accommodate changing network size and network topologies.
* To sum up, these studies focus on implementing a particular deep learning algorithm efficiently, but how to increase the size of the neural networks with scalable and flexible hardware architecture has not been properly solved.
## Contribution
* Use tile techniques to partition large scale input data. The DLUA architecture can be configured to different size of tile data to leverage the trade off between speed up and hardware cost
* DLUA composed by three fully pipelined processing units, including tiled matrix multiplication unit (TMMU), part sum accumulation unit (PSAU), and activation function acceleration unit (AFAU). Different network topologies such as CNN, DNN, or even emerging neural networks can be composed from these basic modules. Consequently, the scalability of FPGA-based accelerator is higher than ASIC-based accelerator.
## DLUA Architecture 
### Techniques
* TILE TECHNIQUES
* HOT SPOT PROFILING then come out key hardware components
### Architecture
* TMMU is the primary computational unit, which reads the total weights and tiled nodes data through DMA, performs the calculations,and then transfers the intermediate part sum results to PSAU.
* PSAU collects part sums and performs accumulation. When the accumulation is completed, results will be passed to AFAU.
* AFAU performs the activation function using piecewise linear interpolation methods.
### TMMU
* Fig. 2 illustrates the TMMU schematic diagram, in which we set tile size = 32 as an example. TMMU first reads the weight matrix data from input buffer into different BRAMs in 32 by the row number of the weight matrix (n = i%32 where n refers to the number of BRAM, and i is the row number of weight matrix). Then, TMMU begins to buffer the tiled node data.
* In the first time, TMMU reads the tiled 32 values to registers Reg_a and starts execution. In parallel to the computation at every cycle, TMMU reads the next node from input buffer and saves to the registers Reg_b. Consequently, the registers Reg_a and Reg_b can be used alternately.
* the weight data and the node data are saved in BRAMs and registers. The pipeline takes advantage of time-sharing the coarse-grained accelerators. As a consequence, this implementation enables the TMMU unit to produce a part sum result every clock cycle.
### PSAU Architecture
* PSAU is responsible for the accumulation operation. Fig. 3 presents the PSAU architecture, which accumulates the part sum produced by TMMU.
* If the part sum is the final result, PSAU will write the value to output buffer and send results to AFAU in a pipeline manner.
* PSAU can accumulate one part sum every clock cycle, therefore the throughput of PSAU accumulation matches the generation of the part sum in TMMU.
### AFAU Architecture
* AFAU implements the activation function using piecewise linear interpolation (y = ai ∗ x+bi, x ∈ [x1, xi+1]). This method has been widely applied to implement activation functions with negligible accuracy loss when the interval between xi and xi+1 is insignificant.
* Equation (1) shows the implementation of sigmoid function.
* For x > 8 and x ≤ −8, the results are sufficiently close to the bounds of 1 and 0, respectively.
* For the cases in −8 < x ≤ 0 and 0 < x ≤ 8, different functions are configured. In total, we divide the sigmoid function into four segments
* AFAU also has both input buffer and output buffer to maintain the throughput with other processing units.
* In particular, we use two separate BRAMs to store the values of a and b. The computation of AFAU is pipelined to operate sigmoid function every clock cycle.
## EXPERIMENTS AND DATA ANALYSIS
* In the experiment we use tile size = 32 considering the hardware resources integrated in the Zedboard development board.
* The DLAU computes 32 hardware neurons with 32 weights every cycle.
* The clock of DLAU is 200 MHz (one cycle takes 5 ns).
* Three network sizes—64×64, 128×128, and 256×256 are tested.
## Resource utilization in FPGA
## Summary
* tile-based technique
* Here 'scalable' means that same design style can be adapted to different hardware resource size instead of same hardware dynamically scalable for different hardware resource configuration or utilization
