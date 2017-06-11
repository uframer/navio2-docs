#### ArduPilot

你可以在Navio2+Raspberry Pi的组合上直接运行ArduPilot。ArduPilot的代码直接运行在Raspberry Pi上。为了使Ardupilot能够正常工作，请使用我们提供的预先配置过的Raspbian发行版。

#### 安装ArduPilot

最新版本的Emlid Raspbian继承了ArduPilot软件包。它包含对所有机型/设备的支持，并且给予最新的稳定版代码。目前包括：

* ArduPlane 3.7.0
* ArduRover 3.0.1
* ArduCopter 3.4.3-rc1

```bash
pi@navio: ~ $ sudo apt-get update && sudo apt-get install apm-navio2
```

Navio2在ArduPilot上游项目中直接支持，如果你想要亲手构建二进制文件，那么请参考[从源码构建](building-from-sources.md)。

#### 下载稳定版二进制文件

你还可以从arudupilot buildserver直接下载最新的稳定版二进制文件。例如，如果想要下载arducopter-quad：

```bash
pi@navio: ~ $ wget http://firmware.eu.ardupilot.org/Copter/stable/navio2-quad/arducopter-quad
pi@navio: ~ $ chmod +x arducopter-quad
```

如果想要卸载其他类型机架的文件，修改后缀即可，例如，改成`/navio2-hexa/arducopter-hexa`。目前支持的类型包括：

* ArduRover
* ArduPlane
* ArduCopter-quad
* ArduCopter-tri
* ArduCopter-hexa
* ArduCopter-y6
* ArduCopter-octa
* ArduCopter-octa-quad
* ArduCopter-heli
* ArduCopter-single

Navio2在ArduPilot上游项目中直接支持，如果你想要亲手构建二进制文件，那么请参考[从源码构建](building-from-sources.md)。

#### Systemd

我们使用`systemd`启动ArduPilot。主要用到的命令是`systemctl`。它的功能包括但不限于：
- 检查系统状态
- 管理系统和服务

更多细节请查看`man systemctl`手册。

#### 运行ArduPilot

如果你是第一次运行ArduPilot，那么需要先做一些准备工作。

首先，你需要编辑`ardupilot.service`文件，选择你的ArduPilot设备类型。

打开文件：

```bash
pi@navio: ~ $ sudo nano /lib/systemd/system/ardupilot.service
```

所有以`#`开头的行都是注释，不会影响ArduPilot服务的设置。所以你只需要去掉想要开启的那种类型设备的那一行前面的`#`。例如，如果你想要运行，那么就像下面那样修改：

```bash
### Uncomment one of these to launch quadcopter/plane or rover on Navio 2 #####

#ExecStart=/opt/ardupilot/navio2/arducopter/bin/arducopter-quad $ARDUPILOT_OPTS
#ExecStart=/opt/ardupilot/navio2/arducopter/bin/arducopter-hexa $ARDUPILOT_OPTS
ExecStart=/opt/ardupilot/navio2/arducopter/bin/arducopter-octa $ARDUPILOT_OPTS
#ExecStart=/opt/ardupilot/navio2/arducopter/bin/arducopter-single $ARDUPILOT_OPTS
#ExecStart=/opt/ardupilot/navio2/arduplane/bin/arduplane $ARDUPILOT_OPTS
```

如果你的二进制文件是自己构建的或者是从网上下载的，那么需要像下面这样修改文件最后一部分：

```bash
# Uncomment and modify this line if you want to launch your own binary
#ExecStart=/home/pi/<path>/<to>/<your>/<binary> $ARDUPILOT_OPTS
```

例如，将`<path>/<to>/<your>/<binary>`改为`downloads/arducopter-quad`。


下面你需要修改启动ArduPilot使用的参数。打开`/etc/default/ardupilot`文件：

```bash
pi@navio: ~ $ sudo nano /etc/default/ardupilot
```

这里你可以指定地面站的IP和另一个ArduPilot的标准参数：

```bash
# Options to pass to ArduPilot
ARDUPILOT_OPTS="-A udp:192.168.1.2:14550 -C /dev/ttyAMA0"
```

其中，`192.168.1.2`是地面站（你的笔记本、智能手机等）的IP地址。

开关和串口（可以使用TCP和UDP替换串口）之间的映射如下：

* -A - serial 0 (always console; default baud rate 115200)  
* -C - serial 1 (normally telemetry 1; default baud rate 57600)  
* -D - serial 2 (normally telemetry 2; default baud rate 57600)  
* -B - serial 3 (normally 1st GPS; default baud rate 38400)  
* -E - serial 4 (normally 2st GPS; default baud rate 38400)  
* -F - serial 5  

此外还请阅读Mission Planner的[串口参数列表](http://ardupilot.org/copter/docs/parameters.html?highlight=serial#serial-parameters)。

当使用UART做数传时，默认的波特率为：

* 115200 for primary (-A)  
* 57600 for secondary (-C)  

3DR Radio数传的默认波特率是57600，因此最简单的连接方式是使用`-C`选项。如果你想要Navio上的UART口做数传，那么需要这样做：

```bash
ARDUPILOT_OPTS="-C /dev/ttyAMA0"
```

UDP和串口数传可以同时使用：

```bash
ARDUPILOT_OPTS="-A udp:192.168.1.2:14550 -C /dev/ttyAMA0"
```


修改完成后，使用如下命令重新加载配置：

```bash
pi@navio: ~ $ sudo systemctl daemon-reload
```

*注意：只有在修改了机架类型或者ArduPilot参数时才需要执行上面的命令。*

可以使用如下命令启动ArduPilot：

```bash
pi@navio: ~ $ sudo systemctl start ardupilot.service
```

可以使用如下命令停止服务：

```bash
pi@navio: ~ $ sudo systemctl stop ardupilot.service
```

#### 开机时自动启动ArduPilot

可以使用如下命令在开机时自动启动ArduPilot：

```bash
pi@navio: ~ $ sudo systemctl enable ardupilot.service
```

可以使用如下命令禁用自动启动：

```bash
pi@navio: ~ $ sudo systemctl disable ardupilot.service
```

可以使用如下命令检查ArduPilot是否成功开启：

```bash
pi@navio: ~ $ systemctl is-enabled ardupilot.service
```


#### 连接到地面站

**Mission Planner**

Mission Planner只支持Windows平台。不过，它的功能是最完整的。

**QGroundControl**

QGroundControl是一个跨平台的地面站，支持使用Mavlink的飞行软件栈（例如Ardupilot）。

**APM Planner**

APM Planner是专为Ardupilot开发的地面站软件。可以从[ardupilot.com](http://ardupilot.com/downloads/?category=35)下载。

APM Planner会监听UDP的14550端口，因此它应该可以自动捕捉到Navio的数传数据。

**MAVProxy**

MAVProxy是一个面向控制台的地面站软件，用Python开发。它很适合高级用户和开发者使用。

可以参考[下载并安装](http://ardupilot.github.io/MAVProxy/html/getting_started/download_and_installation.html)里的步骤安装MAVProxy。

在运行MAVProxy时，请通过`--master`参数指定端口，这个端口可以是串口、TCP或者UDP。还可以通过`--out`选项让它将收到的数据转发出来。

```bash
mavproxy.py --master 192.168.1.2:14550 --console
```

其中，192.168.1.2是地面站的IP，不是RPi的IP。
