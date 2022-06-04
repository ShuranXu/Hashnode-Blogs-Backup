## How to write power-efficient code on powerless microcontrollers ?

### **Background**

Nowadays various IoT products are playing significant roles in increasing our quality of life such as the use of smart watches or dyrones, and it is quite common to see that the majority of IoT products are low-power devices. (i.e. they are either coin-battery powered or energy-harvesting devices).  As a result, it is crucial to design power-efficient software systems when it comes to deploying applications on low-power microcontrollers, which act as the brain of an IoT product. In this article, I will share some valuable techniques and insight about how to code in a power-efficient manner to minimize the system power consumption.

## **Understanding Energy Consumption**

Prior to discussing low-power design techniques, it is important to know the following questions:

1. What is energy ?
2. How does the system consume power ?
3. What is the biggest power consumer? 

The above questions help us identify where we need to optimize for power-consumption minimization. 

***What is energy ?***

As we all know, energy is calculated as follows:

```
Energy(j) = Power(w) x Time(s)
```

It is intuitive to know that the following principles in order to increase the battery life:

- Decrease power
- Decrease the time the power is required

So we can see that the low energy firmware design essentially minimizes both the power and the duration the power is required. 


***How does the system consume power ?***

The following equation tells us the components that the energy is composed of:

```
Energy = dynamic power + static power
```

1). Dynamic Power: 

```
P = 1/2 * f * C * v2
```

From the equation we know that we can lower the power by the following:
- Reducing the running frequency (slowing or stopping the clocks)
- Reducing the voltage (lower algorithmic computation,etc)

2). Static Power: 

Such power consumption is primarily caused by CMOS leakage current and in a commercial CMOS process, leakage current is a function of temperature, so maintaining a reasonable operating temperature is key to preventing thermal runaway and device failure.

***What is the biggest power consumer?***

It is obvious that the CPU is the highest consuming part of a microcontroller, so minimizing CPU on-time certainly reduces energy consumption. So when we are deciding which microcontroller to use for the target application, it is critical to choose a higher performance one, since a higher performance CPU will reduce the time required for a fixed amount of work. For example, 16-bit RISC MSP430 can only achieve 0.288 DMIPS/MHz and consumes 330 uA/MHz when it is active, but a Cortex-M3 based Silicon Lab’s Leopard Gecko board can run at 1.25 DMIPS/MHz and 216 uA/MHz, achieving 35% less current consumption and 400% higher computational performance.

## **Low-Power Design Techniques  **

So how do we design our code to minimize the power consumption, here are some useful techniques that I would like to share. 

### **1). Gating**

- **Power gating**: completely turning power off to some portion of a chip, a large logic area or an oscillator circuit. 
- **Head-end clock gating**: locking down the entire clock distribution (frequency = 0, so dynamic power = 0)
- **Tail-end clock gating**: assert enable signal for flip-flops at the end of the clock tree only when new data is available


![sc1.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1653021829281/KhpANRvoP.png align="left")

The gating operations can be achieved via GPIO pin manipulations or clock control for the chosen peripheral. It should be obvious to see the level of power saved from each gating technique:

```
PS(power gating) > PS(Head-end clock gating) > PS(Tail-end clock gating) 
where PS = Power saving.
```

### **2). Make use of Low energy peripherals**

All peripherals are built with energy consumption in mind and are available in some energy modes. So we should select an appropriate peripheral whenever possible to do the work while the CPU goes to sleep. A few examples include:

- Use DMA to transfer data between memory and peripheral such as U(S)ART
- Use the RTC instead of busing waiting (i.e. looping)

### **3). Design event-driven system**

With respect to the software design, periodic status checking for event handling is absolutely a poor design practice as it wastes too much power. So we should implement Interrupt Service Routine (ISR) as the bottom-half for the target peripheral to clear status and trigger event flags, and implement a service/function to act as the bottom-half to handle the triggered event. If an RTOS is used (e.g VxWorks, FreeRTOS), a task can be launched to handle such operation; if cyclic execution is used, a function should be launched at the beginning of every loop to handle the pending flagged event prior to processing all required actions.

