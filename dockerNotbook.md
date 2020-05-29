
# centos django

```bash
docker pull centos

docker run -d \ # 后台运行
           -it \ # 组合使用，创建一个伪终端
           -p 805:80 \ # 端口映射，本机端口：容器端口
           -p 5002:5000 \
           -p 10027:22 \
           -p 20001:8888 \
           -p 8001:8000 \
           --name centos-django \ # 容器名，（需要注意命名规范，可自己定义 ，only [a-zA-Z0-9][a-zA-Z0-9_.-]）
           --privileged=true \
           -e LANG=zh_CN.utf8 \ # 防止乱码，编码格式
           licw1986/centos-django \
           /usr/sbin/init


```

## --privileged=true
## /usr/sbin/init
## 这两行主要解决容器中使用systemctl去启动服务时出现D_Bus错误的问题
