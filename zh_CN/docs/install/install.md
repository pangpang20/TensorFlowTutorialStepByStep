# 安装TensorFlow


## 下载软件包

使用 Python 的 pip 软件包管理器安装 TensorFlow

```bash

# 创建虚拟环境
python3 -m venv mytf

# 激活虚拟环境
source mytf/bin/activate

export PATH=/usr/local/cuda/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH


# 退出虚拟环境
#deactivate

# 更新pip
pip install --upgrade pip

# 安装CPU版本
# pip install tensorflow


 
# 安装GPU版本
pip install tensorflow==2.15.0


# 查看安装的版本
pip show tensorflow


# 在python中测试
python
import tensorflow as tf
print(tf.__version__)
print("GPU Available: ", tf.config.list_physical_devices('GPU'))
# GPU Available:  [PhysicalDevice(name='/physical_device:GPU:0', device_type='GPU')]
print("Num GPUs Available: ", len(tf.config.experimental.list_physical_devices('GPU')))
# Num GPUs Available:  1 


```

## 卸载

```bash
pip uninstall tensorflow tensorflow-gpu keras tensorboard protobuf -y 
pip cache purge
```


## 异常处理

1. root@ecs-llm:/opt# nvidia-smi
NVIDIA-SMI has failed because it couldn't communicate with the NVIDIA driver. Make sure that the latest NVIDIA driver is installed and running.
2.root@ecs-llm:/usr/src/nvidia-535.161.08# nvidia-smi
Failed to initialize NVML: Driver/library version mismatch
NVML library version: 535.183

