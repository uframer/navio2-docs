## 概览

你可以在Raspberry Pi 3/2+Navio的设备上使用ArduPilot，ArduPilot的代码直接运行在Raspberry Pi上。请使用我们提供的预配置的Raspbian发行版。  

## 当前的设备版本

Emlid Raspbian已经预置了ArduPilot，它包含对所有机型/设备的支持并且基于目前最稳定的分支：

* ArduPlane: **3.7.1**
* ArduRover: **3.1.2**
* ArduCopter: **3.4.6**

## 欢迎文本

通过ssh登录Raspberry Pi后，你会看到下面这样的消息：

```
To enable ArduPilot on boot:
STEP 1:
Choose your vehicle, board and ArduPilot version using update-alternatives
(Please, read carefully all options and select appropriate one for either Navio 2 or Navio+)
        - sudo update-alternatives --config arducopter
        - sudo update-alternatives --config arduplane
        - sudo update-alternatives --config ardurover

STEP 2:
Set your GCS IP
        - sudo nano /etc/default/arducopter
        - sudo nano /etc/default/arduplane
        - sudo nano /etc/default/ardurover
STEP 3:
Reload configuration by issuing these commands
        - sudo systemctl daemon-reload

To start ardupilot (this won't load ArduPilot on boot):
        - sudo systemctl start arducopter
        - sudo systemctl start arduplane
        - sudo systemctl start ardurover


STEP 4:
Enable service on boot:
        - sudo systemctl enable arducopter
        - sudo systemctl enable arduplane
        - sudo systemctl enable ardurover

To disable ArduPilot on boot:
        - sudo systemctl disable arducopter
        - sudo systemctl disable arduplane
        - sudo systemctl disable ardurover
```

下面我们分节详细介绍详细内容。

## Systemd

我们使用`systemd`启动ArduPilot。主要用到的命令是`systemctl`。它的功能包括但不限于：

* 检查系统状态
* 管理系统和服务

更多细节请查看`man systemctl`手册。

## 选择设备类型、版本和飞控板

所有的ArduPilot二进制文件都被安装到`/opt/ardupilot/ardu[vehicle]-[ap_major_version]/bin`中。我们使用`update-alternatives`工具来切换所用的二进制文件。这个工具负责维护指向`/usr/bin/arducopter`、`/usr/bin/arduplane`和`/usr/bin/ardurover`。

在下面的例子中我们会使用arducopter，但是你可以按照自己的需要选择arduplane或者ardurover。

```bash
pi@navio: ~ sudo update-alternatives --config arducopter
```

该命令的输出类似于：

```bash
There are 18 choices for the alternative arducopter
(providing /usr/bin/arducopter).

Selection Path                                               Priority  Status
-----------------------------------------------------------------------------
  0    /opt/ardupilot/navio2/arducopter-3.4/bin/arducopter-y6     50   auto
  1    /opt/ardupilot/navio/arducopter-3.4/bin/arducopter-coax    50   manual
  2    /opt/ardupilot/navio/arducopter-3.4/bin/arducopter-heli    50   manual
  ...
  15   /opt/ardupilot/navio2/arducopter-3.4/bin/arducopter-quad   50   manual
  16   /opt/ardupilot/navio2/arducopter-3.4/bin/arducopter-single 50   manual
  17   /opt/ardupilot/navio2/arducopter-3.4/bin/arducopter-tri    50   manual
* 18   /opt/ardupilot/navio2/arducopter-3.4/bin/arducopter-y6     50   manual

Press enter to keep the current choice[*], or type selection number:
```

第一行显示了会默认启动的设备。如果你想选择另一个机架、飞控板或者版本，那么就从列表里选择对应的条目。

例如，如果你基于Navio 2构建了一个四轴飞行器，并想要使用ArduCopter-3.4，那么可以输入15然后按回车键。


## 指定启动选项

打开文件：

```bash
pi@navio: ~ $ sudo nano /etc/default/arducopter
```

在里面可以指定地面站的IP。

```bash
TELEM1="-A udp:127.0.0.1:14550"
#TELEM2="-C /dev/ttyAMA0"

# Options to pass to ArduPilot
ARDUPILOT_OPTS="$TELEM1 $TELEM2"

# -A is a console switch (usually this is a Wi-Fi link)

# -C is a telemetry switch
# Usually this is either /dev/ttyAMA0 - UART connector on your Navio
# or /dev/ttyUSB0 if you're using a serial to USB convertor

# -B or -E is used to specify non default GPS
```

所有以`#`开头的行都是注释，不会影响设置。

例如，你可以向下面这样将TELEM1指向IP地址：

`TELEM1`="-A udp:192.168.1.2:14550"

其中，`192.168.1.2`是地面站（你的笔记本、智能手机等）的IP地址。

!!! tip
    你可以向`ARDUPILOT_OPTS`添加额外的选项，这些选项会被传递给ArduPilot，例如添加一个新的`TELEM3`环境变量然后把它设置给`ARDUPILOT_OPTS`：`TELEM3="-E /dev/ttyUSB1" ARDUPILOT_OPTS="$TELEM1 $TELEM2 $TELEM3"`

命令行选项和串口（也可以使用TCP或者UDP替代串口）的对应关系如下：

* -A - serial 0 (always console; default baud rate 115200)  
* -C - serial 1 (normally telemetry 1; default baud rate 57600)  
  * 3DR Radio默认配置为57600，所以最简单的方式是通过`-C`选项连接。
