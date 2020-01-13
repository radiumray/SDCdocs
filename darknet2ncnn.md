
https://github.com/xiangweizeng/darknet2ncnn

sudo apt-get install pkg-config

sudo apt install libcanberra-gtk-module



qt编译部署相关问题：当主机编译的可执行文件拷贝到目标机报qt_5.13 not found 之类的的错误时候，需要修改LD_LIBRARY_PATH环境变量到QT的lib
export LD_LIBRARY_PATH=/home/md/Qt5.13.2/5.13.2/gcc_64/lib




# CMakeLists.txt

```bash


# 最低版本要求
cmake_minimum_required(VERSION 3.4.1)

project(darknetYoloV3NCNN)

# 设置C++编译版本
set(CMAKE_CXX_STANDARD 11)

# ncnn项目所在路径，需要替换
set(NCNN_DIR /home/ray/ncnn)
set(DARKNET_DIR /home/ray/darknet2ncnn)


# 分别设置ncnn的链接库和头文件
set(NCNN_LIBS ${NCNN_DIR}/build/install/lib/libncnn.a ${DARKNET_DIR}/libdarknet2ncnn.a)
set(NCNN_INCLUDE_DIRS ${NCNN_DIR}/build/install/include/ncnn ${DARKNET_DIR}/include ${NCNN_DIR}/src)


# 配置OpenCV
find_package(OpenCV REQUIRED)
include_directories(${OpenCV_INCLUDE_DIRS})

include_directories(${NCNN_INCLUDE_DIRS})

# 配置OpenMP
FIND_PACKAGE( OpenMP REQUIRED)  
if(OPENMP_FOUND)  
    message("OPENMP FOUND")  
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")  
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")  
    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} ${OpenMP_EXE_LINKER_FLAGS}")  
endif()  


# 建立链接依赖
add_executable(darknetYoloV3NCNN Main.cpp)
target_link_libraries(darknetYoloV3NCNN ${NCNN_LIBS})
target_link_libraries(darknetYoloV3NCNN ${OpenCV_LIBS})


```


```cpp

#include <stdio.h>
#include <algorithm>
#include <fstream>
#include <string>
#include <vector>

#include <opencv2/core/core.hpp>
#include <opencv2/highgui/highgui.hpp>
#include <opencv2/imgproc/imgproc.hpp>
#include <opencv2/highgui/highgui_c.h>

#include "darknet2ncnn.h"
#include "layer/input.h"
#include "ncnn_tools.h"
#include "benchmark.h"

using namespace cv;
using namespace std;

std::vector<std::string> labels;
CustomizedNet yolo;

char* lables=(char *)"models/yoloV3Lable.txt";
const char* modelParam="models/yolov3-tiny.param";
const char* modelBin="models/yolov3-tiny.bin";

// const char* modelParam="models/yolov3.param";
// const char* modelBin="models/yolov3.bin";

struct Object {
  Rect_<float> rect;
  int label;
  float prob;
};

static void draw_objects(const Mat &bgr, const std::vector<Object> &objects,
                         std::vector<std::string> &labels) {

  Mat image = bgr;
  for (size_t i = 0; i < objects.size(); i++) {
    const Object &obj = objects[i];

    // fprintf(stderr, "%-3d[%-16s] = %.5f at %.2f %.2f %.2f x %.2f\n", obj.label,
    //         labels[obj.label].c_str(), obj.prob, obj.rect.x, obj.rect.y,
    //         obj.rect.width, obj.rect.height);

    rectangle(image, obj.rect, Scalar(255, 0, 0));
    char text[256];
    sprintf(text, "%s %.1f%%", labels[obj.label].c_str(), obj.prob * 100);
    int baseLine = 0;
    Size label_size =
        getTextSize(text, FONT_HERSHEY_SIMPLEX, 0.5, 1, &baseLine);
    int x = obj.rect.x;
    int y = obj.rect.y - label_size.height - baseLine;
    if (y < 0)
      y = 0;
    if (x + label_size.width > image.cols)
      x = image.cols - label_size.width;
    rectangle(
        image,
        Rect(Point(x, y),
                 Size(label_size.width, label_size.height + baseLine)),
        Scalar(255, 255, 255), CV_FILLED);
    putText(image, text, Point(x, y + label_size.height),
                FONT_HERSHEY_SIMPLEX, 0.5, Scalar(0, 0, 0));
  }
}

static int detect_yolov(Mat &bgr) {
    ncnn::Input *input = (ncnn::Input *)yolo.get_layer_from_name("data");
    ncnn::Mat in = ncnn::Mat::from_pixels_resize(bgr.data, ncnn::Mat::PIXEL_BGR2RGB, bgr.cols, bgr.rows, input->w, input->h);
    const float norm_vals[3] = {1 / 255.0, 1 / 255.0, 1 / 255.0};
    in.substract_mean_normalize(0, norm_vals);
    int img_h = bgr.rows;
    int img_w = bgr.cols;
    ncnn::Extractor ex = yolo.create_extractor();
    ex.set_num_threads(4);
    ex.input("data", in);
    ncnn::Mat out;
    ncnn::Blob *out_blob = yolo.get_last_layer_output_blob();
    int result = ex.extract(out_blob->name.c_str(), out);
    if (result != 0) {
        printf("ncnn error: %d\n", result);
        return result;
    }
    std::vector<Object> objects;
    for (int i = 0; i < out.h; i++) {
        const float *values = out.row(i);
        Object object;
        object.label = values[0];
        object.prob = values[1];
        object.rect.x = values[2] * img_w;
        object.rect.y = values[3] * img_h;
        object.rect.width = values[4] * img_w - object.rect.x;
        object.rect.height = values[5] * img_h - object.rect.y;
        objects.push_back(object);
    }
    draw_objects(bgr, objects, labels);
    return 0;
}


int main(int argc, char **argv) {
    VideoCapture cap;
	Mat frame;
	// Create a window
	static const string kWinName = "camera";
	namedWindow(kWinName, WINDOW_NORMAL);
    cap.open(0);
    load_classifier_labels(labels, lables);
    register_darknet_layer(yolo);
    yolo.load_param(modelParam);
    yolo.load_model(modelBin);
    while (waitKey(1) < 0) {
		// get frame from the video
		cap >> frame;
		// Stop the program if reached end of video
		if (frame.empty()) {
            printf("Done processing !!!");
			waitKey(3000);
			break;
		}
        detect_yolov(frame);
		imshow(kWinName, frame);
    }
    cap.release();
    return 0;
}


```
