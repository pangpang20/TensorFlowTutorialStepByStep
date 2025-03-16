# TensorFlowTutorialStepByStep

本项目旨在提供使用 Google TensorFlow 的动手实践指南，方便初学者使用。该存储库包括结构良好的示例、代码片段和解释，可帮助用户使用 TensorFlow 学习深度学习和机器学习概念。

## 如何使用

* 本地下载源码：

``` bash

git clone git@github.com:pangpang20/TensorFlowTutorialStepByStep.git

```

* 参考[MkDocs安装指南](https://www.mkdocs.org/#installation), 安装 Python、Pip、MkDocs 

```bash
# 更新系统包管理器，安装python3
sudo apt update && sudo apt upgrade -y
sudo apt install python3 python3-pip -y

# 安装mkdocs
pip install mkdocs

# 安装主题
pip install mkdocs-material

# 查看版本
mkdocs --version
```
 

* 启动本地WEB服务器并查看文档内容：

```bash
cd zh_CN
# 前台启动
mkdocs serve

# 也可以后台服务启动
nohup mkdocs serve --dev-addr=0.0.0.0:8000 >> /tmp/mkdocs.log 2>&1 &

```

* 通过浏览器 `http://127.0.0.1:8000/index.html` 查看文档