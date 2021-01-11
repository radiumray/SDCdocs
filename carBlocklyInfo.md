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

# 车体模块依赖包引用
from LRSDK.application import Application

# 实例化车体模块
app = Application(fps=25, operate_mode="ManualDrive")
# fps为整数输入参数
# operate_mode为下拉框

# 图形块样式
+--+     +-----------------------------------------------+
|  +-----+                                               |
|      车体:fps ( 26 )   operate_mode: | ManualDrive |    |
|                                       | AutoDrive |    |
|                                       | AutoDrive_ui | |
+--+      +----------------------------------------------+
   +------+

# 车体模块运行方法
app.run(自定义方法名：例如procedure)

# 图形块样式
+--+     +---------------------+
|  +-----+                     |
|      车体.运行 (procedure)    |
+--+      +--------------------+
   +------+

```
