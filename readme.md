# DAP-Link(Type-C)

![](https://img.shields.io/badge/license-CC4.0-brightgreen.svg)
* Author: [brian1024](https://github.com/brian1024) ,[JassyL](https://github.com/JassyL/DAP_Link)
* Date: 2019.06.22
![](http://qiniu.datasheep.cn/20190622232059.png-syg)


# 描述

主控芯片为**STM32F103CBT6**，硬件原理图基于[mbed-HDK](https://github.com/armmbed/mbed-HDK)，软件基于[DAPLink](https://github.com/ARMmbed/DAPLink)，在此基础上修改了USB接口，对PCB进行了重新布局，整理了固件下载流程，方便爱好者制作使用。DAP-Link具有HID(Human Interface Device)、MSC(Mass Storage Device)、CDC(Communication Device)、WEBUSB等接口，有关DAP-Link的详细描述请参考仓库：[DAPLink](https://github.com/ARMmbed/DAPLink)。

# 用户指南
(参考：[DAPLink/docs/USERS-GUIDE.md](https://github.com/ARMmbed/DAPLink/blob/master/docs/USERS-GUIDE.md))


## DAPLink用户指南
DAPLink提供三种接口， 分别是拖放式编程，串口和调试支持。 此外，你可以使用bootloader的拖放式编程接口来更新DAPLink固件。
### 拖放式编程
通过复制或保存到DAPLink驱动器所支持格式之一的文件来编程目标MCU。完成操作后，驱动器将重新安装。 如果不成功，驱动器上会出现“FAIL.TXT”文件，其中包含有关失败的信息。
支持的文件格式：
- bin文件
- Intel Hex文件
### 串口
串口直接连接到目标MCU，它允许双向通信。 它还允许通过串口发送break命令来重置目标MCU。
支持的波特率：
- 9600
- 14400
- 19200
- 28800
- 38400
- 56000
- 57600
- 115200
注意：除了此处列出的波特率之外，大多数DAPLink还支持其它波特率。
### 调试
您可以使用任何支持CMSIS-DAP协议的IDE进行调试。 一些能够调试的工具是：
- [pyOCD](https://github.com/mbedmicro/pyOCD).
- [uVision](http://www.keil.com/).
- [IAR](https://www.iar.com/).
### 固件升级
要更新设备上的固件，请在连接USB时按住reset按钮。 设备启动进入bootloader模式。 从那里，将适当的固件复制到驱动器上。 如果成功，设备将退出bootloader模式并开始运行新固件。 否则，bootloader会显示“FAIL.TXT”，并说明出现了什么问题。



# DAPLink制作流程

(参考：[DAPLink/docs/DEVELOPERS-GUIDE.md](https://github.com/ARMmbed/DAPLink/blob/master/docs/DEVELOPERS-GUIDE.md))


### 设置

安装下面列出的必要工具。你可以跳过已存在兼容工具的任何步骤。
* 安装[Python 2,2.7.11或更高版本](https://www.python.org/downloads/),并添加到`PATH`。
* 安装[Git](https://git-scm.com/downloads),并添加到`PATH`。
* 安装[Keil MDK-ARM](https://www.keil.com/download/product/),最好是版本5。如果未安装到默认位置，将环境变量“UV4”设置为UV4可执行文件的绝对路径。请注意，“UV4”是用于MDK版本4和5的。如果您打算使用mbed cli，可以跳过此步骤，但仍需要Arm Compiler 5和MDK进行调试。
* 在Python全局安装中安装virtualenv，例如：`pip install virtualenv`。


#### 初始设置
获取源并创建虚拟环境
```
$ git clone https://github.com/ARMmbed/DAPLink
$ cd DAPLink
$ pip install virtualenv
$ virtualenv venv
```
#### 激活虚拟环境
 激活虚拟环境并更新依赖。 
```
$ venv/Scripts/activate (For Linux)
$ venv/Scripts/activate.bat (For Windows)
$ pip install -r requirements.txt
```
#### 构建
**每次推送新的更改时都应该这样做**
有两种方法可以构建DAPLink。你可以生成Keil MDK项目文件并在MDK中构建。 MDK还用于调试在接口芯片上运行的DAPLink。或者，你可以使用`mbedcli_compile.py`脚本从命令行构建项目，而无需MDK。
#### 生成MDK项目文件并使用MDK编译
此命令在`projectfiles / uvision`目录下生成MDK项目文件。
![](http://qiniu.datasheep.cn/20190622233249.png-syg)

```
$ progen generate -t uvision
```
仅要生成一个特定项目，请使用如下命令：
```
progen generate -f stm32f103rb.yaml -p stm32f103xb_stm32f746zg_if -t uvision
```
通过`progen`的这些选项设置参数：
- `-f` 设置输入项目文件
- `-p` 设置项目名
- `-t` 指定IDE名称

要仅构建项目的子集，请将项目名称添加到命令行的末尾。在`--help`显示的用法文本中列出了有效的项目名称。每次拉取后第一次构建应该添加`--clean`来执行一次完整重构。

#### 使用MDK下载固件
如果要使用MDK(uVision)IDE来处理DAPLink代码，则必须在适当的环境中启动它。 否则该项目将无法建立。 要正确启动uVision，请使用``tools/launch_uvision.bat``
此脚本可以使用参数来覆盖要安装的默认虚拟环境和python包。 例如`tools \ launch_uvision.bat other_env other_requirements.txt`

![](http://qiniu.datasheep.cn/20190622233351.png-syg)

启动后选择你所生成的项目，编译下载到目标板，完成制作。

![](http://qiniu.datasheep.cn/20190622233443.png-syg)

如图，正在下载固件：
![](http://qiniu.datasheep.cn/20190622234012.png-syg)

下载完成后接入电脑：
![](http://qiniu.datasheep.cn/20190622234320.png-syg)

#### 关于拖拽式编程
单一固件只支持单一目标芯片，目前官方支持的芯片如图，选择不同的固件刷入则支持不同的芯片，网上有自行添加芯片的教程，如有需要自行搜索。

![](http://qiniu.datasheep.cn/20190622234604.png-syg)

#### 关于下载速度
查找相关资料，STM32F103CBT6只支持USB全速设备，全速HID设备单个包最大64KB，所以速度无法提升，网络上有人成功使用F4系列制作了支持USB高速设备的DAPLink，单包可达512KB，速度有很大提升，日常使用也不必纠结这个。

#### 官方电路图的修改
除了将USB接口改为Type-C以及增加自恢复保险以外，还增加了在USB的D+上的1.5k上拉电阻，资料显示STM32F103CBT6是没有自带此电阻的，而USB在上电时需要在D+上接1.5k电阻到3.3v才能识别为全速或高速设备，而实际测试此电阻也是必须的，故添加。如有错误请指正。

![](http://qiniu.datasheep.cn/20190622235238.png-syg)




