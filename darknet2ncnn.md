sudo apt-get install pkg-config

sudo apt install libcanberra-gtk-module



qt编译部署相关问题：当主机编译的可执行文件拷贝到目标机报qt_5.13 not found 之类的的错误时候，需要修改LD_LIBRARY_PATH环境变量到QT的lib
export LD_LIBRARY_PATH=/home/md/Qt5.13.2/5.13.2/gcc_64/lib
