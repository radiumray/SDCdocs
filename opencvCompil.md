
https://www.cnblogs.com/raina/p/11365854.html

```bash

sudo apt-get install build-essential
sudo apt-get install cmake git libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev
sudo apt-get install python-dev python-numpy libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev libjasper-dev libdc1394-22-dev # 处理图像所需的包
sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev libv4l-dev liblapacke-dev
sudo apt-get install libxvidcore-dev libx264-dev # 处理视频所需的包
sudo apt-get install libatlas-base-dev gfortran # 优化opencv功能
sudo apt-get install ffmpeg

## 出现下面错误
errorE: unable to locate libjasper-dev

sudo apt-get install software-properties-common
sudo add-apt-repository "deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ xenial main multiverse restricted universe"
sudo apt update
sudo apt install libjasper1 libjasper-dev


cd /xxxxx/xxxxx/opencv-4.1.1
sudo mkdir build
cd build

sudo cmake -D CMAKE_BUILD_TYPE=RELEASE \
-D CMAKE_INSTALL_PREFIX=/usr/local \
-D OPENCV_EXTRA_MODULES_PATH=/home/[xxxxxxx]/opencv_contrib-4.1.1/modules \
-D WITH_TBB=ON \
-D WITH_V4L=ON \
-D WITH_GTK=ON \
-D WITH_OPENGL=ON \
-D WITH_VTK=ON \
-D OPENCV_GENERATE_PKGCONFIG=YES ..

'''
sudo cmake -D CMAKE_BUILD_TYPE=RELEASE \
-D CMAKE_INSTALL_PREFIX=/usr/local \
-D OPENCV_EXTRA_MODULES_PATH=/home/[xxxxxxx]/opencv_contrib-4.1.1/modules \
-D CMAKE_PREFIX_PATH=/home/[xxxxxxx]/Qt5.13.2/5.13.2/gcc_64/lib/cmake/Qt5 \
-D WITH_TBB=ON \
-D WITH_V4L=ON \
-D WITH_QT=ON \
-D WITH_GTK=ON \
-D WITH_OPENGL=ON \
-D WITH_VTK=ON \
-D OPENCV_GENERATE_PKGCONFIG=YES ..
'''

sudo apt-get install cmake-qt-gui
进入opencv的build
sudo cmake-gui ..
解决下载boostdesc_bgm.i之类的问题

sudo make -j4

sudo make install

sudo sh -c 'echo "/usr/local/lib" > /etc/ld.so.conf.d/opencv.conf'
sudo ldconfig


```

/opt/Qt5.13.0/5.13.0/gcc_64/lib/cmake/Qt5是我Qt的Qt5Config.cmake所在路径, 需要改成你自己的, 如果不需要opencv支持Qt用户界面, 可以把-D WITH_QT=ON \和-D CMAKE_PREFIX_PATH=/opt/Qt5.13.0/5.13.0/gcc_64/lib/cmake/Qt5 \两行删掉.
另外, 不指定"Qt5Config.cmake"所在路径, 在cmake编译的时候可能会报如下错误:

CMake Error at cmake/OpenCVFindLibsGUI.cmake:18 (find_package):
Could not find a package configuration file provided by "Qt5" with any of
the following names:

 Qt5Config.cmake
 qt5-config.cmake
Add the installation prefix of "Qt5" to CMAKE_PREFIX_PATH or set "Qt5_DIR"
to a directory containing one of the above files. If "Qt5" provides a
separate development package or SDK, be sure it has been installed.

配置OpenCV环境
sudo gedit /etc/ld.so.conf.d/opencv.conf
在文件最后添加

/usr/local/lib
生效配置:

sudo ldconfig

