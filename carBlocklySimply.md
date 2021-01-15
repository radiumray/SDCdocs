> # 图形块基础功精简版能说明


>> ## 车初始化
```python

import os
# 启用驱动
os.system("gnome-terminal -e 'bash ./LRSDK/DriveHAL/DriveHAL.sh'") 
# 车体模块依赖包引用
from LRSDK.application import Application
# 实例化车体模块
app = Application(fps=25, operate_mode="ManualDrive")
# 控制模块依赖包引用
from LRSDK.appController import AppController
# 实例化控制模块
control = AppController()

# 图形块样式
+--+     +-------------------+
|  +-----+                   |
|      车初始化               |
+--+     +-------------------+
   +-----+

```

>> ## 车处理

```python
def drive_fun():

    xxxxx
    xxxxx
    xxxxx

    return control
app.run(drive_fun)

# 图形块样式
+-----------------------------------------------+
|                                               |
|     车处理                                    |
|                                               |
|        +--------------------------------------+
|        |
|        |
|        |
|        |
|        +--------------------------------------+
|                                               |
|                                               |
|                                               |
+-----------------------------------------------+

```

>> ## 车控制
```python
# 控制 运动方法
control.angle = 0.5 #左拐
control.throttle  = 0.1 #向前速度为0.1

# 下拉框选项为：
# Angle 为小车转向角度 （0为方向居中）
# Throttle 为小车速度 (0为停车)
# 角度和速度的值为1~-1之间，可根据需求映射为角度0~180度，速度255~-255之间

# 图形块样式
+--+     +------------------------------+
|  +-----+                              |
|      控制: 方式: | angle |   值（0.5） |
|                  | throttle |         |
+--+     +------------------------------+
   +-----+

```



