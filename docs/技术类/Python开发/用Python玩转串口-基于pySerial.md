---
title: 用Python玩转串口(基于pySerial)
tags: [Python, 串口]
---

# 用 Python 玩转串口（基于 pySerial）



## 引言

对于嵌入式设备，串口可谓是最常用的接口。在裸机编程中，串口通常用于输出程序的运行或调试信息；在嵌入式操作系统中，串口通常会作为系统的控制台接口。如果掌握了 Python 操作串口的方法，那我们就可以利用 Python 强大的数据处理能力，快速开发出许多好用的工具。



## 串口的基本操作
在使用 Python 之前，我们先回想一下平时我们是如何使用串口的。总结来说，无非就是下面几个步骤：
首先，我们需要确定要使用的串口号。
其次，配置波特率、数据位、奇偶校验位、停止位、DTR/DSR、RTS/CTS 和 XON/XOFF。
第三，打开串口。
第四，收发数据。
第五，关闭串口。
接下来，我们就来研究下用 Python 怎么实现上面的这些步骤。



## 初识 pySerial
pySerial 是 Python 中用于操作串口的第三方模块，它支持 Windows、Linux、OSX、BSD等多个平台。如果要使用 pySerial 模块，首先必须保证 Python 版本高于 Python 2.7 或者 Python 3.4。另外，如果你是用的是 Windows 系统，那必须使用 Win7 及以上的版本。
pySerial 的安装很简单，只需要执行一条命令：`pip install pyserial`
安装完成后，只需要在 Python 代码中使用`import serial`语句导入该模块即可。



## 确定串口号

```python
import serial
import serial.tools.list_ports
 
# 获取所有串口设备实例。
# 如果没找到串口设备，则输出：“无串口设备。”
# 如果找到串口设备，则依次输出每个设备对应的串口号和描述信息。
ports_list = list(serial.tools.list_ports.comports())
if len(ports_list) <= 0:
    print("无串口设备。")
else:
    print("可用的串口设备如下：")
    for comport in ports_list:
        print(list(comport)[0], list(comport)[1])
```

运行结果：

```bash
可用的串口设备如下：
COM4 蓝牙链接上的标准串行 (COM4)
COM6 蓝牙链接上的标准串行 (COM6)
COM5 蓝牙链接上的标准串行 (COM5)
COM18 Prolific PL2303GT USB Serial COM Port (COM18)
COM17 Prolific USB-to-Serial Comm Port (COM17)
COM3 蓝牙链接上的标准串行 (COM3)
```



## 配置串口 & 打开串口

pySerial 配置和打开串口有两种方式，第一种方式是在调用函数接口打开串口时传入配置参数，第二种方式是先配置参数，然后再打开串口。这两种方式操作的效果一样，此处我们只介绍第一种。

```python
# 方式1：调用函数接口打开串口时传入配置参数
import serial
 
ser = serial.Serial("COM17", 115200)    # 打开COM17，将波特率配置为115200，其余参数使用默认值
if ser.isOpen():                        # 判断串口是否成功打开
    print("打开串口成功。")
    print(ser.name)    # 输出串口号
else:
    print("打开串口失败。")
 
```

在使用 serial.Serial() 创建串口实例时，可以传入的参数很多，常用的参数如下（默认值用粗体标记）：

- port - 串口设备名或 **None**。
- baudrate - 波特率，可以是50, 75, 110, 134, 150, 200, 300, 600, 1200, 1800, 2400, 4800, **9600**, 19200, 38400, 57600, 115200, 230400, 460800, 500000, 576000, 921600, 1000000, 1152000, 1500000, 2000000, 2500000, 3000000, 3500000, 4000000。
- bytesize - 数据位，可取值为：FIVEBITS, SIXBITS, SEVENBITS, **EIGHTBITS**。
- parity - 校验位，可取值为：**PARITY_NONE**, PARITY_EVEN, PARITY_ODD, PARITY_MARK, PARITY_SPACE。
- stopbits - 停止位，可取值为：**STOPBITS_ONE**, STOPBITS_ONE_POINT_FIVE, STOPBITS_TOW。
- xonxoff - 软件流控，可取值为 True, **False**。
- rtscts - 硬件（RTS/CTS）流控，可取值为 True, **False**。
- dsr/dtr - 硬件（DSR/DTR）流控，可取值为 True, **False**。
- timeout - 读超时时间，可取值为 **None**, 0 或者其他具体数值（支持小数）。当设置为 None 时，表示阻塞式读取，一直读到期望的所有数据才返回；当设置为 0 时，表示非阻塞式读取，无论读取到多少数据都立即返回；当设置为其他数值时，表示设置具体的超时时间（以秒为单位），如果在该时间内没有读取到所有数据，则直接返回。
- write_timeout: 写超时时间，可取值为 **None**, 0 或者其他具体数值（支持小数）。参数值起到的效果参考 timeout 参数。

```python
import serial
 
# 打开 COM17，将波特率配置为115200，数据位为7，停止位为2，无校验位，读超时时间为0.5秒。
ser = serial.Serial(port="COM17",
                    baudrate=115200,
                    bytesize=serial.SEVENBITS,
                    parity=serial.PARITY_NONE,
                    stopbits=serial.STOPBITS_TWO,
                    timeout=0.5) 
```



## 关闭串口

关闭串口很简单，直接调用 close() 方法即可。

```python
import serial
 
ser = serial.Serial("COM17", 115200)    # 打开 COM17，将波特率配置为115200，其余参数使用默认值
if ser.isOpen():                        # 判断串口是否成功打开
    print("打开串口成功。")
else:
    print("打开串口失败。")
 
ser.close()
if ser.isOpen():                        # 判断串口是否关闭
    print("串口未关闭。")
else:
    print("串口已关闭。")
```



## 发送数据 write()

关于write() 方法，需要了解如下几点：
① write() 方法只能发送 bytes 类型的数据，所以需要对字符串进行 encode 编码。
② write() 方法执行完成后，会将发送的字节数作为返回值。
③ 在打开串口时，可以为 write() 方法配置超时时间

```python
import serial
 
# 打开 COM17，将波特率配置为115200.
ser = serial.Serial(port="COM17", baudrate=115200)
 
# 串口发送 ABCDEFG，并输出发送的字节数。
write_len = ser.write("ABCDEFG".encode('utf-8'))
print("串口发出{}个字节。".format(write_len))
 
ser.close()
```



## 读取数据 read()

关于 read() 方法，需要了解如下几点：
① read() 方法默认一次读取一个字节，可以通过传入参数指定每次读取的字节数。
② read() 方法会将读取的内容作为返回值，类型为 bytes。
③ 在打开串口时，可以为 read() 方法配置超时时间。

```python
import serial
 
# 打开 COM17，将波特率配置为115200, 读超时时间为1秒
ser = serial.Serial(port="COM17", baudrate=115200, timeout=1)
 
# 读取串口输入信息并输出。
while True:
    com_input = ser.read(10)
    if com_input:   # 如果读取结果非空，则输出
        print(com_input)
 
ser.close()
```



## 总结

以上就是对 pySerial 模块使用方法的简单总结，如果想要了解更多 pySerial 细节，可以参考 [pySerial官方文档](https://pyserial.readthedocs.io/en/latest/pyserial.html)。