## 下载预配置的Raspbian映像

Navio2需要预先配置好的Raspbian映像。我们为RPi2和RPi3提供了统一的映像。这个映像不包含GUI，因为对于无人机应用来说我们不需要GUI。

[Download emlid-raspbian-20170323 SD card image](http://files.emlid.com/images/emlid-raspbian-20170323.img.xz), [(md5)](https://files.emlid.com/images/MD5SUMS)

## 将映像写入SD卡

* 下载最新的Emlid Raspbian映像。
* 下载、解压并用管理员权限运行[Etcher](https://etcher.io/)。
* 选择第一步解压出来的映像文件并指定sd卡的盘符。
* 点击“Flash!”。烧写过程可能需要几分钟时间。

<iframe  title="Emlid manuals" width="680" height="380" src="https://www.youtube.com/embed/i8_TFYWYt_M" allowfullscreen></iframe>

可以在[这里](http://www.raspberrypi.org/documentation/installation/installing-images/)了解详细信息。

## 配置Wi-Fi访问

Raspberry Pi 3内置了Wi-Fi模块，Raspberry Pi 2则需要使用外置USB Wi-Fi模块。你可以在[这里](http://elinux.org/RPi_USB_Wi-Fi_Adapters)查到可用的列表。

Wi-Fi网络可以通过编辑SD卡上的`/boot/wpa_supplicant.conf`文件来配置。如果你的WiFi AP使用的是PSK的方式配置，那么添加如下内容即可：

```bash
network={
  ssid="yourssid"
  psk="yourpasskey"
  key_mgmt=WPA-PSK
}
```

你可以通过如下方式之一编辑这个文件的内容：

### 在SD卡上编辑

将SD卡插入你的电脑，打开并编辑`/boot/wpa_supplicant.conf`（在Linux上需要root权限）。

### 外接显示器和键盘

分别通过HDMI接口和USB接口将显示器和键盘接入你的Raspberry Pi，启动RPi并登陆控制台，然后使用你喜欢的文本编辑器修改`/boot/wpa_supplicant.conf`。例如，在登陆后，输入如下命令：

```bash
sudo nano /boot/wpa_supplicant.conf
```

然后将文件内容修改为上面提到的内容，保存文件并重启RPi。

这个方法可能会遇到一些问题，因为我们这个发行版的内科可能同某些键盘不兼容。如果你的键盘不能正常工作，那么请尝试使用上一种方法。

### 使用以太网

你也可以通过网线将RPi接入以太网。

### 尝试使用Zeroconf连接

很有可能你可以直接通过Zeroconf协议连接到Raspberry Pi。

你可以在Mac或者Linux上使用`ssh pi@navio.local`，在Windows的Putty上使用`navio.local`。

如果连不上，那么请尝试下面的方法。

### 获得所用的IP地址

如果想要知道RPi所使用的IP地址，可以使用nmap工具。

从你的电脑上运行如下命令：

```bash
nmap -sn 192.168.1.*
```

你也可以使用如Zenmap或者Fing这样的图形界面应用程序。

在结果中查找主机名为”navio”的条目。

### Linux上的wpa_passphrase

如果你在Linux上或者直接在Raspberry Pi上编辑`wpa_supplicant.conf`文件，那么可以使用`wpa_passphrase`工具：

```bash
sudo bash -c "wpa_passphrase SSID password >> /boot/wpa_supplicant.conf"
```

## 升级

如果有必要，你可以使用下面的命令升级你的系统：

```bash
sudo apt-get update && sudo apt-get dist-upgrade
```
