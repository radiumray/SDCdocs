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

# 设置启用名为wideAngle的摄像头, 也就是车上的冲下方的广角摄像头
app.setup_camera(dev_name="/dev/wideAngle")

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

>> ## 车体

```python
# 获取广角摄像头图像
frame_wide_drive = app.get_image(dev_name="/dev/wideAngle")
# 截取摄像头图像区域,起始坐标为(0, 0), 终止坐标为(640, 380)
frame_wide_drive = app.cut_active_area(frame_wide_drive, coordinate_start=(0, 0), coordinate_end=(640, 380), padding=False)
# 改变图像大小为(224, 224)
frame_wide_drive = app.resize(frame_wide_drive, (224, 224))

# 图形块样式
    +----------------------------------------------+
    |                                              |
+---+                                              |
|          车体.获取摄像头地址: (/dev/wideAngle)    |
+---+                                              |
    |                                              |
    +----------------------------------------------+

# 显示图像
app.show_image(window_name="frame_wide_drive", frame=frame_wide_drive, show_fps=True)

# 图形块样式
+--+     +----------------------+
|  +-----+                      |
|      车体.摄像头显示           |
+--+     +----------------------+
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
