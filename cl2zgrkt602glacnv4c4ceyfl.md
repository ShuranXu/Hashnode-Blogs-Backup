## How to easily measure the Disk I/O performance on Linux ?

Whenever we are running I/O intensive applications on Linux platform (e.g. Desktop Ubuntu or Embedded Linux), it is usually necessary to periodically monitor the disk I/O performance and adjust the program behaviour accordingly. Here I would like to share some performance indicators of the disk and how to observe these indicators.

### **I/O Performance Factors**

There are the following five factors when it comes with measuring the disk I/O performance:


1. **Utilization rate**: The average time spent processing read/write requests on all disks and partitions as a percentage of the total reported time window.
2. **Saturation rate**: The indication of the busyness of the disk. 100% saturation rate simply means that the disk can no longer accept new read/write requests.
3. **IOPS(Input/Output Per Second)**: Refers to the number of I/O requests per second.
4. **Throughput**: The size of I/O requests per second.
5. **Response time**: Refers to the interval time between sending an I/O request and receiving a response.

### **Disk I/O Monitoring**

Out of many Linux commands for I/O performance diagnostics, `iostat` is the most commonly observation tool, as it shows the usage rate, IOPS, and throughput of each disk. The following is an example output of the iostat command:


![sc1.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1652145013276/4gEnZmXsQ.png align="left")

Among the above indicators, one can notice the following metrics:

- disk I/O usage: %util
- IOPS: r/s+ w/s
- Throughput: rkB/s+wkB/s
- The response time:r_await+w_await

### **Process I/O Monitoring**

To monitor the I/O performance for a particular process, one can use `pidstat` command. By supplying the PID of the target process, the I/O statistical information such as I/O delay time and IOPS can be obtained altogther. Here is an example output:


![sc2.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1652145022074/G1S-R09zn.png align="left")

Here we are checking the I/O performance of a Google Chrome process.

### **Conclusion**

I/O performance monitoring can be essential in terms of perfomance tuning to I/O intensive applications. By using `pidstat` we can find out how a particular process is doing in terms of I/O processing; by using `iostat` we can have a peek at the overall disk I/O performance.