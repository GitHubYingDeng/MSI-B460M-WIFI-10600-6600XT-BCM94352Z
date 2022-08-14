# MSI-B460M-WIFI-10600-6600XT

hackintosh: OpenCore + MSI B460M Mortar + i5 10600 + 6600XT+BCM94352Z

UPDATE:

- 2022-08-10：准备安装 macOS 12.5 & OpenCore 0.8.0
- 2022-08-14：已成功安装 macOS 12.5 & OpenCore 0.8.0

## 硬件配置

| 配置        | 型号                           | 价格 | 渠道       |
| ----------- | ------------------------------ | ---- | ---------- |
| CPU         | Intel i5 10600                 | 1075 | 淘宝: 散片 |
| 主板        | 微星 MSI MAG B460M MORTAR WiFi | 400  | 闲鱼       |
| 显卡        | 华硕  RX 6600XT 8G DUAL 雪豹   | 1800 | 闲鱼       |
| 内存        | 光威 普条 DDR4 2666MHz 8G * 2  | 300  | 京东自营   |
| SSD         | WD SN550 500G                  | 350  | 京东自营   |
| 机箱        | 傻瓜超人K88                    | 253  | 淘宝       |
| 电源        | 长城V7  700W 全模组 +定制线    | 295  | 闲鱼       |
| CPU 风扇    | 乔思伯 CR1400                  | 0    | 机箱附送   |
| WiFi + 蓝牙 | BCM94352Z                      | 85   | 闲鱼       |

2022 年 8 月 9 号，总计 4558元。

## 配置过程

基本上完全参考了 [OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/)

