
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
-D OPENCV_EXTRA_MODULES_PATH=/home/md/opencv_contrib-4.1.1/modules \
-D WITH_TBB=ON \
-D WITH_V4L=ON \
-D WITH_GTK=ON \
-D WITH_OPENGL=ON \
-D WITH_VTK=ON \
-D OPENCV_GENERATE_PKGCONFIG=YES ..



sudo make -j4

sudo make install

```
