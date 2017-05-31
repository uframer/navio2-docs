## 目前支持的飞控板

ArduPilot on Navio2可以运行在：

* Raspberry Pi 3 Model B
* Raspberry Pi 2 Model B

!!! note
    其他一些型号，例如Raspberry Pi Model A+、Raspberry Pi Model B+、Raspberry Pi Zero同Navio2都是电气兼容的，但是它们的性能不足以支撑ArduPilot:Copter的运行。将Navio2同这些型号连接是完全安全的。

## 将Navio2安装到Raspberry Pi

* Install spacers to the top side of Raspberry Pi and fix them with screws from the bottom.
* Connect extension header to the 40-pin gpio port.
* Attach Navio2 to the extension header.
* 使用螺丝固定Navio2。

![mount](img/navio2-mount.png)

## 为Navio2供电

!!! attention
    ALL POWER SOURCES SHOULD PROVIDE VOLTAGE IN 4.8-5.3V RANGE, OTHERWISE YOU CAN DAMAGE YOUR NAVIO2 AND RASPBERRY PI.**

Navio2可以通过三种方式供电，它们被理想二极管保护，所以你可以同时使用多种方式。

### 测试及开发适合的供电方式

Connect 5V 1A power adapter to the Raspberry Pi’s microUSB port. Raspberry Pi will provide power to the Navio2.

### 无人机上的供电方式

Navio2 should be powered by a power module connected to the “POWER” port on Navio2. Navio2会为Raspberry Pi供电。
![power-module](img/navio2-power-module.png)

### 冗余

如果电源模块失效了，Navio2会切换到舵机电轨取电。

## 通过舵机电轨供电

Power module does not power servos. To provide power to the servo rail plug your drone’s BEC into any free channel on the servo rail. Use BECs that provide voltage in a range of 4.8-5.3V. If you’d like to use high voltage servos use a power separation board.

![antenna](img/navio2-esc.png)

## GNSS天线

GNSS天线通过Navio2顶部的MCX接口连接。

![antenna](img/navio2-gnss-antenna.png)

## RC输入

Navio2 supports PPM and SBUS signals as an RC input. To connect receivers that do not support PPM output you can use PPM encoder. PPM receiver is powered by Navio2 and does not require power on the servo rail.

!!! important
    Do not connect servos to the RC receiver! Servos can consume a lot of power which RC receiver port may not be able to provide and that may lead to Raspberry Pi and Navio shutting down and even getting damaged.

Some of the receivers with PPM output:

### For ACCST (most FrSky transmitters)

* FrSky D4R-II 4ch 2.4Ghz ACCST Receiver
* FrSKY V8R7-SP ACCST 7 Channel RX with composite PPM
* FrSKY D8R-XP

### For FASST (Futaba & some FrSky trasmitters)

FrSky TFR4 4ch 2.4Ghz Surface/Air Receiver FASST Compatible

![rcin](img/navio2-rc-receiver.png)

## RC输出

### ESC

ESCs are connected to RC outputs labeled from 1 to 14 on a 2.54mm header.

Only one ESC power wire (central) should be connected to Navio2 servo rail, otherwise BECs built in ESCs will heat each other.

![escs](img/navio2-escs.png)

### 舵机

可以通过1到14号2.54mm舵机输出引脚连接舵机。

电源模块并不会给舵机供电。你需要用BEC通过舵机电轨给舵机供电。BEC也会作为Navio2的备份电源。

![servos](img/navio2-servos.png)

## 数传调制解调器

可以通过UART或者USB端口连接数传。

### UART数传

使用/dev/ttyAMA0串口连接UART数传。
![uartradio](img/navio2-uart-radio.png)

### USB数传

使用/dev/ttyUSB0虚拟串口连接USB数传。
![usbradio](img/navio2-usb-radio.png)

## 气压计的紫外线保护

MS5611气压计（钢壳的IC）对紫外线非常敏感，在阳光下输出的高度可能会跳变。使用一块泡沫把气压计覆盖起来是很有必要的，或者你也可以为Navio2加一个保护壳，既能遮蔽阳光也能屏蔽气流。

![barouvprotection](img/baro-uv-protection.jpg)


## 减震支架

我们为Navio设计了3D打印的减震支架，它可以简化安装方式，同时消除震动。  
底视图：
![vibronavio](img/vibro-bottom-view.png)
Navio2减震支架安装图：
![anti-vibration-mount](img/anti-vibration-mount.jpg)


STL文件：  
[上半部](https://github.com/emlid/hardware/blob/master/VibroNavio2top_rev_A.STL)  
[下半部](https://github.com/emlid/hardware/blob/master/VibroNavio2bot_rev_A.STL)  

你还需要8个减震球，例如[Hobbyking](https://hobbyking.com/en_us/vibration-damping-ball-65g-bag-of-8.html)上的这个。
![damping-balls](img/damping-balls.jpg)
