### SYSFS

在Navio2上，PWM、ADC、SBUS和PPM都被集成进Linux的sysfs文件系统中，你可以从任何编程语言中访问它们。Sysfs是一种虚拟文件系统，它可以将内核和硬件设备的信息导出到用户空间。

例如，可以通过`/sys/class/pwm/pwmchip0/`访问PWM的属性，或者通过`/sys/kernel/rcio/rcin/`访问RC输入的结构。

### RCIO状态

如果你遇到PWM生成或者RC输入解码相关的问题，请检查Navio的板载RCIO状态。

使用下面的命令获得状态：

```bash
pi@navio:~ $ cat /sys/kernel/rcio/status/alive
```

如果输出是`1`，那么表示RCIO已经开启并且Raspberry Pi可以检测到它。

`0`表示RCIO没有连接。优先你需要检查Navio2和Raspberry Pi之间的所有的硬件连接。Navio2应该按照[硬件设置文档](https://docs.emlid.com/navio2/Navio-APM/hardware-setup/#attaching-navio2-to-a-raspberry-pi)所说用螺丝固定好。
