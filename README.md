# MSI-B460M-WIFI-10600-6600XT-BCM94352Z

hackintosh: OpenCore + MSI B460M Mortar + i5 10600 + 6600XT+BCM94352Z(更换板载AX200)

## 日志记录:

- 2022-08-10：准备安装 macOS 12.5 & OpenCore 0.8.0
- 2022-08-14：已成功安装 macOS 12.5 & OpenCore 0.8.0

## 硬件配置

| 配置        | 型号                           | 价格 | 渠道       |
| ----------- | ------------------------------ | ---- | ---------- |
| CPU         | Intel i5 10600                 | 1075 | 淘宝: 散片 |
| 主板        | 微星 MSI MAG B460M MORTAR WiFi | 400  | 闲鱼       |
| 显卡        | 技嘉 RX 6600XT 8G 猎鹰         | 1899 | 闲鱼       |
| 内存        | 光威 普条 DDR4 2666MHz 8G * 2  | 300  | 京东自营   |
| SSD         | WD SN550 500G                  | 350  | 京东自营   |
| 机箱        | 傻瓜超人K88                    | 253  | 淘宝       |
| 电源        | 长城V7  700W 全模组 +定制线    | 295  | 闲鱼       |
| CPU 风扇    | 乔思伯 CR1400                  | 0    | 机箱附送   |
| WiFi + 蓝牙 | BCM94352Z                      | 85   | 闲鱼       |

理论上10代U都是通用的

## 功能验证

- [X] CPU超线程和变频
- [X] UHD630
- [X] RX6600XT
- [X] 显卡传感器
- [X] 睡眠唤醒
- [X] 关机
- [X] WIFI
- [X] 蓝牙
- [X] 以太网，支持2.5G速率
- [X] 音频输出
- [X] 后面板麦克风输入
- [X] USB端口
- [X] iMessage, Facetime,
- [X] Handoff
- [X] Airdrop

## 待修复BUG

##### 1.加载苹果Logo时会显示黑屏

据观察可能为RX6000系的macOS驱动问题，加载完后可正常进入系统恢复显示，目前暂无解决方式

##### 部分USB端口失效