```bash
# 安装cuda
apt install nvidia-cuda-toolkit

# 检查当前已安装的驱动
dpkg -l | grep -i nvidia

# 检查当前已安装的驱动
#说明硬件识别正常，但驱动可能有问题。
#如果没有输出：GPU 未被识别，可能存在硬件问题或虚拟机 GPU 直通未正确配置。
lspci | grep -i nvidia

 
# 安装dkms
apt install dkms -y

cd /usr/src/nvidia-535.161.08

# 将现有 NVIDIA 驱动源码加入 DKMS
dkms add -m nvidia -v 535.161.08

# 编译和安装驱动
dkms build -m nvidia -v 535.161.08
dkms install -m nvidia -v 535.161.08

# 验证内核模块已生成
# 确保有 nvidia.ko 等模块存在
ls -l /lib/modules/$(uname -r)/kernel/drivers/video/

# 重新加载 NVIDIA 模块
modprobe nvidia
modprobe nvidia-uvm
modprobe nvidia-drm
modprobe nvidia-modeset

# 检查模块是否加载成功
lsmod | grep nvidia

# 重启 NVIDIA 服务
systemctl restart nvidia-persistenced

# 检查服务状态
systemctl status nvidia-persistenced

# 验证 NVIDIA 驱动是否生效
nvidia-smi 

# 查看 NVML 库路径,so.535.161.08是对的，so.535.183.01是错的
root@ecs-llm:/usr/src# ls -lh /usr/lib/x86_64-linux-gnu/libnvidia-ml.so.*
lrwxrwxrwx 1 root root   26 May 30  2024 /usr/lib/x86_64-linux-gnu/libnvidia-ml.so.1 -> libnvidia-ml.so.535.183.01
-rwxr-xr-x 1 root root 1.9M Mar 16 11:17 /usr/lib/x86_64-linux-gnu/libnvidia-ml.so.535.161.08
-rw-r--r-- 1 root root 1.9M May 30  2024 /usr/lib/x86_64-linux-gnu/libnvidia-ml.so.535.183.01

# 验证 nvidia-smi 使用的库
root@ecs-llm:/usr/src# ldd $(which nvidia-smi)
        linux-vdso.so.1 (0x00007fff6d2a6000)
        libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007fb8acf46000)
        libm.so.6 => /lib/x86_64-linux-gnu/libm.so.6 (0x00007fb8ace5f000)
        libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007fb8ace5a000)
        libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007fb8acc31000)
        librt.so.1 => /lib/x86_64-linux-gnu/librt.so.1 (0x00007fb8acc2c000)
        /lib64/ld-linux-x86-64.so.2 (0x00007fb8acf59000)


 


# 如果以上还是不行，就重新安装 
# https://support.huaweicloud.com/usermanual-ecs/ecs_03_0199.html
wget -t 10 --timeout=10 https://hgcs-drivers-cn-north-9.obs.cn-north-9.myhuaweicloud.com/release/script/auto_install.sh && bash auto_install.sh


#如果有 TensorFlow、CUDA、X Server 等正在运行，先将其终止
# 查找并终止所有占用 GPU 的进程
sudo fuser -v /dev/nvidia*
sudo lsof /dev/nvidia*

sudo kill -9 <进程ID>

sudo modprobe -r nvidia_drm
sudo modprobe -r nvidia_modeset
sudo modprobe -r nvidia_uvm
sudo modprobe -r nvidia
systemctl stop nvidia-persistenced
systemctl disable nvidia-persistenced

sed -i '/cuda/d' ~/.bashrc
source ~/.bashrc

echo $PATH | grep cuda
echo $LD_LIBRARY_PATH | grep cuda

sudo rm -rf /usr/local/cuda-12.2
sudo rm -rf /usr/local/cuda


# 升级cuda
sudo apt purge nvidia-cuda-toolkit
sudo apt autoremove
# 安装CUDA 
wget https://developer.download.nvidia.com/compute/cuda/12.4.0/local_installers/cuda_12.4.0_550.54.14_linux.run

 
sudo sh cuda_12.4.0_550.54.14_linux.run --silent --toolkit

wget https://developer.download.nvidia.com/compute/cuda/12.2.2/local_installers/cuda_12.2.2_535.104.05_linux.run
sudo sh cuda_12.2.2_535.104.05_linux.run --silent --toolkit

wget https://developer.download.nvidia.com/compute/cudnn/redist/cudnn/linux-x86_64/cudnn-linux-x86_64-8.9.0.131_cuda12-archive.tar.xz 
tar -xvf cudnn-linux-x86_64-8.9.0.131_cuda12-archive.tar.xz  
sudo cp cudnn-linux-x86_64-8.9.0.131_cuda12-archive/include/* /usr/local/cuda/include  
sudo cp cudnn-linux-x86_64-8.9.0.131_cuda12-archive/lib/* /usr/local/cuda/lib64  

cd /usr/local/cuda-12.4/targets/x86_64-linux/lib/
sudo ln -sf libcudnn_cnn_train.so.8.9.2 libcudnn_cnn_train.so.8
sudo ln -sf libcudnn_cnn_infer.so.8.9.2 libcudnn_cnn_infer.so.8
sudo ln -sf libcudnn_adv_train.so.8.9.2 libcudnn_adv_train.so.8
sudo ln -sf libcudnn_adv_infer.so.8.9.2 libcudnn_adv_infer.so.8
sudo ln -sf libcudnn_ops_infer.so.8.9.2 libcudnn_ops_infer.so.8
sudo ln -sf libcudnn_ops_train.so.8.9.2 libcudnn_ops_train.so.8
sudo ln -sf libcudnn.so.8.9.2 libcudnn.so.8
ls -lh | grep cudnn


sudo ldconfig
echo 'export PATH=/usr/local/cuda/bin:$PATH' >> ~/.bashrc
echo 'export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH' >> ~/.bashrc
source ~/.bashrc

nvcc --version
systemctl start nvidia-persistenced
systemctl enable nvidia-persistenced

sudo reboot
source /opt/mytf_new/bin/activate
python -c "import tensorflow as tf; print(tf.config.list_physical_devices('GPU'))"


```

