# 安装Miniconda

[Miniconda下载官网](https://docs.conda.io/en/latest/miniconda.html)

### 下载Miniconda到linux

```shell
# wget + url，中间可以加+c参数，断点续传
wget https://
```

### 安装

```shell
# bash命令安装，注意路径
bash packagename.sh
```

1. ![用bash激活安装](https://upload-images.jianshu.io/upload_images/17511166-8ba039ba64f6c9e6.png?imageMogr2/auto-orient/strip|imageView2/2/w/1170/format/webp)

2. **看到`more`就是按空格键翻页查看协议**，按**`q`**退出

   ![在服务器安装软件查看协议](https://upload-images.jianshu.io/upload_images/17511166-fba862b4afb1eca4.png?imageMogr2/auto-orient/strip|imageView2/2/w/1084/format/webp)

3. **接受协议**，输入**`yes`**

   ![接受安装协议](https://upload-images.jianshu.io/upload_images/17511166-b67ec88c87e3fcdb.png?imageMogr2/auto-orient/strip|imageView2/2/w/922/format/webp)

4. **默认安装路径**，按**`enter`**

   ![安装软件默认安装路径](https://upload-images.jianshu.io/upload_images/17511166-5751b4bcd31aa049.png?imageMogr2/auto-orient/strip|imageView2/2/w/1070/format/webp)

5. **会询问是否需要初始化**，输入**`yes`**

   ![安装需要初始化](https://upload-images.jianshu.io/upload_images/17511166-ba5dd6ada4f5ca0a.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

6. **显示安装已完成的提示信息**

   ![安装已经完成的提示](https://upload-images.jianshu.io/upload_images/17511166-3850226e6eae2f6a.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

### **激活刚安装完成的软件**

一般安装软件完成后需要重启，在Linux叫激活，有两种方式，**第一种**是重新登录服务器，**第二种**是输入以下命令：

```shell
# 常用
source ~/.bashrc
```

# Conda

### 查看conda版本

```
# -v = --version
conda -v
```

### 显示所有的环境

```
conda env list
# -e = --envs
conda info -e
```

### 显示当前安装的conda信息

```
conda info
```

### 配置镜像

```
# 查看频道
conda config --show channels
# 添加频道
conda config --add channels <channel_name>
conda config --add channels https://mirrors.bfsu.edu.cn/anaconda/pkgs/main/
conda config --add channels https://mirrors.bfsu.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.bfsu.edu.cn/anaconda/cloud/conda-forge/
conda config --add coannels https://mirrors.bfsu.edu.cn/anaconda/cloud/bioconda/
# 删除频道
conda config --remove channels <channel_name>
# 用于设置conda配置文件中的show_channel_urls选项,设置为yes，则在运行conda install等命令时，将显示正在使用的通道的URL
conda config --set show_channel_urls yes
```

### 创建新环境

```
# -n = --name
conda create -n <env_name> <python_version> <python_package>
conda create -n your_env_name python=3.7 pandas scipy
conda create -n new_env_name --clone old_env_name
```

### 删除环境

```
conda env remove -n <env_name>
```

### 激活环境

```
conda activate <env_name>
# 激活默认环境
conda activate
```

### 退出环境

```
# 在env环境中退出会切换到默认环境
conda deactivate
```

### 更新conda

```
# 需要在默认环境中
conda update conda
```

### 更新包

```
# 更新当前环境所有包
conda update --all
# 更新指定包
conda update <package_name>
```

### 查看所有已经安装的包

```
conda list
```

### 安装包

```
# 在当前环境中安装包
conda install <package_name>
# 在指定环境中安装包,-n = --name
conda install -n <env_name> <package_name>
```

### 删除包

```
# 删除当前环境中的包
conda remove <package_name>
# 删除指定环境中的包,-n = --name
conda remove -n <env_name> <package_name>
```