少量参考了 up 主司波图的[CometLake十代Intel平台台式机Opencore黑苹果通用配置教程（附安装包）](https://www.bilibili.com/video/BV1uf4y1X7MT)

对于详细的过程就不一一介绍了。不过非常强调的一点是，一定要仔仔细细的看好 OpenCore 官方教程的每一句话，正确理解每一句话的意思。有些 kext 扩展是某些特定平台特定硬件需要的，**你要是弄多了，反而会造成问题**。我的 efi 里基本上算是非常干净的了，都是必要的驱动。

#### Wi-Fi + 蓝牙

更换板载网卡AX200 为免驱的 **BCM94352Z** DW1560无线网卡 WIFI: 5G **867**Mbps 2.4G **300**Mbps bluetooth:**4.0**  兼容性待测试,此网卡有多个版本

#### 有线网卡

有线网卡是板载的 2.5G Realtek® RTL8125B，苹果用不了 2.5G 口，得在装好系统之后，手工在系统设置里，有线网卡从自动改成 1000baseT，强制用千兆就可以了。

### 第二步：创建 USB 镜像

我是在白苹果 macOS 下配置的，准备好一个应该是 16G 以上的 U 盘。

按照[教程](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/)配置就好了。

把 OpenCorePkg 的底包里的 EFI 目录丢到 U盘的 EFI 挂载分区下，然后删除一些没用的文件就好了。

### 第三步：收集驱动文件

这一步是最关键的，搜集自己需要的各种驱动丢到对应目录里。然后配置 config.plist 文件。

展示以下我的最终文件目录树：

```
.
├── EFI
│   ├── BOOT
│   │   └── BOOTx64.efi
│   └── OC
│       ├── ACPI
│       │   ├── SSDT-AWAC.aml
│       │   ├── SSDT-EC-USBX-DESKTOP.aml
│       │   ├── SSDT-PLUG.aml
│       ├── Bootstrap
│       │   └── Bootstrap.efi
│       ├── Drivers
│       │   ├── HfsPlus.efi
│       │   └── OpenRuntime.efi
│       ├── Kexts
│       │   ├── AppleALC.kext
│       │   ├── Lilu.kext
│       │   ├── LucyRTL8125Ethernet.kext
│       │   ├── NVMeFix.kext
│       │   ├── SMCProcessor.kext
│       │   ├── SMCSuperIO.kext
│       │   ├── USBPorts.kext
│       │   ├── VirtualSMC.kext
│       │   ├── WhateverGreen.kext
│       │   ├── XHCI-unsupported.kext
│       │   └── dAGPM.kext
│       ├── OpenCore.efi
│       ├── Tools
│       │   └── OpenShell.efi
│       └── config.plist
└── README.md
```

逐个文件介绍一下：

| 文件                              | 用途                                                                                                                                                                                    |
| --------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| BOOT/BOOTx64.efi                  | OpenCore 底包自带，不管，不动它                                                                                                                                                         |
| OC/ACPI/SSDT-AWAC.aml             | ACPI 介绍见下方                                                                                                                                                                         |
| OC/ACPI/SSDT-EC-USBX-DESKTOP.aml  | ACPI 介绍见下方                                                                                                                                                                         |
| OC/ACPI/SSDT-PLUG.aml             | ACPI 介绍见下方                                                                                                                                                                         |
| OC/Bootstrap/Bootstrap.efi        | OpenCore 底包自带，不管，不动它                                                                                                                                                         |
| OC/Drivers/HfsPlus.efi            | 底包里其他驱动都可以删掉，这两个是必须的。                                                                                                                                              |
| OC/Drivers/OpenRuntime.efi        | 同上，必须的驱动                                                                                                                                                                        |
| OC/Kexts/AppleALC.kext            | 声卡相关驱动，基本都需要，不过后续需要一些配置                                                                                                                                          |
| OC/Kexts/Lilu.kext                | 基础驱动，必须的                                                                                                                                                                        |
| OC/Kexts/LucyRTL8125Ethernet.kext | 有线网卡驱动                                                                                                                                                                            |
| OC/Kexts/NVMeFix.kext             | 其他驱动，也基本都是必须的                                                                                                                                                              |
| OC/Kexts/SMCProcessor.kext        | VirtualSMC 的相关插件驱动，基本必须                                                                                                                                                     |
| OC/Kexts/SMCSuperIO.kext          | 同上                                                                                                                                                                                    |
| OC/Kexts/USBPorts.kext            | 自己生成的正确配置的 USB Map 文件（其实我是从[别的地方](https://github.com/szc188/MSI-B460M-MORTAR-10700K-5500XT-OC/tree/main/EFI/OC/Kexts)抄的，我按照文档教程和司波图教程，都没配置成功) |
| OC/Kexts/VirtualSMC.kext          | 基础驱动，必须                                                                                                                                                                          |
| OC/Kexts/WhateverGreen.kext       | 显卡相关必须驱动                                                                                                                                                                        |
| OC/Kexts/XHCI-unsupported.kext    | 配合解决 USB 问题的驱动                                                                                                                                                                 |
| OC/OpenCore.efi                   | 自带，不动它                                                                                                                                                                            |
| OC/Tools/OpenShell.efi            | Tools 里面其实都删差不多了                                                                                                                                                              |
| OC/config.plist                   | **重点编辑文件**                                                                                                                                                                  |

#### ACPI

ACPI 其实在教程里是在 kext 后面配置的，不过目录树排前面，就先介绍了。

按照 [OpenCore 教程](https://dortania.github.io/Getting-Started-With-ACPI/ssdt-platform.html#desktop)，Comet Lake 平台基本上只需要 `SSDT-PLUG`、`SSDT-EC-USBX`、`SSDT-AWAC` 这三个文件。

`SSDT-RHUB` 这个文件是解决部分主板的 USB 问题的。不是所有都必须的。但是，刚开始我也没加这个文件。装完之后，整个 USB 设备里只有 USB 3.0 Bus。但是蓝牙的线接在主板内部的 USB Header 上是 USB 2.0 的，所以蓝牙并不工作。后来加了这个文件之后，USB 2.0 确实工作了。蓝牙也就有了。不过这时候，USB 还是不完美的，USB 2.0 工作了，USB 3.0 就不工作了。这时候还需要后来单独自己配置 USBPorts.kext 来正确处理 USB Map 的问题。所以，对于这个文件，可以一半解决问题，并不完美。

#### kext

按照教程，看清楚，自己需要什么，不需要什么。

比如在 [Wi-Fi 这里](https://dortania.github.io/OpenCore-Install-Guide/ktext.html#wifi-and-bluetooth)，挺多网上的 efi 都会放的 `AirportBrcmFixup`、`BrcmPatchRAM`，其实我们是不需要的，我们是免驱的网卡。

### 第四步：配置 config.plist

从 0 开始的配置应该是复制 OpenCorePkg 底包里面的 Sample.plist 进行修改。如果是直接用我的 EFI 的话，最好还是按照教程都走一遍。比如里面配置 PlatformInfo 的地方，是需要每个人自己生成的，PlatformInfo 里面的 ROM 是 en0 的 MAC 地址，每个人的硬件不一样，也需要自己配置的。这个 ROM 是后面 Post-Install fix iService 阶段改的，安装的时候其实不改就能用。

这一步就按照[教程](https://dortania.github.io/OpenCore-Install-Guide/config.plist/comet-lake.html)一个一个改就行了。

用 ProperTree 打开 config.plist 配置文件，cmd + shift + R 选择 OC 的目录，就可以自动加载修改了的 ACPI、kext 文件的对应配置。

然后就逐个参数修改就好了。

`AAPL,ig-platform-id` 这里说一下，十代平台目前对于纯核显支持还有点问题，最好是核显 iGPU + 独显 dGPU 一起用，使用 `0300C89B`这个参数。在司波图的视频里也有说明。

`XhciPortLimit` 这个改为 True 是在还没有自定义 USB Map 的时候需要改的。自定义 USB Map 完成之后，是可以改回去的。

上面 config.plist 改完之后，保存就可以了，基本就可以用了。建议去 [Sanity Checker](https://opencore.slowgeek.com/) 检查一下配置正确性。

### 第五步：修改 BIOS

OpenCore 给了要开启和关闭的选项，但是不是每个主板都有全部选项的。

MSI B460M 这个改了大概这些：

* Fast Boot
* Secure Boot
* CFG lock
* Intel SGX
* VT-x
* Above 4G decoding
* Hyper-Threading
* EHCI/XHCI Hand-off
* SATA Mode: AHCI

其中有些默认值就是我们需要的，找到看一眼，也不用特意改他。

### 第六步：安装系统

> 如果使用有线网卡，没有无线网卡安装时，可以通过命令行工具设置网卡参数以启用(具体网卡名可通过命令行查看): `<br>` ifconfig en0 media 1000baseT mediaopt full-duplex

这一步没啥好说的。选择 U 盘引导启动，选择 macOS 安装程序。

全新硬盘安装的话，先去分区，然后安装。

安装过程看情况把，大概 20 来分钟的样子。然后就配置 iCloud、账户之类的，也没啥特别的。

OpenCore 也给出了一些安装前后可能遇到的问题，比如我就遇到了，最后发现还是自己应该配置的 ScanPolicy = 0 没配置导致的。正确的 config.plist 配置，安装就很顺畅了，没啥问题。

### 第七步：Post-Install

这就是系统安装后的小问题的修复过程。大部分问题其实我没遇到。就主要遇到一个 USB 的问题。

通常常做的 Fix Audio 过程，可以看文档，也可以看司波图的视频教程，都挺清晰的。我的板载声卡直接用 layout = 1 就可以，所以没改啥就可以用。

小 tips：系统安装好了之后，会让你把 U 盘里的 efi 复制到主机硬盘的 EFI 分区里。之后修改各种细节配置，还是在 USB 的 efi 里改，然后系统引导从 U 盘启动就可以使用新的配置。确定修改正确之后，再覆盖主机 SSD 的 EFI 分区的文件。

小系统升级没啥问题，装完之后，自动提示我升级 macOS 10.15.7 的补充更新，我就直接在线更新了，就没啥问题。

Fixing DRM 那一块我也搞了半天。最后发现，人家根本还不支持 Safari 14，所以整了半天没啥用。

USB Map 这个，有点麻烦，建议看看司波图的视频，说实话，我也没能实践成功。因为起手 USB 2.0 不可用，加了 SSDT-RHUB 之后，USB 3.0 就不可用，有点鬼畜。最后大概摸清楚原理，找了一份可用的 USBPorts.kext 文件。司波图的那种改 SSDT 的方式，我是没实践成功的。还是用了改 kext 的方式解决了问题。

### 总结

搞到这里就没啥了，各种东西都可用了。

+ [X] 睡眠 唤醒
+ [X] 所有USB端口、USB3.0
+ [X] 独显免驱
+ [X] 板载声卡
+ [X] 板载网卡（需要手动设置）
+ [X] Airdrop（更换BCM94352Z）

主要参考链接：

- https://github.com/maemual/MSI-B460M-10700-5500XT
- https://dortania.github.io/OpenCore-Install-Guide/
- https://github.com/abner-xu/efi
- https://github.com/szc188/MSI-B460M-MORTAR-10700K-5500XT-OC
- https://www.bilibili.com/video/BV1uf4y1X7MT
