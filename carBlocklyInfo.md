# 图形块基础功能说明

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
```python

from LRSDK.application import Application

app = Application(fps=25, operate_mode="ManualDrive")


# 图形块样式
+--+     +-----------------------------------------------+
|  +-----+                                               |
|      车体:fps ( 26 )   operate_mode: | ManualDrive |    |
|                                       | AutoDrive |    |
|                                       | AutoDrive_ui | |
+--+      +----------------------------------------------+
   +------+


```
