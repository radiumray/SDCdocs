## qt下载：
http://download.qt.io/archive/qt/5.13/5.13.2/qt-opensource-linux-x64-5.13.2.run

## 安装：
./qt-opensource-linux-x64-5.13.2.run

## QtCreator设置野火iMx6开发板提供的qt交叉编译套件
https://www.cnblogs.com/GregTse/p/11892287.html

过程：
1.根据qt交叉编译工具里的md文件说明，安装qt交叉编译环境并测试是否安装成功
2.在QtCreator安装目录的bin文件夹内，修改qtcreator.sh，在第一行添加如下内容（qt交叉编译环境按默认路径安装）：

```bash

source /opt/fsl-imx-x11/4.1.15-2.1.0/environment-setup-cortexa7hf-neon-poky-linux-gnueabi 

注意：第二行才是

#! /bin/sh

```

3. 用命令行运行qtcreator.sh
4. 在QtCreator中，打开 工具->选项->Kits，设置Qt Versions、编译器和Debuggers
5. Qt Versions中添加安装的qt交叉编译环境，选择 qmake：设置名称(如：Qt %{Qt:Version} (imx))并应用
qmake路径：

/opt/fsl-imx-x11/4.1.15-2.1.0/sysroots/x86_64-pokysdk-linux/usr/bin/qt5/qmake
6. 编译器添加野火提到的gcc和g++，设置名称(如 gcc-imx 和 g++-imx)并应用

gcc路径：
/opt/fsl-imx-x11/4.1.15-2.1.0/sysroots/x86_64-pokysdk-linux/usr/bin/arm-poky-linux-gnueabi/arm-poky-linux-gnueabi-gcc

g++路径：
/opt/fsl-imx-x11/4.1.15-2.1.0/sysroots/x86_64-pokysdk-linux/usr/bin/arm-poky-linux-gnueabi/arm-poky-linux-gnueabi-g++
7. Debuggers添加gdb（可以不添加）设置名称(如gdb-imx)并应用

gdb路径：
/opt/fsl-imx-x11/4.1.15-2.1.0/sysroots/x86_64-pokysdk-linux/usr/bin/arm-poky-linux-gnueabi/arm-poky-linux-gnueabi-gdb

8. 构建套件(Kit)中添加套件


设置名称（如 imx6）
设备类型 通用Linux设备
设备 （可无，但没有设备该套件会发出警告）
Sysroot 无
编译器： C:选择 gcc-imx C++选择 g++-imx
调试器： 可选择系统默认，或添加的gdb-imx
Qt版本： 选择 Qt 5.6.2 (imx)
Qt mkspec: linux-oe-g++
(注意：必须添加以上mkspec)


9.点OK即可
在Qt新建工程，并选择该套件进行编译，检查配置是否成功
选择该套件时，在概要信息中出现警告：
Cannot read /opt/fsl-imx-x11/4.1.15-2.1.0/sysroots/cortexa7hf-neon-poky-linux-gnueabi/usr/lib/qt5/mkspecs/oe-device-extra.pri: No such file or directory
可忽略

对于以上警告，可修改 /opt/fsl-imx-x11/4.1.15-2.1.0/sysroots/cortexa7hf-neon-poky-linux-gnueabi/usr/lib/qt5/mkspecs/linux-oe-g++/qmake.conf文件

注释 第40行 include(../oe-device-extra.pri)

PS：每次要使用该套件，都要从命令行启动qtcreator.sh方可
