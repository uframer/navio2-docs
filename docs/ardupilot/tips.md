### 辅助功能开关

目前我们还不支持通道5-8上的辅助功能开关，操作它们可能会造成在电机通道上产生错误的PWM信号。

!!! danger " "
    一定不要在通道5-8上设置辅助功能开关!

### 第二罗盘的配置

Navio2包含两个罗盘 - AK8963和LSM9DS1。后者的偏移更小并被设置为主罗盘。默认禁用AK8963。  

W我们将带领你一步一步的完成板载罗盘的校准。

## 板载罗盘校准

依次进入`Initial Setup` - `Mandatory Hardwawre` - `Compass`

- 如果你启用了两个罗盘，那么在`Compass #2`页面勾选`Use this compass`
- 在`Onboard Mag Calibration`标签页点击`Start`按钮
- 分别绕X、Y、Z三个轴旋转你的无人机
- 等待校准完成（完成时会弹出类似于如下的消息）

![compass-onboard-calibration](img/compass-onboard-calibration.png)

- 点击`Accept`按钮（有可能点了之后什么反应也没有）
- 切换到另一个视图然后再切换回`Compass Calibration`
- 请确认前面校准得到的偏移量已经被成功保存

### 电压和电流测量

如果你使用Navio2原装的电源模块，可以从它得到电压和电流的读数。

![PM](img/navio2-power-module.png)

请按照如下步骤设置ArduPilot中的电压和电流测量：

- 在Mission Planner中切换到`Initial Setup`标签页
- 找到`Optional Hardware` - `Battery Monitor`
- 按照下图设置`Monitor`、`Sensor`和`ArduPilot Ver`的参数

![BatteryMonitor](img/mp-battery-monitor.png)

接下来你需要重启ArduPilot然后重新连接Mission Planner：

- 使用快捷键`Ctrl-F`打开`Temp Screen`
- 电机`reboot pixhawk`按钮
- 在主菜单栏上点击`DISCONNECT`，然后再点击`CONNECT`

完成上述操作之后，你就可以在`Flight Data`屏幕上见到电压和电流的读数了。

你还可以查看完整的参数列表：

```bash
BATT_CURR_PIN 3
BATT_VOLT_PIN 2
```
### 进阶配置

由于其他的Ardupilot配置过程同大多数的Ardupilot飞控过程很相似，所以请直接参考Ardupilot的文档。

[硬件配置](http://ardupilot.org/copter/docs/configuring-hardware.html)

[ESC校准](http://ardupilot.org/copter/docs/esc-calibration.html)

[开启RC失效保护](http://ardupilot.org/copter/docs/radio-failsafe.html)!
