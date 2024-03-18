---
title: STLink使用RTT输出日志
date: 2024/02/29
tags:
 - 日志 
categories:
 - other
---

## 前提
通过RTT输出日志，可以不占额外的引脚和外设，速度非常快，几乎不影响程序的实时性。硬件需要搭配使用**ST-LINK**，代码需添加**SEGGER RTT**，软件采用的是**PyOCD**。如果你采用的调试器是**J-link**，请参考这篇博客:[基于J-Link RTT Viewer输出日志](https://blog.csdn.net/qq_36973838/article/details/131541025)

## 安装软件

安装pyOCD，所有安装过程中，必要时请搭配科学上网。

```shell
pip install pyocd
```

查看设备

```shell
pyocd list
```

可以检测到有一个**ST-Link**的设备。

```shell
(base) zzg@192 ~ % pyocd list
  #   Probe/Board    Unique ID                  Target  
--------------------------------------------------------
  0   STM32 STLink   53001300120000304B57534E   n/a     
(base) zzg@192 ~ % 
```

查看pyocd所支持的芯片：

```shell
pyocd list --targets
```

如果芯片的**name**所对应的source是**builtin**状态，那么不需要安装pack。

```shell
(base) zzg@192 ~ % pyocd list --targets

  Name                      Vendor                   Part Number                  Families                    Source   
-----------------------------------------------------------------------------------------------------------------------
  air001                    AirM2M                   Air001                                                   builtin  
  air32f103xb               AirM2M                   Air32F103xB                                              builtin  
  air32f103xc               AirM2M                   Air32F103xC                                              builtin  
  air32f103xe               AirM2M                   Air32F103xE                                              builtin  
  air32f103xg               AirM2M                   Air32F103xG                                              builtin  
  air32f103xp               AirM2M                   Air32F103xP                                              builtin  
  cc3220sf                  Texas Instruments        CC3220SF                                                 builtin  
  cortex_m                  Generic                  CoreSightTarget                                          builtin  
  cy8c64_sysap              Cypress                  cy8c64_sysap                                             builtin  
  cy8c64x5_cm0              Cypress                  cy8c64x5_cm0                                             builtin  
  cy8c64x5_cm0_full_flash   Cypress                  cy8c64x5_cm0_full_flash                                  builtin  
  cy8c64x5_cm4              Cypress                  cy8c64x5_cm4                                             builtin  
  cy8c64x5_cm4_full_flash   Cypress                  cy8c64x5_cm4_full_flash                                  builtin  
  cy8c64xa_cm0              Cypress                  cy8c64xA_cm0                                             builtin  
  cy8c64xa_cm0_full_flash   Cypress                  cy8c64xA_cm0_full_flash                                  builtin 
 -----------------------------------------------------------------------------------------------------------------------
  stm32f103c4               STMicroelectronics       STM32F103C4                  STM32F1 Series, STM32F103   pack     
  stm32f103c6               STMicroelectronics       STM32F103C6                  STM32F1 Series, STM32F103   pack     
  stm32f103c8               STMicroelectronics       STM32F103C8                  STM32F1 Series, STM32F103   pack     
  stm32f103cb               STMicroelectronics       STM32F103CB                  STM32F1 Series, STM32F103   pack     
  stm32f103r4               STMicroelectronics       STM32F103R4                  STM32F1 Series, STM32F103   pack    
```

我这里使用的芯片是STM32F103C8T6，需要安装pack。

```shell
pyocd pack install STM32F103C8
```
## 配置代码

需要安装J-Link驱动，[前往官网下载](https://www.segger.com/downloads/jlink/)，根据系统选择对应的版本安装即可

![pFRwUAK.png](https://s21.ax1x.com/2024/03/18/pFRwUAK.png)

安装完成后，在以下目录，找到移植的文件：***SEGGER_RTT_V796a.tgz***，不同系统的文件格式可能不一样。

```shell
/安装目录/SEGGER/JLink_V796a/Samples/RTT/SEGGER_RTT_V796a.tgz
```

将文件解压，将```Config\SEGGER_RTT_Conf.h```和```RTT\SEGGER_RTT.h```放入工程的```Inc```文件夹中，将```RTT\SEGGER_RTT_printf.c```和```RTT\SEGGER_RTT.c```放入到工程的```Src```文件夹中。如果你使用的**keil**，需要将.c文件添加到grups，将.h文件添加到头文件路径。如果你使用的是**makefile**，需要Makefile中添加.c文件和.h路径。

### 使用pyocd监控log

### 接口

```c
#include "SEGGER_RTT.h"
int SEGGER_RTT_printf(unsigned BufferIndex, const char * sFormat, ...);
```

### Demo

```c
#include "SEGGER_RTT.h"

int main()
{
	while (1)
	{
		SEGGER_RTT_printf(0, "Hello world!\r\n");
	}
    return 0;
}
```

### 连接设备

```shell
pyocd rtt -t STM32F103C8
```

连接成功，并打印日志：

```shell
s(base) zzg@192 ~ % pyocd rtt -t STM32F103C8
0001572 I Target type is stm32f103c8 [board]
0001617 I DP IDR = 0x1ba01477 (v1 rev1) [dap]
0001617 I debugvar 'DbgMCU_CR' = 0x7 (7) [pack_target]
0001625 I AHB-AP#0 IDR = 0x14770011 (AHB-AP var1 rev1) [discovery]
0001628 I AHB-AP#0 Class 0x1 ROM table #0 @ 0xe00ff000 (designer=020:ST part=410) [rom_table]
0001630 I [0]<e000e000:SCS v7-M class=14 designer=43b:Arm part=000> [rom_table]
0001631 I [1]<e0001000:DWT v7-M class=14 designer=43b:Arm part=002> [rom_table]
0001632 I [2]<e0002000:FPB v7-M class=14 designer=43b:Arm part=003> [rom_table]
0001633 I [3]<e0000000:ITM v7-M class=14 designer=43b:Arm part=001> [rom_table]
0001636 I [4]<e0040000:TPIU M3 class=9 designer=43b:Arm part=923 devtype=11 archid=0000 devid=ca0:0:0> [rom_table]
0001640 I CPU core #0: Cortex-M3 r1p1, v7.0-M architecture [cortex_m]
0001640 I Setting core #0 (Cortex-M3) default reset sequence to ResetSystem [pack_target]
0001642 I 4 hardware watchpoints [dwt]
0001645 I 6 hardware breakpoints, 4 literal comparators [fpb]
0001734 I 3 up channels and 3 down channels found [rtt_cmd]
0001734 I Reading from up channel 0 ("Terminal") [rtt_cmd]
0001736 I Writing to down channel 0 ("Terminal") [rtt_cmd]
Hello world!
Hello world!
```
