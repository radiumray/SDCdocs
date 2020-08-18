```bash

+ 查看串口协议
+ 用一台纯净电脑从头搭建，写搭建步骤
+ 研究驱动
+ 测试车道线模型帧率


```


## 查看串口就接收数据
```bash

socat file:/dev/ttyUSB0,b115200,inlcr=1  stdout

```

## 安装cutecom 使用
```bash
1.安装cutecom
$sudo apt-get install cutecom

2.运行cutecom
$ sudo cutecom

插入串口后，使用以下命令查看串口驱动是否已经安装：

$ dmesg | grep usb
```

## Putty

```bash

安装：sudo apt-get install putty
打开方式都一样：sudo putty

```
