> # 图形块基础功能说明
(ascii在线生成器:http://asciiflow.com/)

>> ## 启用驱动
```python

import os

os.system("gnome-terminal -e 'bash ./LRSDK/DriveHAL/DriveHAL.sh'") 


# 图形块样式
+--+     +-------------------+
|  +-----+                   |
|      启动驱动               |
+--+     +-------------------+
   +-----+


```

>> ## 车体模块
>>> ### 车体实例化
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
+--+     +-----------------------------------------------+
   +-----+
```
>>> ### 车体方法
>>>> #### 车体运行
```python
# 车体模块运行方法
app.run(自定义方法名：例如procedure)

# 图形块样式
+--+     +---------------------+
|  +-----+                     |
|      车体.运行 (procedure)    |
+--+     +---------------------+
   +-----+

```
>>>> #### 车体遥控器
```python
# 车体遥控器获取方法, 获取角度与速度指令 （返回值为2个小数值，范围 -1~1 ）
angle, throttle = app.get_joystick_info()

# 图形块样式
    +-------------------------+
    |                         |
+---+                         |
|          车体.遥控器指令     |
+---+                         |
    |                         |
    +-------------------------+

```

>>>> #### 车体摄像头
```python
# 设置启用名为wideAngle的摄像头, 也就是车上的冲下方的广角摄像头
app.setup_camera(dev_name="/dev/wideAngle")

# 图形块样式
+--+     +-----------------------------------+
|  +-----+                                   |
|      车体.摄像头配置地址: (/dev/wideAngle)  |
+--+     +-----------------------------------+
   +-----+

# 获取广角摄像头图像
frame_wide_drive = app.get_image(dev_name="/dev/wideAngle")
 
# 图形块样式
    +----------------------------------------------+
    |                                              |
+---+                                              |
|          车体.获取摄像头地址: (/dev/wideAngle)    |
+---+                                              |
    |                                              |
    +----------------------------------------------+
 
# 截取摄像头图像区域,起始坐标为(0, 0), 终止坐标为(640, 380)
frame_wide_drive = app.cut_active_area(frame_wide_drive, coordinate_start=(0, 0), coordinate_end=(640, 380), padding=False)
# 图形块样式
    +------------------------------------------------+
    |                                                |
+---+                                                |
|          车体.摄像头剪切: 起始(0, 0) 终止(640, 380)  |
+---+                                                |
    |                                                |
    +------------------------------------------------+
    
# 说明：
+-------------------------------------------+
|                                           |
|        起始（x,y）                         |
|        +---------------------+            |
|        |                     |            |
|        |                     |            |
|        |                     |            |
|        |                     |            |
|        |                     |            |
|        |                     |            |
|        |                     |            |
|        +---------------------+            |
|                              终止(x,y)     |
|                                           |
|                                           |
|                                           |
+-------------------------------------------+

# 改变图像大小为(224, 224)
frame_wide_drive = app.resize(frame_wide_drive, (224, 224))
# 图形块样式
    +--------------------------------------+
    |                                      |
+---+                                      |
|          车体.摄像头缩放: (224, 224)      |
+---+                                      |
    |                                      |
    +--------------------------------------+


# 显示图像
app.show_image(window_name="frame_wide", frame=frame_wide_drive, show_fps=True)

# 图形块样式
+--+     +----------------------+
|  +-----+                      |
|      车体.摄像头显示           |
+--+     +----------------------+
   +-----+

# 发送图像到网页服务
app.web_video_broadcast(frame_list=[frame_wide_drive])
# frame_list 为一个数组列表
# [frame_wide_drive] 为数组有一个元素的情况，最多两个， frame_wide_drive为 numpy array类型

# 图形块样式
+--+     +-------------------------------+
|  +-----+                               |
|      车体.网页广播(frame_wide_drive)    |
+--+     +-------------------------------+
   +-----+

```


>> ## 控制模块
>>> ### 控制实例化
```python

# 控制模块依赖包引用
from LRSDK.appController import AppController

# 实例化控制模块
control = AppController()

# 图形块样式
+--+     +---------------------+
|  +-----+                     |
|      控制模块                 |
+--+     +---------------------+
   +-----+
```
>>> ### 控制方法
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
+--+     +------------------------------+
   +-----+

```
>>> ### 控制返回
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

>> ## 自定义功能
```python
# 定义用户实现逻辑
def procedure():
	xxxxxx
   return control

# 图形块样式
+-----------------------------------------------+
|                                               |
|     (自定义函数名 比如:procedure)              |
|                                               |
|        +--------------------------------------+
|        |
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

>> ## 数据存储模块
>>> ### 数据存储实例化

```python
# 调用数据存储模块
from LRSDK.dataStore import DataStore

# 实例化数据存储模块,设置存储路径
data_store = DataStore(data_folder="./data")

# 图形块样式
+--+     +------------------------+
|  +-----+                        |
|      数据模块 存储地址:(./data)  |
+--+     +------------------------+
   +-----+


```

>>> ### 数据存储方法
```python
# 将当前图像和对应的指令标签存贮到指定目录用于后续训练(一张图片对应一个标签)
data_store.save(frame_wide_drive, angle, throttle)
# frame_wide_drive 处理后的采集图片，类型为 numpy array
# angle 遥控器针对此图片的角度, 类型为小数(-1~1)
# throttle 遥控器针对此图片的速度, 类型为小数(-1~1)

# 图形块样式
+--+     +-------------------------------------------+
|  +-----+                                           |
|      数据.保存（frame_wide_drive, angle, throttle） |
+--+     +-------------------------------------------+
   +-----+

```


>> ## 模型推理模块
>>> ### 模型推理实例化

```python
# 引用自动驾驶模型模块
from baseModels.KerasBaseModel import DriveModel

# 指定模型路径
model_path = './output/default_2019_05_10_los012.h5'

# 实例化自动驾驶模块, 指定模型路径
drive_model = DriveModel(model_path=model_path)

# 图形块样式
+--+     +-------------------------------------------------------+
|  +-----+                                                       |
|      推理模块 模型地址:(./output/default_2019_05_10_los012.h5)  |
+--+     +-------------------------------------------------------+
   +-----+


```

>>> ### 模型推理方法

```python

# 模型推理，并获取预测角度和速度
angle, throttle = drive_model.predict_image(frame_wide_drive)
# frame_wide_drive 为摄像头获取的图像

# 图形块样式
    +---------------------------------------+
    |                                       |
+---+                                       |
|          推理模块.预测(frame_wide_drive)   |
+---+                                       |
    |                                       |
    +---------------------------------------+


```


>> ## 行人识别模块
>>> ### 行人识别实例化

```python
# 引用openvino的行人识别模型
from baseModels.vino.vino_pedestrain_detector import PedestrainDetector

# 实例化行人模块
pedestrain_detector_obj = PedestrainDetector()

# 图形块样式
+--+     +-----------------+
|  +-----+                 |
|      行人模型             |
+--+     +-----------------+
   +-----+

```

>>> ### 行人识别方法

```python

# 运行行人识别预处理
input_img = pedestrain_detector_obj.preprocess_img(frame_wide_person)
# 预测图像中行人的框图
out = pedestrain_detector_obj.predict(input_img)
# 在图像上画出框图, 并显示时候有人状态
drawed_frame_person, has_pedestrain = pedestrain_detector_obj.draw_box(out)

# 输入frame_wide_person：为numpy array 图像
# 输出drawed_frame_person, has_pedestrain: 
+ drawed_frame_person:为显示框图，类型为numpy array 图像
+ has_pedestrain:为bool类型，代表图片中是否有行人

# 图形块样式
    +----------------------------------------+
    |                                        |
+---+                                        |
|          行人模型.预测(frame_wide_person)   |
+---+                                        |
    |                                        |
    +----------------------------------------+


```


>> ## 红绿灯识别模块
>>> ### 红绿灯识别实例化

```python

# 调用opencv级联分类器用来定位红绿灯
from baseModels.signal_light.cv_get_signal_light_area import CvSignalLight
# 调用红绿灯分类模型区分红,黄,绿灯
from baseModels.signal_light.keras_detect_signal_light import KerasSignalLight

# 实例化opencv级联分类器
cv_obj = CvSignalLight()
# 实例化红绿灯分类模型
keras_obj = KerasSignalLight()
# 调用红绿灯分类模型
keras_obj.load_model("./baseModels/signal_light/classification/models/signal_light_model.h5")


# 图形块样式
+--+     +---------------------------------------------------------------------------------------+
|  +-----+                                                                                       |
|      红绿灯模型：地址 (./baseModels/signal_light/classification/models/signal_light_model.h5)   |
+--+     +---------------------------------------------------------------------------------------+
   +-----+

```

>>> ### 红绿灯识别方法

```python

# 定义一个函数用来完善红绿灯模型
def getTrafficLightResult(frame_normal_light):
	label = None
	# 得到红绿灯的框坐标
	signal_frame, box = cv_obj.get_signal_light(frame_normal_light)
	# 如果有框则框出红绿灯
	if box != ():
		# 对框出的红绿灯进行分类
		label = keras_obj.detect_each(signal_frame)
		# 在原图中框出红绿灯
		cv_frame_drawed = cv_obj.draw_box_label(frame_normal_light, box, label)
		detect_frame = cv_frame_drawed
	else:
		detect_frame = frame_normal_light
	return detect_frame, label
	
# 调用红绿灯识别函数
detect_frame, label = getTrafficLightResult(frame_normal_light)

# 输入是图像
# 输出：
+ detect_frame：为识别的红绿灯图像，没有则是原图
+ label：为识别红绿灯的类型(red, yellow, green),没有则是None


# 图形块样式
    +------------------------------------------+
    |                                          |
+---+                                          |
|          红绿灯模型.预测(frame_normal_light)  |
+---+                                          |
    |                                          |
    +------------------------------------------+


```



>> ## 停止标志识别模块
>>> ### 停止标志识别实例化

```python

# 引用停止标示模型模块
from baseModels.ssd_keras.keras_detect_sign import KerasSign

# 实例化停止标示模型
KerasSign_obj = KerasSign()
# 加载停止标示模型
KerasSign_obj.loadModel(model_path="./baseModels/ssd_keras/weights/sign_model.h5")
# 定义一个线程运行此模型运行
t = Thread(target=KerasSign_obj.update, args=())
# 启动线程
t.start()

# 图形块样式
+--+     +-------------------------------------------------------------------+
|  +-----+                                                                   |
|      停止标志模型：地址 (./baseModels/ssd_keras/weights/sign_model.h5)      |
+--+     +-------------------------------------------------------------------+
   +-----+

```

>>> ### 停止标志识别方法

```python

# 线程运行停止标示模型
frame_drawed_stop, label_list = KerasSign_obj.run_threaded(frame_normal_stop)

# 输入是图像
# 输出：
+ frame_normal_stop：为识别的红绿灯图像，没有则是原图
+ label_list：为list类型 if label_list != []: print('stop') 用来判断是否有停止标志


# 图形块样式
    +------------------------------------------+
    |                                          |
+---+                                          |
|          停止标志模型.预测(frame_normal_stop) |
+---+                                          |
    |                                          |
    +------------------------------------------+


```



-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
> # 示例代码 

>> 速度方向控制

```python
import os
import time
from LRSDK.application import Application
from LRSDK.appController import AppController

# 通过终端向驱动控制板发送串口指令
os.system("gnome-terminal -e 'bash ./LRSDK/DriveHAL/DriveHAL.sh'") #连接小车主板
app = Application(fps=25, operate_mode="ManualDrive")
control = AppController()

start = time.time() #开始时间
def drive_fun():
    global start #使用上面定义好的全局变量start
    end = time.time() # 更新当前时间

    if (end-start) <= 1: #前1秒
        control.angle = 1 #左拐
        control.throttle  = 0.1 #向前速度为0.1
    elif (end-start) > 1 and (end-start) <= 2: # 1到2秒
        control.angle = 0 # 直走
        control.throttle  = 0 #向前速度为 0.1
    else: #2秒之后
        start = time.time() # 更新开始时间

    return control
app.run(drive_fun)

```

>> 遥控器接收指令

```python

from LRSDK.application import Application
from LRSDK.appController import AppController

app = Application(fps=25)
control = AppController()

def collect_fun():
    # 获取遥控器数据
    angle, throttle = app.get_joystick_info()
    # 打印方向和速度信息
    print('angle:', angle)
    print('throttle:', throttle)

    return control

app.run(collect_fun)

```

>> 遥控车体验
```python

from LRSDK.application import Application
from LRSDK.appController import AppController
import os

os.system("gnome-terminal -e 'bash ./LRSDK/DriveHAL/DriveHAL.sh'") #连接小车主板

app = Application(fps=25)
control = AppController()

def collect_fun():
    # 获取遥控器数据
    angle, throttle = app.get_joystick_info()
    # 通过角度和速度控制车的舵机和电机
    control.angle = angle
    control.throttle = throttle
    return control

app.run(collect_fun)

```

>> 摄像头相关

```python

from LRSDK.application import Application
from LRSDK.appController import AppController

app = Application(fps=25)
control = AppController()
# 设置启用名为wideAngle的摄像头, 也就是车上的冲下方的广角摄像头
app.setup_camera(dev_name="/dev/wideAngle")

def collect_fun():
    # 获取广角摄像头图像
    frame_wide_drive = app.get_image(dev_name="/dev/wideAngle")
    # 截取摄像头图像区域,起始坐标为(0, 0), 终止坐标为(640, 380)
    frame_wide_drive = app.cut_active_area(frame_wide_drive, coordinate_start=(0, 0), coordinate_end=(640, 380),
                                           padding=False)  # 200W
    # 改变图像大小为(224, 224)
    frame_wide_drive = app.resize(frame_wide_drive, (224, 224))
    # 显示图像
    app.show_image(window_name="frame_wide", frame=frame_wide_drive, show_fps=True)

    return control

app.run(collect_fun)

```


>> 数据存储

```python

from LRSDK.application import Application
from LRSDK.appController import AppController
# 调用数据存储模块
from LRSDK.dataStore import DataStore

app = Application(fps=25)
control = AppController()
# 设置启用名为wideAngle的摄像头, 也就是车上的冲下方的广角摄像头
app.setup_camera(dev_name="/dev/wideAngle")
# 实例化数据存储模块,设置存储路径
data_store = DataStore(data_folder="./data")

def collect_fun():
    # 获取广角摄像头图像
    frame_wide_drive = app.get_image(dev_name="/dev/wideAngle")
    # 截取摄像头图像区域,起始坐标为(0, 0), 终止坐标为(640, 380)
    frame_wide_drive = app.cut_active_area(frame_wide_drive, coordinate_start=(0, 0), coordinate_end=(640, 380),
                                           padding=False)  # 200W
    # 改变图像大小为(224, 224)
    frame_wide_drive = app.resize(frame_wide_drive, (224, 224))
    # 显示图像
    app.show_image(window_name="frame_wide", frame=frame_wide_drive, show_fps=True)
    # 获取遥控器指令
    angle, throttle = app.get_joystick_info()
    # 将当前图像和对应的指令标签存贮到指定目录用于后续训练(一张图片对应一个标签)
    data_store.save(frame_wide_drive, angle, throttle)

    return control

app.run(collect_fun)

```


>> 遥控采集数据

```python

from LRSDK.application import Application
from LRSDK.appController import AppController
# 调用数据存储模块
from LRSDK.dataStore import DataStore
import os

app = Application(fps=25)
control = AppController()
# 设置启用名为wideAngle的摄像头, 也就是车上的冲下方的广角摄像头
app.setup_camera(dev_name="/dev/wideAngle")
# 实例化数据存储模块,设置存储路径
data_store = DataStore(data_folder="./data")
# 启动控制板驱动
os.system("gnome-terminal -e 'bash ./LRSDK/DriveHAL/DriveHAL.sh'")

def collect_fun():
    # 获取广角摄像头图像
    frame_wide_drive = app.get_image(dev_name="/dev/wideAngle")
    # 截取摄像头图像区域,起始坐标为(0, 0), 终止坐标为(640, 380)
    frame_wide_drive = app.cut_active_area(frame_wide_drive, coordinate_start=(0, 0), coordinate_end=(640, 380),
                                           padding=False)  # 200W
    # 改变图像大小为(224, 224)
    frame_wide_drive = app.resize(frame_wide_drive, (224, 224))
    # 显示图像
    app.show_image(window_name="frame_wide", frame=frame_wide_drive, show_fps=True)
    # 获取遥控器指令
    angle, throttle = app.get_joystick_info()
    # 将当前图像和对应的指令标签存贮到指定目录用于后续训练(一张图片对应一个标签)
    data_store.save(frame_wide_drive, angle, throttle)

    # 通过接收指令控制舵机和电机
    control.angle = angle
    control.throttle = throttle

    return control

app.run(collect_fun)


```

>> 推理模型

```python

import os
from LRSDK.application import Application
from LRSDK.appController import AppController
# 引用自动驾驶模型模块
from baseModels.KerasBaseModel import DriveModel

# 指定模型路径
model_path = './output/default_2019_05_10_los012.h5'

# 通过终端向驱动控制板发送串口指令
os.system("gnome-terminal -e 'bash ./LRSDK/DriveHAL/DriveHAL.sh'")
# 实例化应用模块, 模式为AutoDrive, 此时会调用网页控制服务
app = Application(fps=25, operate_mode="AutoDrive")
# 配置摄像头
app.setup_camera(dev_name="/dev/wideAngle")

control = AppController()
# 实例化自动驾驶模块, 指定模型路径
drive_model = DriveModel(model_path=model_path)

def autoDrive_fun():
    frame_wide_drive = app.get_image(dev_name="/dev/wideAngle")
    frame_wide_drive = app.cut_active_area(frame_wide_drive, coordinate_start=(0, 0), coordinate_end=(640, 380),
                                           padding=False)  # 200W
    frame_wide_drive = app.resize(frame_wide_drive, (224, 224))
    app.show_image(window_name="frame_wide", frame=frame_wide_drive, show_fps=True)
    angle, throttle = drive_model.predict_image(frame_wide_drive)
    # 发送图像到网页服务
    app.web_video_broadcast(frame_list=[frame_wide_drive])
    control.angle = angle
    control.throttle = throttle
    return control

app.run(autoDrive_fun)


```


>> 行人识别

```python

from LRSDK.application import Application
from LRSDK.appController import AppController
# 引用openvino的行人识别模型
from baseModels.vino.vino_pedestrain_detector import PedestrainDetector

app = Application(fps=30)
control = AppController()
# 实例化行人模块
pedestrain_detector_obj = PedestrainDetector()
# 启用设备名为normal的摄像头, 也就是平视摄像头
app.setup_camera(dev_name="/dev/normal")

def autoDrive_fun():

    # 获取平视摄像头图像
    frame_wide_person = app.get_image(dev_name="/dev/normal")
    # 调整图像尺寸
    frame_wide_person = app.resize(frame_wide_person, (520, 390))
    # 截取图像区域
    frame_wide_person = app.cut_active_area(frame_wide_person, coordinate_start=(0, 180), coordinate_end=(520, 390),
                                            padding=True)
    # 运行行人识别模型
    input_img = pedestrain_detector_obj.preprocess_img(frame_wide_person)
    # 预测图像中行人的框图
    out = pedestrain_detector_obj.predict(input_img)
    # 在图像上画出框图, 并显示时候有人状态
    drawed_frame_person, has_pedestrain = pedestrain_detector_obj.draw_box(out)
    # 显示处理后的图像
    app.show_image(window_name="pedestrain_detector", frame=drawed_frame_person, show_fps=True)

    # 如果有人打印　|||||||
    if has_pedestrain:
        print('|||||||')
    else: # 没人打印　------
        print('------')

    return control

app.run(autoDrive_fun)

```

>> 红绿灯识别
```python

from LRSDK.application import Application
from LRSDK.appController import AppController
# 调用opencv级联分类器用来定位红绿灯
from baseModels.signal_light.cv_get_signal_light_area import CvSignalLight
# 调用红绿灯分类模型区分红,黄,绿灯
from baseModels.signal_light.keras_detect_signal_light import KerasSignalLight

app = Application(fps=30)
control = AppController()

app.setup_camera(dev_name="/dev/normal")

# 实例化opencv级联分类器
cv_obj = CvSignalLight()
# 实例化红绿灯分类模型
keras_obj = KerasSignalLight()
# 调用红绿灯分类模型
keras_obj.load_model("./baseModels/signal_light/classification/models/signal_light_model.h5")

def autoDrive_fun():
    label = None

    # 获取平视摄像头图像
    frame_normal_light = app.get_image(dev_name="/dev/normal")
    # 调整图像大小
    frame_normal_light = app.resize(frame_normal_light, (520, 390))
    # 截取图像区域
    frame_normal_light = app.cut_active_area(frame_normal_light, coordinate_start=(0, 0), coordinate_end=(520, 150))
    # 得到红绿灯的框坐标
    signal_frame, box = cv_obj.get_signal_light(frame_normal_light)
    # 如果有框则框出红绿灯
    if box != ():
        # 对框出的红绿灯进行分类
        label = keras_obj.detect_each(signal_frame)
        # 在原图中框出红绿灯
        cv_frame_drawed = cv_obj.draw_box_label(frame_normal_light, box, label)
        detect_frame = cv_frame_drawed
    else:
        detect_frame = frame_normal_light
    # 显示图像
    app.show_image(window_name="detect_frame", frame=detect_frame, show_fps=True)

    # 如果红灯输出red
    if label == "red":
        print('red')
    # 如果黄灯输出yellow
    if label == "yellow":
        print('yellow')

    return control

app.run(autoDrive_fun)

```

>> 停止标志识别
```python

from threading import Thread

from LRSDK.application import Application
from LRSDK.appController import AppController
# 引用停止标示模型模块
from baseModels.ssd_keras.keras_detect_sign import KerasSign

app = Application(fps=30)
control = AppController()
# 实例化停止标示模型
KerasSign_obj = KerasSign()
# 加载停止标示模型
KerasSign_obj.loadModel(model_path="./baseModels/ssd_keras/weights/sign_model.h5")
# 定义一个线程运行此模型运行
t = Thread(target=KerasSign_obj.update, args=())
# 启用平视摄像头
app.setup_camera(dev_name="/dev/normal")
# 启动线程
t.start()

def autoDrive_fun():

    frame_normal_stop = app.get_image(dev_name="/dev/normal")
    frame_normal_stop = app.resize(frame_normal_stop, (520, 390))
    # 线程运行停止标示模型
    frame_drawed_stop, label_list = KerasSign_obj.run_threaded(frame_normal_stop)
    # 显示图像
    app.show_image(window_name="sign_detector", frame=frame_drawed_stop, show_fps=True)

    # 如果有停止标示做出相应反应
    if label_list != []:
        print('stop')

    return control

app.run(autoDrive_fun)


```
