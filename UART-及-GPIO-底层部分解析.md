本文综述 RPS 中 UART 和 GPIO 部分的底层架构，关于标准输出的顶层封装，请参见 [标准输出部分解析](https://github.com/Yradex/RaspberryPi3_OS/wiki/%E6%A0%87%E5%87%86%E8%BE%93%E5%87%BA%E9%83%A8%E5%88%86%E8%A7%A3%E6%9E%90)。

RPS 的 UART 和 GPIO 部分分别声明在 [driver/include/uart.h](https://github.com/Yradex/RaspberryPi3_OS/blob/master/os/driver/include/uart.h) 和 [driver/include/gpio.h](https://github.com/Yradex/RaspberryPi3_OS/blob/master/os/driver/include/gpio.h) 中。

如需查找 UART 及 GPIO 端口的在树莓派 3 上的具体位置，请参见 [树莓派 3 UART 及 GPIO 针脚定义](https://github.com/Yradex/RaspberryPi3_OS/wiki/%E6%A0%91%E8%8E%93%E6%B4%BE-3-UART-%E5%8F%8A-GPIO-%E9%92%88%E8%84%9A%E5%AE%9A%E4%B9%89)。

## 函数列表
| 返回类型 | 函数 | 描述 |
|---|---|---|
| void | [gpio_output_init](UART-及-GPIO-底层部分解析#void-gpio_output_init-int-n) (int n) | 初始化 GPIO 端口 n 为 OUTPUT |
| void | [gpio_set](UART-及-GPIO-底层部分解析#void-gpio_set-int-n) (int n) | 设置已初始化的 OUTPUT GPIO 端口 n |
| void | [gpio_clr](UART-及-GPIO-底层部分解析#void-gpio_clr-int-n) (int n) | 清除已初始化的 OUTPUT GPIO 端口 n |
| void | [uart_init](UART-及-GPIO-底层部分解析#void-uart_init-void) (void) | 初始化 UART 接口 |
| unsigned int | [uart_recv](UART-及-GPIO-底层部分解析#unsigned-int-uart_recv-void) (void) | 获取 UART 接收寄存器内容 |
| void | [uart_send](UART-及-GPIO-底层部分解析#void-uart_send-unsigned-int-c) (unsigned int c) | 通过 UART 发送内容 c |

## 函数信息
#### void gpio_output_init (int n)
###### 简述
gpio_output_init 用于将指定的 GPIO 端口设置为 OUTPUT。其核心部分是调用汇编函数 GET32 和 PUT32 将 GPFSELn 寄存器中的对应位设置为 0x000，即设置其功能为 OUTPUT。

###### 参数
* `int n`

  需要设置为 OUTPUT 的寄存器。n 的合法范围为 2 - 27，超出此范围的 n 值均会导致 panic。

###### 返回值
无

###### 额外信息
树莓派中的 GPIO 端口可以被设置为 INPUT、OUTPUT 和 Alternate function 1 - 5 中的任意一种，各 Alternate function 的功能定义请参见 *BCM2835 ARM Peripherals* 中的 6.2 节。

#### void gpio_set (int n)
#### void gpio_clr (int n)
###### 简述
gpio_set 和 gpio_clr 用于设置和复位指定的 OUTPUT GPIO 端口。默认状态下，当某个 GPIO 端口被设置为 OUTPUT 时，其电位为高电平或低电平的固定的一种，当执行 gpio_set 函数后，对应 GPIO 端口的电位改变为与默认相反；同理，当执行 gpio_clr 后，对应 GPIO 端口的电位会恢复为与默认相同。

###### 参数
* `int n`

  需要被设置或复位的 OUTPUT GPIO 端口。n 的合法范围为 2 - 27，超出此范围的 n 值均会导致 panic。

###### 返回值
无

###### 额外信息
博通的官方文档指出，gpio_set 或 gpio_clr 行为需要 150 条指令的延时方能生效，故我们在修改相关寄存器后调用了 sleep 函数来保证至少 150 条指令的延时，有关 sleep 函数的内容请参见 [标准系统函数部分解析](https://github.com/Yradex/RaspberryPi3_OS/wiki/%E6%A0%87%E5%87%86%E7%B3%BB%E7%BB%9F%E5%87%BD%E6%95%B0%E9%83%A8%E5%88%86%E8%A7%A3%E6%9E%90)。

gpio_set 或 gpio_clr 的操作对象必须是已经使用 gpio_output_init 初始化过的 GPIO 端口，否则其行为是未定义的。

#### void uart_init (void)
###### 简述
uart_init 用于初始化 UART 所需的 GPIO 端口并设置波特率等参数。

###### 参数
无

###### 返回值
无

###### 额外信息
UART 部分使用了 GPIO14、GPIO15 的 Alternate function 5，即 TXD1、RXD1。有关 UART 寄存器地址布局及其含义，请参见 *BCM2835 ARM Peripherals* 中的 13.4 节。

#### unsigned int uart_recv (void)
###### 简述
uart_recv 用于从 UART 接收寄存器中获取数据。轮询 LSR 寄存器中的 Data ready 位，直到其值为 1 时，返回 AUX_MU_IO_REG 寄存器的值。

###### 参数
无

###### 返回值
UART 接收寄存器的值。

###### 额外信息
uart_recv 函数目前只用于 DEBUG 用途。

#### void uart_send (unsigned int c)
###### 简述
uart_send 用于通过 UART 发送数据。轮询 LSR 寄存器中的 Transmitter empty 位，直到其值为 1 时，将需要发送的数据写入 AUX_MU_IO_REG 寄存器。

###### 参数
* `unsigned int c`

  需要发送的 unsigned int 值。

###### 返回值
无

###### 额外信息
无