* -D - serial 2 (normally telemetry 2; default baud rate 57600)  
* -B - serial 3 (normally 1st GPS; default baud rate 38400)  
* -E - serial 4 (normally 2st GPS; default baud rate 38400)  
* -F - serial 5  

此外还请阅读Mission Planner的[串口参数列表](http://ardupilot.org/copter/docs/parameters.html?highlight=serial#serial-parameters)。

当使用UART连接串口时，请留意默认的串口波特率。   


## 重新加载配置

如果你在前面的步骤中修改了配置，那么你需要通过systemd重新加载配置：

```bash
pi@navio: ~ $ sudo systemctl daemon-reload
```

## 启动

现在你可以启动ArduPilot：

```bash
pi@navio: ~ $ sudo systemctl start arducopter
```

可以使用下面的命令停止服务：

```bash
pi@navio: ~ $ sudo systemctl stop arducopter
```

## 在引导时自动启动

可以使用如下命令在开机时自动启动`arducopter`：

```bash
pi@navio: ~ $ sudo systemctl enable arducopter
```

可以使用如下命令禁用自动启动：

```bash
pi@navio: ~ $ sudo systemctl disable arducopter
```

可以使用如下命令检查ArduPilot是否成功开启：

```bash
pi@navio: ~ $ systemctl is-enabled arducopter
```


## 连接到地面站

### Mission Planner

Mission Planner只支持Windows平台。不过，它的功能是最完整的。

### QGroundControl

QGroundControl是一个跨平台的地面站，支持使用Mavlink的飞行软件栈（例如Ardupilot）。

### APM Planner

APM Planner是专为Ardupilot开发的地面站软件。可以从[ardupilot.com](http://ardupilot.com/downloads/?category=35)下载。

APM Planner会监听UDP的14550端口，因此它应该可以自动捕捉到Navio的数传数据。

### MAVProxy

MAVProxy是一个面向控制台的地面站软件，用Python开发。它很适合高级用户和开发者使用。

可以参考[下载并安装](http://ardupilot.github.io/MAVProxy/html/getting_started/download_and_installation.html)里的步骤安装MAVProxy。


在运行MAVProxy时，请通过`--master`参数指定端口，这个端口可以是串口、TCP或者UDP。还可以通过`--out`选项让它将收到的数据转发出来。

```bash
pi@navio: ~ $ mavproxy.py --master 192.168.1.2:14550 --console
```

其中，192.168.1.2是地面站的IP，不是RPi的IP。

## 启动一个自定义的ArduPilot二进制程序

Navio2在ArduPilot上游项目中直接支持，如果你想要亲手构建二进制文件，那么请参考[从源码构建](building-from-sources.md)。你还可以从arudupilot buildserver直接下载最新的稳定版二进制文件。例如，如果想要下载arducopter-quad：

```bash
pi@navio: ~ $ wget http://firmware.eu.ardupilot.org/Copter/stable/navio2-quad/arducopter-quad
pi@navio: ~ $ chmod +x arducopter-quad
```

如果想要启动其他类型机架的文件，修改后缀即可，例如，改成`/navio2-hexa/arducopter-hexa`。目前支持的类型包括：

* ArduRover
* ArduPlane
* ArduPlane-tri
* ArduCopter-quad
* ArduCopter-tri
* ArduCopter-hexa
* ArduCopter-y6
* ArduCopter-octa
* ArduCopter-octa-quad
* ArduCopter-heli
* ArduCopter-single

如果你想要启动自定义的二进制文件，那么应该去修改`/etc/systemd/system/ardupilot.service`文件。

```bash
[Unit]
Description=ArduPilot for Linux
After=systemd-modules-load.service
Documentation=https://docs.emlid.com/navio2/navio-ardupilot/installation-and-running/#autostarting-ardupilot-on-boot
Conflicts=arduplane.service arducopter.service ardurover.service

[Service]
EnvironmentFile=/etc/default/ardupilot

###############################################################################
####### DO NOT EDIT ABOVE THIS LINE UNLESS YOU KNOW WHAT YOU"RE DOING #########
###############################################################################

# Uncomment and modify this line if you want to launch your own binary
#ExecStart=/bin/sh -c "/home/pi/<path>/<to>/<your>/<binary> ${ARDUPILOT_OPTS}"

##### CAUTION ######
# There should be only one uncommented ExecStart in this file

###############################################################################
######## DO NOT EDIT BELOW THIS LINE UNLESS YOU KNOW WHAT YOU"RE DOING ########
###############################################################################

Restart=on-failure

[Install]
WantedBy=multi-user.target
```

上面的注释很好理解。你唯一需要调整的内容是把下面这行：

```bash
#ExecStart=/bin/sh -c "/home/pi/<path>/<to>/<your>/<binary> ${ARDUPILOT_OPTS}"
```

修改成类似下面这样：

```bash
ExecStart=/bin/sh -c "/home/pi/arducopter-quad ${ARDUPILOT_OPTS}"
```

除此以外，还有另一个不同之处是你需要用`ardupilot`这个systemd服务名，而不是`arducopter`/`arduplane`/`ardurover`，并且需要修改`/etc/default/ardupilot`文件来调整`ARDUPILOT_OPTS`选项。

下面的命令会启动自定义的ArduPilot二进制文件并且将其标记为引导时启动：

```bash
pi@navio: ~ sudo systemctl start ardupilot && sudo systemctl enable ardupilot
```
