## How to improve our sleep quality using the BLE technology ?

### **Background**

Sleep trouble has been one of the ongoing threats to individual health worldwide. According to the National Institutes of Health (NIH) , approximately 20% to 30% of adults in the U.S. alone have experienced insomnia symptoms, with at least 6% suffering from insomnia disorder. [1] The research has identified that the following key factors impact the sleep quality considerably:[2]

- **Bedroom Temperature**: low temperature on average makes people feel sleepy, whereas a higher temperature keeps individuals awake. 
- **Noise**: Statistics have shown that loud noise disturbances can cause severe sleep fragmentation and disruption, which in turn can have negative impacts on individuals’ physical and mental health. 
- **Light**: Studies have found that exposure to strong light sources later in the day can lead to more nocturnal awakenings and less slow-wave sleep, which is a portion of a sleep cycle that is vital to cell repair and bodily restoration. It is a good practice to keep the bedroom light levels as low as possible to promote sleep.

Hence, it is crucial to adjust the above factors properly to create an optimal sleep environment to improve the sleep quality. 

References:

[1] [https://www.goodpath.com/learn/statistics-on-insomnia-in-us-goodpath-results](Link) 
[2] [https://www.sleepfoundation.org/bedroom-environment](Link)

### **Simple Project Overview**

As a graduate student at [CU Boulder](https://www.colorado.edu/), I was fortunate to  develop a Low-Power-Bluetooth-based smart bedroom environment monitor with my partner [Kevin Tom](https://sites.google.com/view/kevintom/home) as a course project for one of my IoT courses. The monitor itself does not control home appliances to physically adjust the bedroom environment, but acts as a emulator to illustrate the process of how an simple IoT system can improve our sleep quality.  According to the research, we found the following conditions that are promoting better sleep:


- A temperature between 15.6C - 22.0C (changes from person to person)
- No noise disturbance (sound should be between 40db- 55 db)
- During bedtime the lux reading should be less than 180 (inside the house and less than 5 after lights are turned off (inside the bedroom).

The prototype monitors and 'adjusts' the above three factors of the bedroom environment using 3 sensors so as to meet the above optimal conditions. In addition, the prototype enables the user to sleep better at any time by promoting the user to set the sleep time and configuring temperature, light and noise values correspondingly and automatically.

### **Software & Hardware Architecture**

The system is developed on top of the BLE 4+ technology with the following hardware components:

1. ISL29125  light sensor
2. SEN-14262 sound sensor
3. Si7021 temperature & humidity sensor
4. Two Gecko develop kit with EFR32BG13 radio boards

![hw-block-diagram.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1652294110127/nI0e50kL9.png align="left")

The hardware block diagram shown above is the gist of hardware being used in the project. The sound sensor is controlled via ADC0 and the light & temperature sensors are controlled via I2C0. The main two parts are GATT server and GATT Client, as the system works using a GATT client-server architecture:

![sw-flow.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1652294236103/ucNNkD1T0.png align="left")

The system allows the user to supply `sleep_hour` and `sleep_time` as inputs to the system which will 'adjust' the environment based on the corresponding optimal conditions for high sleep quality.


![interact.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1652294486939/SICUunDPR.png align="left")

### **Demo Screenshots**

The following screenshots are taken when performing a LIVE demo, and these screenshots together demonstrate the typical usage of the system.

The client device is initializing:
![init_client.jpg](https://cdn.hashnode.com/res/hashnode/image/upload/v1652294531364/EamZn3cjl.jpg align="left")

The server device is reading sensors' values:
![sensor_read.jpg](https://cdn.hashnode.com/res/hashnode/image/upload/v1652294556757/-AtlJ5oTn.jpg align="left")

The user is inputting the sleep time:
![input_sleep_time.jpg](https://cdn.hashnode.com/res/hashnode/image/upload/v1652294579982/ifvUHaF1J.jpg align="left")

The user is inputting the time reference:
![input_ref.jpg](https://cdn.hashnode.com/res/hashnode/image/upload/v1652294604843/O1sletsdo.jpg align="left")

The user is inputting the sleep hours:
![input_sleep_hrs.jpg](https://cdn.hashnode.com/res/hashnode/image/upload/v1652294627517/norVqL7OH.jpg align="left")

The server is adjusting the bedroom environment:
![server_adjusting_env.jpg](https://cdn.hashnode.com/res/hashnode/image/upload/v1652294657451/uTuTVWobf.jpg align="left")

The client is updating the remaining sleep hours:
![client_changing_sleep_hrs.jpg](https://cdn.hashnode.com/res/hashnode/image/upload/v1652294679510/j1ZlS1_an.jpg align="left")

### **Future Improvement**

The project is currently closed for development, but there are certainly a lot of improvements to be made. Several key pending improvements are the following:

1. Lower the energy consumption on the server by powering down the sound sensor with the help of a voltage regulator
2. Achieve full parallelism by resolving I2C bus contention issues 
3. Add a feature to allow users to change the settings during the sleep period
4. Establish a ecosystem to physically control home appliance such as air conditioner, etc.

For people who are interested in this project, please feel free to check it out at 
https://github.com/ShuranXu/BLE-Smart-Bedroom-Environment-Monitor

### **Final Remarks**

The prototpye presented here is just to demonstrate how BLE technology can improve our living quality with a simple application. There is no deny that commerical home automation products have already enabled us to live with a better standard, simple DIY BLE projects enables us even more to address our unique needs.