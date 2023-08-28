# remote

默认已经安装nvidia驱动，如525
#### 安装cuda
1. https://developer.nvidia.com/cuda-toolkit-archive下载适合的cuda-toolkit
```
wget https://developer.download.nvidia.com/compute/cuda/11.8.0/local_installers/cuda_11.8.0_520.61.05_linux.run
sudo sh cuda_11.8.0_520.61.05_linux.run
```
2. 不安装驱动driver
3. 更新环境变量
```
vim ~/.bashrc
```
在最后行添加如下
```
export PATH="$PATH:/usr/local/cuda-11.8/bin"
export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/usr/local/cuda-11.8/lib64/"
export LIBRARY_PATH="$LIBRARY_PATH:/usr/local/cuda-11.8/lib64"
```
更新环境变量
```
source ~/.bashrc
```

4.查看是否安装成功,注意是大V
```
nvcc -V
```

#### 安装cudnn
1. https://developer.nvidia.com/rdp/cudnn-archive 下载对应cudnn，第一次需注册和填写调查问卷
2. 解压
```
cd 路径
tar -xvf cudnn-linux-x86_64-8.9.2.26_cuda11-archive.tar.xz
```
3. 拷贝cudnn文件到cuda文件夹
```
cp /mnt/cudnn-*-archive/include/cudnn*.h /usr/local/cuda/include
cp -P /mnt/cudnn-*-archive/lib/libcudnn* /usr/local/cuda/lib64
```
4. 赋权限
```
chmod a+r /usr/local/cuda/include/cudnn*.h /usr/local/cuda/lib64/libcudnn*
```

5. 查看是否安装成功

```
cat /usr/local/cuda/include/cudnn_version.h | grep CUDNN_MAJOR -A 2
```

#### 下载工程文件sd_webui

```
cd /mnt/feature-dreambooth/
git clone -b feature-dreambooth http://gitlab.53site.com/ai/stable-diffusion-web-ui.git
```

#### 安装python3.10

- 更新源
```
apt-get update
apt-get install -y \
        make \
        build-essential \
        libssl-dev \
        zlib1g-dev \
        libbz2-dev \
        libreadline-dev \
        libsqlite3-dev \
        wget \
        curl \
        llvm \
        libncurses5-dev \
        libncursesw5-dev \
        xz-utils \
        tk-dev \
        libffi-dev \
        liblzma-dev \
        git \
        openssl \
        locales-all
```

- 切换到工程目录并下载解压python3.10版本
```
cd /mnt/feature-dreambooth/stable-diffusion-web-ui/
wget https://registry.npmmirror.com/-/binary/python/3.10.10/Python-3.10.10.tgz
tar -xf Python-3.10.10.tgz
```

- 配置python安装执行目录并指定并行编译加速
```
cd /mnt/feature-dreambooth/stable-diffusion-web-ui/
mkdir python
cd /mnt/feature-dreambooth/stable-diffusion-web-ui/Python-3.10.10
./configure --prefix=/mnt/feature-dreambooth/stable-diffusion-web-ui/python
make -j 12
```
或者本地安装在/usr/local/


- 编译python
```
make altinstall
```

- 映射Python3 pip3软连接

```
ln -fs /mnt/feature-dreambooth/stable-diffusion-web-ui/python/bin/python3.10 /usr/bin/python3
ln -fs /mnt/feature-dreambooth/stable-diffusion-web-ui/python/bin/pip3.10 /usr/bin/pip3

ln -fs /mnt/sd2.0/stable-diffusion-webui/python/bin/python3.10 /usr/bin/python3
ln -fs /mnt/sd2.0/stable-diffusion-webui/python/bin/pip3.10 /usr/bin/pip3
````

- 检查映射是否成功

```
ls -al python*
```

#### 安装虚拟环境venv

- 安装venv

```
sudo apt-get install python3-venv
```

- 在指定目录创建venv

```
cd /mnt/feature-dreambooth/stable-diffusion-web-ui/
python3 -m venv venv
```

- 激活虚拟环境
```
source venv/bin/activate
```
注：如需退出虚拟环境可执行`deactivate`

- 升级pip（以后都需激活虚拟环境）
```
pip install --upgrade pip
```

- 根据环境进行pip安装
```
pip install -r requirements_versions.txt
```

`注意：由于启动项目（bash /mnt/feature-dreambooth/stable-diffusion-web-ui/webui.sh --api --env=dev --xformers --listen --enable-insecure-extension-access）不能用root用户启动（不允许），需要切换到子用户启动服务，所以为了pip方便，最好把venv、python的用户和用户组所有权给子用户。`


- 启动
```
cd /mnt/feature-dreambooth/stable-diffusion-web-ui/
source venv/bin/activate
bash /mnt/feature-dreambooth/stable-diffusion-web-ui/webui.sh --api --env=dev --xformers --listen --enable-insecure-extension-access

nohup bash webui.sh --api --xformers --listen --enable-insecure-extension-access >log/webui.log 2>&1 &

tail -f log/webui.log
```

