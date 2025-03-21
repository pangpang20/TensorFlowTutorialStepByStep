# 本地部署Deepseek

使用Ollama安装蒸馏版本

## 安装Ollama

``` bash
curl -fsSL https://ollama.com/install.sh | sh
```

## 部署DeepSeek模型

``` bash


ollama run deepseek-r1:7b

# 先不要关闭，后面的操作在新的窗口执行

# 后台运行
nohup ollama run deepseek-r1:7b > /tmp/deepseek.log 2>&1 &

```
## 部署词向量模型

``` bash
ollama pull mxbai-embed-large
```

## 构建RAG向量数据库

``` bash

# 创建虚拟环境
python3 -m venv mydeepseek

# 激活虚拟环境
source mydeepseek/bin/activate

# 退出虚拟环境
deactivate

# 克隆项目：结合了DeepSeek R1 模型的 ChatPDF 项目
# 旨在通过检索增强生成（RAG）技术，实现对上传的 PDF 文档进行问答的功能
git clone https://github.com/paquino11/chatpdf-rag-deepseek-r1
cd chatpdf-rag-deepseek-r1

# 安装依赖
pip install -r requirements.txt



```

## 配置RAG向量数据库实现

```bash
vi chatpdf-rag-deepseek-r1/rag.py
# 将23行的deepseek-r1:latest改为前面部署的deepseek-r1:7b
23:    def __init__(self, llm_model: str = "deepseek-r1:7b", embedding_model: str = "mxbai-embed-large"):


cd chatpdf-rag-deepseek-r1
# 后台任务运行程序
nohup streamlit run app.py >> rag.log 2>&1 &
```


使用huggingface安装
## 下载huggingface的模型

模型下载工具地址：
https://gist.github.com/padeoe/697678ab8e528b85a2a7bddafea1fa4f

模型地址：

https://huggingface.co/deepseek-ai/DeepSeek-R1/tree/main

```bash
python3 -m venv huggingfaceDeepseek
source huggingfaceDeepseek/bin/activate

# 设置镜像地址
export HF_ENDPOINT="https://hf-mirror.com"
# 创建模型下载存放路径
mkdir model_download
# 进入该目录
cd model_download
# 下载所需的脚本
wget https://gist.githubusercontent.com/padeoe/697678ab8e528b85a2a7bddafea1fa4f/raw/3ed815fdcef9a30bd985772ab95ca57801c80bfa/hfd.sh
# 修改文件的执行权限
chmod a+x hfd.sh
# 注册执行命令
alias hfd="$PWD/hfd.sh"

apt update
apt install aria2

# 下载模型
hfd deepseek-ai/deepseek-llm-7b-chat -x 8 -j 8
hfd deepseek-ai/deepseek-llm-67b-chat -x 8 -j 8



```