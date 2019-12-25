## 获取ncnn
https://github.com/Tencent/ncnn

## 编译ncnn
```bash

cd ncnn
mkdir -p build
cd build
cmake ..
make -j4
make install

```

## 安装onnx-simplifier
```bash
pip install onnx-simplifier

```
 ## 使用
```bash

# onnx简化模型
python -m onnxsim model.onnx model-sim.onnx

# onnx转换成ncnn
# 进入build/tools/onnx,如果编译成功会有onnx2ncnn转换工具
./onnx2ncnn model-sim.onnx model.param model.bin

# caff转换成ncnn
./caffe2ncnn mobilnet_yolo_deploy.prototxt mobilenet_yolo_deploy_iter_80000.caffemodel mobilenet_yolo.param mobilenet_yolo.bin


```
