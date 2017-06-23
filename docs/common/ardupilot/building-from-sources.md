## 从哪里获得代码

Navio2代码在上游的[Ardupilot的主代码仓库](https://github.com/ArduPilot/ardupilot)中。

## 如何构建

Navio2的Ardupilot二进制文件有两种构建方式：

1) 直接在Raspberry Pi上构建。过程更简单，但是会比较慢。整个构建过程需要大概15分钟。

2) 在Linux PC或者虚拟机上使用交叉编译器构建。会快很多，但是需要设置一次交叉编译环境。

如果你想要在Raspberry Pi上构建，可以跳过下一节。

## 在Linux上设置交叉编译环境（可选）

我们推荐使用Raspberry Pi基金会指定的交叉编译器。下载这个编译器，然后把它随便保存在什么位置，例如`/opt/`：

```bash
sudo git clone --depth 1 https://github.com/raspberrypi/tools.git /opt/tools
```

如果你使用的是32位的发行版，那么将下面的内容添加到你的`PATH`里：

```bash
export PATH=/opt/tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian/bin:$PATH
```

如果你使用的是64位的发行版，那么将下面的内容添加到你的`PATH`里：

```bash
export PATH=/opt/tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian-x64/bin:$PATH
```

如果你想把编译器永久地添加到`PATH`中，那么请编辑`/etc/environment`。

## 使用Waf构建Ardupilot

无论是直接在RPi上编译，还是在计算机上交叉编译，下面的步骤都是通用的。

下载Ardupilot代码并更新子模块：

```bash
git clone https://github.com/ArduPilot/ardupilot.git
cd ardupilot
git submodule update --init
```  

请用标签签出一个版本编译，尽量不要直接用master飞行。
<sub>可以用`git tag`命令获取标签列表</sub>

例如，如果要构建多轴固件，请使用

```bash
git c
heckout ArduCopter-stable
```

或者

```bash
git checkout ArduCopter-beta
```

注意，一定要从ardupilot的根目录调用Waf。

为了少敲几个字母，可以为Waf设置Bash别名：

```bash
alias waf="$PWD/modules/waf/waf-light"
```

不同于基于make的构建系统，你需要先配置一下需要为哪个飞控板编译：

```bash
waf configure --board=navio2
```

现在你就可以构建arducopter了。如果你的机架是多轴，请使用如下命令：

```bash
waf copter
```

编译完成后，会在`ardupilot/build/navio2/bin/`目录生成一个名为`arducopter-quad`的二进制文件。

!!! tip
    我们建议阅读[ArduPilot的文档](https://github.com/ArduPilot/ardupilot/blob/master/BUILD.md)了解最新的信息。

如果你执行的是交叉编译，那么请将生成的二进制文件传输到你的Raspberry Pi：

```bash
rsync -avz ardupilot/build/navio2/bin/arducopter-quad pi@192.168.1.3:/home/pi/
```

其中，192.168.1.3是你的RPi的IP地址。

如何在RPi上执行Ardupilot并连接地面站的文档可以看[安装并执行Ardupilot](installation-and-running.md)。