USB已定制，因端口数量限制屏蔽了部分端口，定制情况详见[USB定制](#USB定制)

## 安装过程

**请勿直接使用**

1. 准备好镜像文件，这里我直接使用的是黑果小兵的mac12.5镜像文件
2. 下载[最新EFI文件](https://github.com/GitHubYingDeng/MSI-B460M-WIFI-10600-6600XT/archive/refs/heads/main.zip)，解压复制EFI目录下文件至自己的EFI目录
3. Bios设置打开 `D.T.M`（微星自带黑苹果一键设置），关闭 `ResizeBar`(默认关，不清楚可以使用GPU-Z查看))
4. 参考黑果小兵的[安装教程](https://mp.weixin.qq.com/s/Vs0uxeQNh2dPxnC76KPNbA)
5. 参考[此处](https://dortania.github.io/OpenCore-Install-Guide/config.plist/comet-lake.html#platforminfo)或其他方式配置 `config.plist`三码，i7及以下使用iMac20,1机型，i9使用iMac20,2机型
6. Win+Mac双系统解决系统时间差问题
   在Windows下运行

```
Reg add HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation /v RealTimeIsUniversal /t REG_DWORD /d 1
```

7. 设置默认启动项
   在OC引导界面时按键盘的Ctrl+Enter进入系统，下次重启后默认就选中该项

## 注意事项

- 不适用未更换板载网卡的WIFI迫击炮，可能会卡在网卡代码那里,如果使用板载AX200网卡建议使用这位大佬的[EFI](https://github.com/Spectrelai/Hackintosh-B460M-MORTAR-WIFI)
- 三码需要保证唯一性，否则可能会影响Handoff，iMessage和FaceTime功能

EFI文件描述：

| 文件                              | 用途                                           |
| --------------------------------- | ---------------------------------------------- |
| BOOT/BOOTx64.efi                  | OpenCore 底包自带，不管，不动它                |
| OC/ACPI/SSDT-AWAC.aml             | ACPI 介绍见下方                                |
| OC/ACPI/SSDT-EC-USBX-DESKTOP.aml  | ACPI 介绍见下方                                |
| OC/ACPI/SSDT-PLUG.aml             | ACPI 介绍见下方                                |
| OC/Bootstrap/Bootstrap.efi        | OpenCore 底包自带，不管，不动它                |
| OC/Drivers/HfsPlus.efi            | 底包里其他驱动都可以删掉，这两个是必须的。     |
| OC/Drivers/OpenRuntime.efi        | 同上，必须的驱动                               |
| OC/Kexts/AppleALC.kext            | 声卡相关驱动，基本都需要，不过后续需要一些配置 |
| OC/Kexts/Lilu.kext                | 基础驱动，必须的                               |
| OC/Kexts/LucyRTL8125Ethernet.kext | 有线网卡驱动                                   |
| OC/Kexts/NVMeFix.kext             | 其他驱动，也基本都是必须的                     |
| OC/Kexts/SMCProcessor.kext        | VirtualSMC 的相关插件驱动，基本必须            |
| OC/Kexts/SMCSuperIO.kext          | 同上                                           |
| OC/Kexts/USBMap.kext              | USB 映射                                       |
| OC/Kexts/VirtualSMC.kext          | 基础驱动，必须                                 |
| OC/Kexts/WhateverGreen.kext       | 显卡相关必须驱动                               |
| OC/Kexts/XHCI-unsupported.kext    | 配合解决 USB 问题的驱动                        |
| OC/OpenCore.efi                   | 自带，不动它                                   |
| OC/Tools/OpenShell.efi            | Tools 里面其实都删差不多了                     |
| OC/config.plist                   | **重点编辑文件**                         |

## 定制参考说明

### USB定制

定制端口表

| 名称 | 类型             | 位置                     |
| ---- | ---------------- | ------------------------ |
| HS01 | USB2.0           | 后置面板网口旁USB-A口 1  |
| HS02 | USB2.0           | 后置面板网口旁USB-A口 2  |
| HS03 | USB2.0           | 后置面板USB-C口旁USB-A口 |
| HS04 | USB2.0 Type-C    | 后置面板USB-C口          |
| HS05 | USB2.0           | 主板扩展口JUSB3 1        |
| HS06 | USB2.0           | 主板扩展口JUSB3 2        |
| HS07 | USB2.0 Type-C    | 主板扩展口JUSB4          |
| HS08 | 内置蓝牙         | 主板内置                 |
| HS09 | USB2.0           | 后置面板PS2口旁USB-A口 1 |
| HS10 | USB2.0           | 后置面板PS2口旁USB-A口 2 |
| HS11 | 内置USB2.0Hub    | 主板扩展口JUSB1-2        |
| HS12 | 内置微星灯效控制 | 主板内置                 |
| SS01 | USB3.0           | 后置面板网口旁USB-A口 1  |
| SS02 | USB3.0           | 后置面板网口旁USB-A口 2  |
| SS03 | USB3.0           | 后置面板USB-C口旁USB-A口 |
| SS04 | USB3.0 Type-C    | 后置面板USB-C口          |
| SS05 | USB3.0           | 主板扩展口JUSB3 1        |
| SS06 | USB3.0           | 主板扩展口JUSB3 2        |
| SS07 | USB3.0 Type-C    | 主板扩展口JUSB4          |

特殊端口说明：

- HS08：蓝牙端口，必须包含，否则蓝牙失效
- HS11：内部USB2.0Hub端口，如果未使用JUSB1-2扩展口则可以屏蔽
- HS12：微星灯效端口，mac下没有适配，可屏蔽

默认屏蔽：HS11, HS12, SS01, SS02

如果默认的USB定制不符合你的接口使用情况，可根据此表重新定制USB端口，预置的USBMap.kext包含该表所有端口，在此基础上可以去除检测端口的步骤直接进行定制选择

#### 定制步骤

1. 根据说明下载定制程序：[USBMap](https://github.com/corpnewt/USBMap)
2. 如果没有Python环境，先下载安装[Python](https://www.python.org/downloads/)
3. 运行USBMapInjectorEdit.command(macOS)或USBMapInjectorEdit.bat(Windows)
4. 拖入EFI内的USBMap.kext
5. 根据程序说明选择你需要的端口，因macOS限制，所选端口数不能超过**15个**
6. 修改完成后退出程序，此时原USBMap.kext已完成修改

参考教程：

- https://github.com/Spectrelai/Hackintosh-B460M-MORTAR-WIFI/tree/main/EFI/OC/Kexts
- https://github.com/maemual/MSI-B460M-10700-5500XT
- https://github.com/myqqiu/Hackintosh-B460M-MORTAR-i5-10500-iGPU-UHD630
- https://dortania.github.io/OpenCore-Install-Guide/
- https://github.com/abner-xu/efi
- https://apple.sqlsec.com/
- https://www.bilibili.com/video/BV1uf4y1X7MT
