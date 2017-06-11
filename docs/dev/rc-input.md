## RC输入示例

作为一个自动驾驶应用，Navio需要解码来自RC的输入。Navio2支持S.Bus和PPM，这两种编码方式都是将所有PWM通道的信号编码到一个序列中，并通过一条信号线传送。这个被编码的信号会被解码并用于控制舵机。

RCInput这个例子显示了指定PWM通道的值。你可以在源代码中指定通道编号。将你的RC接收机的PPM或者S.Bus输出接入到Navio2的PPM/SB输入引脚。请注意，请在断电的情况下插入接收机。

如果你现在还没有下载过Navio2的驱动和示例代码，那么现在去[这里](navio-repository-cloning/)把它下载下来。

### C++

进入源码目录，编译并运行：

```bash
cd C++/Examples/RCInput
make
sudo ./RCInput
```
### Python

进入源码目录，运行：

```bash
cd Python
sudo python RCInput.py
```

这个程序会测量所选PWM通道并将测量结果输出到控制台。如果你在遥控器上做一些操作，例如拨动下油门，这些值会有相应的变化。

```bash
1087
1087
1087
1224
1322
1322
```

请阅读源码了解更详细的信息。请注意`rcin.init()`和`rcin.read()`函数。`rcin.init()`函数会初始化8个PWM通道。在这之后，才能通过`rcin.read()`函数读取0-7通道的值。这个函数需要一个指定通道编号的参数。
