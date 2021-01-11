# 图形块基础功能说明
(ascii在线生成器:http://asciiflow.com/)

## 启用驱动
```python

import os

os.system("gnome-terminal -e 'bash ./LRSDK/DriveHAL/DriveHAL.sh'") 


# 图形块样式
+--+     +-------------------+
|  +-----+                   |
|      启动驱动               |
+--+      +------------------+
   +------+


```

## 车体模块
### 车体实例化
```python

# 车体模块依赖包引用
from LRSDK.application import Application

# 实例化车体模块
app = Application(fps=25, operate_mode="ManualDrive")
# fps为整数输入参数
# operate_mode为下拉框 默认:ManualDrive

# 图形块样式
+--+     +-----------------------------------------------+
|  +-----+                                               |
|      车体:fps ( 25 )   operate_mode: | ManualDrive |    |
|                                       | AutoDrive |    |
|                                       | AutoDrive_ui | |
+--+      +----------------------------------------------+
   +------+
```
### 车体方法
```python
# 车体模块运行方法
app.run(自定义方法名：例如procedure)

# 图形块样式
+--+     +---------------------+
|  +-----+                     |
|      车体.运行 (procedure)    |
+--+      +--------------------+
   +------+

```

## 控制模块
### 控制实例化
```python

# 控制模块依赖包引用
from LRSDK.appController import AppController

# 实例化控制模块
control = AppController()

# 图形块样式
+--+     +---------------------+
|  +-----+                     |
|      控制模块                 |
+--+      +--------------------+
   +------+
```
### 控制方法
```python
# 控制 运动方法
control.angle = 0.5 #左拐
control.throttle  = 0.1 #向前速度为0.1

# 下拉框选项为：
# Angle 为小车转向角度
# Throttle 为小车速度
# 角度和速度的值为1~-1之间，可根据需求映射为角度0~180度，速度255~-255之间


# 图形块样式
+--+     +------------------------------+
|  +-----+                              |
|      控制: 方式: | angle |   值（0.5） |
|                  | throttle |         |
+--+      +-----------------------------+
   +------+

```
### 控制返回
```python
# 返回 控制实例
return control

# 图形块样式
    +-------------------------+
    |                         |
+---+                         |
|          控制                |
+---+                         |
    |                         |
    +-------------------------+


```