### **4). Taking advantage of advanced autonomous peripheral functions**

Depending on the microcontroller being used, some microcontrollers offer advanced autonomous peripheral functions. For instance, Silab’s Gecko boards offer the Peripheral Reflex System (PRS) to automate a great variety of tasks involving peripherals. A PRS is a network that lets the different peripheral modules communicate directly with each other without involving the MCU. [1]

The PRS module contains certain interconnect channels, and each of these can select between all the output reflex signals offered by the producers. The consumers can then choose which PRS channel to listen to and perform actions based on the reflex signals routed through that channel. The reflex signals can be both pulse signals and level signals. 

![sc2.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1653021974403/HJPPEWerM.png align="left")

The above example shows four peripherals connected to two PRS channels. On one channel is TIMER0 and ADC0 and the ACMP and TIMER1 are connected to a second channel. An overflow from TIMER0 can start an ADC single conversion, and an ACMP output can be used as input for a Compare/ Capture channel on TIMER1.

Making use of systems like PRS involves taking advantage of the associated APIs from the chip vendor, and instrumentation should be performed to ensure the PRS works as expected. 

### **5). Turning off unused peripherals**

At any given time in every microcontroller application, there are peripherals unused, so please ensure they are completely disabled. Sometimes this also applies to the CPU itself. In other words, if the core is idle, it can be turned off and energy is saved. But keep in mind about the startup and stop condition into consideration when disabling peripherals. For instance, the ADC usually requires some time to warm up before a conversion could be started if it has been turned off completely, and such wait time might impact the application performance. 

### **6). Clock Prescaling** 

The clock prescaling is usually available for all high frequency and many low frequency clock domains.  Even when a module is idle, portions of its logic, such as registers and the bus interface, are continuously clocked and draw current. Therefore,  clocks should be prescaled so that continuously clocked logic runs at the minimum frequency necessary for tasks to be completed or for a particular level of performance to be sustained.

### **7). Optimize Code**

Optimizing code usually leads to lower energy consumption by increasing the program speed and efficiency. A faster program spends less time in active mode, and each task in a more efficient program takes fewer instructions to execute. 

Code optimization begins with the performance profiling of the application via either a [static profiler]( https://www.perforce.com/blog/sca/what-static-analysis
) or a [dynamic run-time profiler](https://geekflare.com/application-profiling-software/). Once ‘hotspots’ are identified, various optimization techniques can be applied, such as refactoring the code to be cache friendly and be concurrency bug-free. Code optimization is not discussed here, as that is a big topic to cover in future blogs. 

A simple way to optimize the code is to build it with the highest optimization settings in release mode rather than in debug mode. 

### **8). Energy Profiling**

Some chip vendors provide energy measurement circuitry in their development kits and associated energy profiling tools in their IDEs, such as Silab’s Energy Profiler in Simplicity Studio. [2] If applicable, take power measurement on all operating stages of the target system and check if the power consumption meets the expectation for each stage. Any mismatch leads to code optimization towards energy minimization. 


![sc3.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1653022158706/zXBIXWT5e.png align="left")

The above image shows the interface of the energy profiling tool offered by Simplicity Studio, the user can visualize the current consumption in real time and relate the chosen current consumption region with the source code so as to narrow down the scope for the optimization.


## **Summary**

In this article, I discussed what energy consumption is and shared with you several common design practices to design low-power systems. I hope you find this article useful and enjoy the readling ! 

## **References**

[1] https://www.silabs.com/documents/public/application-notes/an0025-efm32-prs.pdf

[2]https://docs.silabs.com/simplicity-studio-5-users-guide/latest/ss-5-users-guide-tools-energy-profiler/



