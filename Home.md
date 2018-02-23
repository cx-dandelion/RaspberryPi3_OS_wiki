## 关于此项目

RaspberryPi3_OS 是一个将 MIPS 模拟器中的修改版 JOS 操作系统移植到 ARM AArch64 指令集树莓派 3B 型设备上的教学实践项目。

## 开发环境

* Ubuntu Desktop 14.04 x86
* GNU based build toolchain
* Raspberry Pi 3B (with ARMv8 BCM2837 SoC)

## 文档目录

* 准备工作
  * [部署交叉编译环境](部署交叉编译环境)
  * [树莓派 3 UART 及 GPIO 针脚定义](树莓派-3-UART-及-GPIO-针脚定义)
* 内核、Boot 和 printf
  * [树莓派 3 启动过程概述](树莓派-3-启动过程概述)
  * [启动部分汇编代码解析](启动部分汇编代码解析)
  * [标准系统函数部分解析](标准系统函数部分解析)
  * [UART 及 GPIO 底层部分解析](UART-及-GPIO-底层部分解析)
  * [标准输出部分解析](标准输出部分解析)
* 内存管理
  * [内存管理模型概述](内存管理模型概述)
  * [系统页表初始化函数概述](系统页表初始化函数概述)
  * [内存管理函数概述](内存管理函数概述)
* 进程与中断
  * [进程管理模型概述](进程管理模型概述)
  * [进程管理函数概述](进程管理函数概述)
  * [时钟中断部分解析](时钟中断部分解析)
  * [异常处理程序概述](异常处理程序概述)
* 系统调用与 fork
  * [系统调用实现概述](系统调用实现概述)
  * [fork 部分原理概述](fork 部分原理概述)
  * [ipc 部分原理概述](ipc 部分原理概述)
* 文件系统
* 管道与 Shell