## PWM输出

0-13号PWM通道对外通过编号为1-14的2.54mm针脚连接。舵机一般需要通过正确的频率（例如常见的50hz）和占空比（控制脉冲的长度，通常在1-2秒之间）来控制。

如果想通过这个示例来控制舵机，请将舵机连接到Navio的1号输出通道。不要忘了为舵机电轨供电。

如果你现在还没有下载过Navio2的驱动和示例代码，那么现在去[这里](navio-repository-cloning/)把它下载下来。

### C++

进入源码目录，编译并运行：

```bash
cd C++/Examples/Servo
make
sudo ./Servo
```

### Python

进入源码目录，运行：

```bash
cd Python
sudo python Servo.py
```

请阅读源码了解详细信息。注意`set_period`函数会根据第二个参数设定的频率来控制`PWM_OUTPUT`通道的周期。你可以通过修改`SERVO_MIN`和`SERVO_MAX`的值来修改脉冲范围的值。
