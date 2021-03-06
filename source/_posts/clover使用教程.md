---
title: clover使用教程
urlname: clover-user-manual
date: 2017-10-20 11:19:06
top: 99
tags:
- clover
- 手册
- 教程
categories:
- 教程
photos:
- "http://7.daliansky.net/Clover_Logo.png"
---

# Clover的前世今生
> 前言：*先将最最基本的操作发出来，然后再写完整的教程*

## 如何打开啰嗦模式进行排错
开机进入clover引导界面，
![1-main](http://7.daliansky.net/1-main.png)
在要引导的分区卷标上按 `空格` 即可进入
![space2](http://7.daliansky.net/space2.png)
勾选以下选项：
![space-selected](http://7.daliansky.net/space-selected.png)
选择 `Boot macOS with selected options` 启动
出现错误画面拍照发群里寻求帮助。

## Clover是什么
什么是Clover（三叶草）呢？显然它不是指的草地里用来喂牛的草啦。Clover是一个软件，是一个新型的启动器，它能够让普通的PC上用上Mac OS X系统。

苹果公司（Apple）限制Mac OS X系统只能在Apple设备上使用，并且苹果不保证Mac OS X在其它设备上能够正常工作。所以，用户需要承担一定的风险。当然，为了避免其它的法律纠纷，你不应该用作商业用途。装上了Mac OS X的非苹果电脑，就叫做黑苹果(Hackintosh)。

## 名字的来源
启动器的名字 `Clover` 由一位创建者kabyl命名。他发现了四叶草和Mac键盘上Commmand键的相似之处，由此起了Clover这个名字。
> 维基百科：*四叶草是三叶草的稀有变种。根据西方传统，发现者四叶草意味的是好运，尤其是偶然发现的，更是祥瑞之兆。另外，第一片叶子代表信仰，第二片叶子代表希望，第三片叶子代表爱情，第四片叶子代表运气。*

## Clover能干什么
Clover是一个操作系统启动加载器(boot loader)，能够同时运行于支持EFI方式启动的新式电脑和不支持它的老式电脑上。一些操作系统可以支持以EFI方式启动，比如OS X, Windows 7/8/10 64-bit, Linux；也有不支持的，比如Windows XP，它只能通过传统的BIOS方式来启动，也就是通过启动扇区来启动。

EFI不仅存在于操作系统的启动过程中，它还会创建操作系统可访问的表和服务(tables and services)，操作系统的运行依赖于EFI正确的提供功能。从内建的UEFI来启动OS X是不可能的，用原始的DUET来启动OS X也不可能。CloverEFI和CloverGUI做了大量的工作来修正内部表，让运行OS X成为可能。

> 译注：*DUET(Developer's UEFI Emulation)，开发者的UEFI模拟*

## Clover的两种启动方式
启动方式A: 基于BIOS的电脑（老式主板）
`BIOS->MBR->PBR->boot->CLOVERX64.efi->OSLoader`

启动方式B: 基于UEFI的电脑（新式主板）
`UEFI->CLOVERX64.efi->OSLoader`

## Clover兼EFI的目录结构
![EFI](http://7.daliansky.net/EFI.png)

## Clover驱动程序详解
BIOS启动过程中（启动方式A）要用到drivers32或drivers64目录，UEFI启动过程中（启动方式B）则使用 `drivers64UEFI` 目录。它们的内容会根据配置和BIOS版本而有所不同。
**必须要提的一点是这些驱动程序只在bootloader运行时有效，不会影响最终启动的操作系统。**
至于到底要使用哪些驱动程序由用户来决定。
### Drivers64UEFI目录几必备的驱动程序：
| 驱动程序                | 详解                                                         |
| ----------------------- | ------------------------------------------------------------ |
| ApfsDriverLoader-64.efi | 苹果新推出的`apfs`文件系统，macOS 10.13/10.14必备            |
| FSInject.efi            | 控制文件系统注入kext到系统的可能性。详细解释请参照[WithKexts](https://clover-wiki.zetam.org/Configuration#boot-args) |
| HFSPlus.efi             | HFS+文件系统驱动程序。这个驱动对于通过启动方式B来启动Mac OS X是必须的。启动方式A中用到的启动程序(CloverEFI)已经包含了这个驱动 |
| AptioMemoryFix-64.efi   | 修复AMI Aptio EFI内存映射。如果没有就不能启动OS X            |
| OsxFatBinaryDrv-64.efi  | 允许加载FAT模块比如boot.efi                                  |
| CsmVideoDxe.efi         | 比UEFI里提供更多分辨率的显卡驱动(可选)                       |

# Clover Bootloader详解
本节会详细介绍Clover各项功能的用法

## Clover主界面
使用Clover开机引导后，默认的系统界面如下：
![1-main](http://7.daliansky.net/1-main.png)

本例中:

* 蓝色图标为 `Windows 10` 的引导
* 绿色图标为 `Ubuntu Linux` 的引导
* 橙色图标为 `macOS High Sierra` 的引导
* 红色图标为 `macOS Sierra`的引导

当你想引导到不同的操作系统，只需要移动键盘上的左右键到各自的图标后回车即可。

## 帮助菜单 F1
按 `F1` 键会呼出 `Clover` 的帮助信息
![Help_F11](http://7.daliansky.net/Help_F11.png)

* ESC 退出子菜单，恢复到主菜单
* F1 帮助

* F2 保存 `preboot.log` 到 `EFI/CLOVER/misc/` 目录下，以便于您排错
* F3 显示 `被隐藏` 的入口
    * 比如你在 `config.plist` 中隐藏了 `Recovery HD` 
      ![HideVolume](http://7.daliansky.net/HideVolume.png)
    * 当你想进入恢复模式的时候，可以不需要修改 `config.plist` 而直接按 `F3` 显示出那些被你隐藏的引导项。如下图：
      ![f3](http://7.daliansky.net/f3.png)

* F4 提取 `DSDT` 保存到 `EFI/CLOVER/ACPI/origin/`
    * 此选项会经常用到。比如你的机器出现问题，需要别人帮助你解决问题，人家会跟你要 `DSDT` ，这个时候你只需要在 `Clover` 引导界面按下快捷键 `F4` 即可提取
* F5 提取修正过的 `DSDT` 保存到 `EFI/CLOVER/ACPI/origin/`
* F6 提取显卡ROM `VideoBios` 保存到 `EFI/CLOVER/misc/`
* F10 截屏，截取当前界面，保存到 `EFI/CLOVER/misc/`
* F11 重置NVRAM,r4299版本新增
* 空格 选定菜单项的详细信息
* 数字 1-9 菜单项的快捷键
* A 关于 `Clover`
  ![aboutclover](http://7.daliansky.net/aboutclover.png)


* O `Clover` 选项
    * 此选项是 `Clover` 的核心，所有的选项都在这个菜单里，当你无法引导进入 `macOS` 系统使用 `Clover Configurator` 进行选项调整时，可以通过该选项进行修改进入系统。**后面会详细介绍**
      ![options](http://7.daliansky.net/options.png)


* R 软复位
* U 退出

## 如何打开啰嗦模式进行排错【重复内容，目的是加深印象】
开机进入clover引导界面，
![1-main](http://7.daliansky.net/1-main.png)
在要引导的分区卷标上按 `空格` 即可进入
![space2](http://7.daliansky.net/space2.png)
勾选以下选项：
![space-selected](http://7.daliansky.net/space-selected.png)
选择 `Boot macOS with selected options` 启动
出现错误画面拍照发群里寻求帮助。

## Clover Options：Clover选项
> 文章上面已经提到了Clover的选项是它的核心，那么现在就让我们走进Clover选项设置

照例先上一张图：
![options](http://7.daliansky.net/options.png)

* Boot Args
    * 引导参数传递，比如前面教大家的使用 `-v` 打开啰嗦模式，就是通过它传递的；再比如你使用了不兼容版本的 `Lilu` 和 `AppleALC` 而导致无法进入系统时，可以在这上面手动添加上： `-liluoff` 或者 `-alcoff` 跳过相关的驱动而进入系统

* Configs
    * 配置文件选择。当你有不止一个 `config.plist` 配置文件时，可以通过该选项选择不同的配置文件进入系统
    * 操作过程
        * 光标移动到 `Configs`
           ![configs](http://7.daliansky.net/configs.png)

        * 回车后进入子菜单
          ![configselect](http://7.daliansky.net/configselect.png)

        * 通过光标上下移动选择其它的配置文件，回车后按 `ESC` 键退到主菜单 
* GUI tuning
    * Clover主题切换，当你有不止一套主题的时候，可以通过该选项切换主题
    * 操作过程
        * 光标移动到 `GUI tuning`
          ![gui](http://7.daliansky.net/gui.png)
        * 回车后进入子菜单
          ![guithemes](http://7.daliansky.net/guithemes.png)
        * 光标移动到 `Themes` ，回车后进入子菜单
          ![guithemeselect](http://7.daliansky.net/guithemeselect.png) 
        * 通过光标上下移动选择其它的主题，回车后按 `ESC` 键退到主菜单，Clover主界面已经刷新为选择的新主题 

* ACPI patching
    * 电源补丁：进不去电脑的，需要drop tables的，禁用/调用 `DSDT.aml` ，禁用 `hotpatch` ，无关关机的，无法重启的；总之跟电源相关的都在这里边。
    * 操作过程
      光标移动到 `ACPI patching`
      ![acpi](http://7.daliansky.net/acpi.png)

        回车后进入子菜单
        ![acpi-submenu](http://7.daliansky.net/acpi-submenu.png)

        * Debug DSDT
            * 打开DSDT调试模式
        * DSDT name
            * 默认文件名为：DSDT.aml
        * Tables dropping
            * 光标移动到 `Tables dropping` 回车进入
              ![acpi-tablesdrop](http://7.daliansky.net/acpi-tablesdrop.png)
            * 通过移动光标按空格勾选各选项，按 `ESC` 退出该子菜单
              ![acpi-table-dropping](http://7.daliansky.net/acpi-table-dropping.png)
            * 该表格等同于使用 `Clover Configurator` 打开 `config.plist` 后，在 `ACPI` 选项的左下角 `Drop Tables`
              ![Drop-Tables](http://7.daliansky.net/Drop-Tables.png)
        * Drop OEM _DSM
            * 丢弃_DSM
            * 光标移动到 `Drop OEM _DSM` 回车进入
              ![apci-drop-oem_dsm](http://7.daliansky.net/apci-drop-oem_dsm.png)
            * 通过移动光标按空格勾选各选项
              ![acpi-drop-oem_dsm-selected](http://7.daliansky.net/acpi-drop-oem_dsm-selected.png)
            * 按 `ESC` 退出该子菜单

        * DSDT fix mask
            * DSDT修复遮盖
            * 详细说明如下：

| 参数            | 描述                                       |
| ------------- | ---------------------------------------- |
| `AddDTGP`     | 修改 DSDT 添加方法所必须依赖的函数。必不可缺                |
| `FixDarwin`   |                                          |
| `Fixshutdown` | 关机修复，主要是添加 \_PTS 函数，判断寄存器 arg0 值是否为 5 ，华硕主板建议勾选. |
| `AddMCHC`     | 这个功能是在 dsdt 中添加一装置具体是 DveiceID=0X0044,匹配 Intel Clarkdale 平台。有些芯片需要这个装置来解决 PCI 的电源管理问题，一般不启用 |
| `FixHPET`     | 修复 HPET ，添加 IRQ(0,8,11) 加载原生电源管理，10.9 不需要 |
| `FakeLPC`     | 仿冒 LPC ，一般 Clover 会自动注入合适的芯片参数到 dsdt 中，来达到加载 AppleLPC.kext 的目的。对以 Intel and NForce 芯片，建议勾选。特别是芯片组比较老的如：ICH7,ICH9 |
| `FixIPIC`     | 从 decice IPIC 移除中断语句 (IRQ(2)),有助于电源按钮的工作，对于笔记本而言，更希望增加这个中断功能 |
| `FixSBUS`     | 增加 SMBusControlle 到设备树种，可修复因缺失 SBUS 控制而在系统 log 中出现的警告，建议勾选 |
| `Fixdisplay`  | 增加 GFX0,和 HDMI 音频设置 HADU. 如果设置了 FAKEID 也会增加到这边，建议勾选 |
| `FixIDE`      | 修复在 10.6 事五国出现的 AppleIntelPIIXATA 错误。 一般不启用 |
| `FixSATA`     | 内建磁盘，用 ICH6 的 ID 匹配，解决橙色磁盘问题，一般启用        |
| `FixFIREWIRE` | 在火线控制装置中增加 fwhub 属性。一般不启用                |
| `FixUSB`      | 注入 USB 属性，帮助内建 USB                       |
| `FixLAN`      | 注入网卡属性，帮助网卡内建。建议启用                       |
| `FixAirport`  | 为支持 Airport 的无线网卡注入属性，以开启 Airport 功能，无此设备的不启用 |
| `FixHDA`      | 修正 AZAL to HDEF or HDAU, 增加 layout -id 和 pinconfig,MaximumBootBeepVolume 属性 |
| `FixDarwin7`  | 这项补丁只有Darwin OS系统［苹果系统］识别                |
| `FixRTC`      | 从RTC装置中删除IRQ（0），作用是否与patch里的防RTC重置类似？    |
| `FixTMR`      | 从TMR装置中删除IRQ(8)，适用于较早的dos设备，现代新的计算机不需要补丁，这个问题只是以前没发现［作者］ |
| `AddIMEI`     | 这个设置用于intelHDxxx集成显卡，解决完美注入。这项也需要开启fakeid -> IMEI |
| `FixIntelGfx` | 开启对IntelGFX显卡的新补丁设置，不开启则补丁不会生效［配合imei］   |
| `FixWAK`      | 这个补丁主要是消除警告，如在method_WAK下缺少返回语句则加入Return(Package(0))，我不知道会有什么 |
| `DeleteUnuse` | 从DSDT中删除没有使用的设备如软盘驱动器，打印机端口和其他没用的设备      |
| `FixADP1`     | 将“ AC0 ”设备 重命名为“ ADP1”设备.                |
| `AddPNLF`     | 添加一个非常实用的PNLF 设置代码：当然只有你可以调节亮度控制时才有用。这个补丁也会对系统良好的睡眠/唤醒 |
| `FixS3D`      | 修正了 \_S3Dmethods函数，也解决了一些睡眠/唤醒的问题        |
| `FixACST`     | ACST项含义对于苹果和华硕意义不同，对于华硕是AC适配器状态，而苹果是一个替代\_cst，c-states table［CPU 闲置休眠状态的功能］。如果要没有冲突就要将其重名为其他名称的东西 |
| `AddHDMI`     | 修复HDMI音频输出问题（无需修改AppleHDA）               |
| `FixRegions`  | 因为BIOS当中的一些内容改变了。这个浮动的区域导致无法使用自定义DSDT（custom DSDT），因为这个区域可移动且不符合当前的状态。这个补丁的目的是找到BIOS中所有这样的区域并在自定义DSDT中加以修正。所以现在你可以生成有错误区域的自定义DSDT然后使用这个补丁 |
| `FixHeaders`  | MACH reboot修复|


* 光标移动到 `DSDT fix mask` 回车进入
  ![acpi-DSDT-fix-mask](http://7.daliansky.net/acpi-DSDT-fix-mask.png)
* 通过移动光标按空格勾选各选项
  ![acpi-DSDT-fix-mask-selected](http://7.daliansky.net/acpi-DSDT-fix-mask-selected.png)
  ![acpi-DSDT-fix-mask-selected2](http://7.daliansky.net/acpi-DSDT-fix-mask-selected2.png)
    * 按 `ESC` 退出该子菜单
* Custom DSDT patches
    * 自定义的DSDT补丁
    * 光标移动到 `Custom DSDT patches` 回车进入
    ![acpi-Custom-DSDT-patches](http://7.daliansky.net/acpi-Custom-DSDT-patches.png)
    * 通过移动光标按空格勾选各选项
    ![acpi-Custom-DSDT-patches-selected](http://7.daliansky.net/acpi-Custom-DSDT-patches-selected.png)
    * 按 `ESC` 退出该子菜单

## Block injected kexts 管理你的驱动程序

通过Clover加载的驱动程序位于 `EFI/CLOVER/kexts/Other` ，也有可能位于 `EFI/CLOVER/kexts/10.13` 或者 `EFI/CLOVER/kexts/10.12` 目录中。它至少包括以下驱动程序：

| 驱动程序                     | 详细信息                                                     | 备注     |
| ---------------------------- | ------------------------------------------------------------ | -------- |
| FakeSMC.kext                 | 安装hackintosh的核心程序，没有它就没法在你的电脑上面运行macOS | 必备     |
| Lilu.kext                    | 内核扩展程序，离开它，下面的几个程序都无法正常运行           | 必备     |
| WhateverGreen.kext           | 显卡综合修复，整合了核显、AMD、NVIDIA的综合修复，包括 （单卡启动黑屏，唤醒黑屏 等等）(依赖于Lilu) | 必备     |
| AppleALC.kext                | 动态对系统注入必要的文件/打补丁以驱动声卡(依赖于Lilu)        | 可选     |
| ~~IntelGraphicsFixup.kext~~  | ~~修补 Intel 核显综合问题 (开机花屏，Haswell/Skylake 因 PAVP 导致的死机等等)(依赖于Lilu)~~ | ~~可选~~ |
| IntelGraphicsDVMTFixup.kext  | 修正 Broadwell/Skylake 平台核显因 DVMT 不足而导致的死机(依赖于Lilu) | 可选     |
| ~~NvidiaGraphicsFixup.kext~~ | ~~修正 N 卡 (可能也适用于 I 卡) 使用某些 SMBios 如 MacPro6,1 等引发黑屏的问题(依赖于Lilu)~~ | ~~可选~~ |
| AirportBrcmFixup             | 修补 Broadcom Wi-Fi 综合问题                                 | 可选     |
| FakePCIID.kext               | 仿冒PCI设备核心驱动，部分驱动依赖于它                        | 可选     |
| ACPIBatteryManager.kext      | 笔记本电池管理驱动                                           | 可选     |
| RealtekRTL8xxx.kext          | Realtek 8xxx网卡驱动程序                                     | 可选     |
| VoodooPS2Controller.kext     | Voodoo键盘/鼠标驱动程序                                      | 可选     |

当你面对这么一堆驱动的时候，如何让它们有条不紊地正常工作呢？万一出现问题的时候又如何让这些驱动在Clover引导的时候禁用它们呢？这个时候 `Block injected kexts` 就派上用场了。新版的 `Clover Bootloader` 已经将 `Block injected kexts` 放到了 `macOS` 系统分区的图标下面了。

* 操作方法：
    * 开机进入clover引导界面，要引导的分区卷标上按 `空格` 即可进入
      ![1-main](http://7.daliansky.net/1-main.png)
    * 光标移动到 `Block injected kexts` 回车进入
      ![kim](http://7.daliansky.net/Blockinjectedkexts.png)
    * 选择当前系统加载的驱动程序目录
      ![kimselect](http://7.daliansky.net/BIKSelect.png)

    * 勾选禁用的驱动程序
      ![kimoptions](http://7.daliansky.net/BIKChoose.png)
    * 按 `ESC` 退出该子菜单

# 后记
Clover Bootloader的使用暂时先写到这里，还有更多的用法等着我们去发掘。您有什么想法或者需要补充的，请点击下面的QQ群与我联系更新。

# 特别感谢
群友 `(￣(工)￣)_小哥哥` 帮忙整理部分资料

# 关于打赏
整整写了两天的博文，如果不希望看到博主停更的话，请点击下方的 `打赏` 支持一下，有钱的捧个钱场，没钱的捧个人场，谢谢大家！


# QQ群:
331686786 [一起吃苹果](http://shang.qq.com/wpa/qunwpa?idkey=db511a29e856f37cbb871108ffa77a6e79dde47e491b8f2c8d8fe4d3c310de91)[群已满,请加下面群]
688324116[一起黑苹果](https://shang.qq.com/wpa/qunwpa?idkey=6bf69a6f4b983dce94ab42e439f02195dfd19a1601522c10ad41f4df97e0da82)

