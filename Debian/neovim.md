> 前言

Neovim 在我看来是非常有前途、好用的工具，毕竟传统的 vim、vi 放到现在着实显老旧了些。所以，我在这篇文章中记录一下我的 Nvim（Neovim）的源码编译、安装过程。为什么用源码编译安装呢？这是因为使用 Linux 系统自带的包管理器下载的 Nvim 版本我觉得实在是太老了，和 Nvim 的官方发布版本相差太大。我也不想折腾什么包管理器，使用 Github 下载源码编译安装来的最直接。

> 1. 源码获取

1. 使用 Git 拉取仓库

使用 SSH 拉取仓库（需要配置 SSH）
```
git clone --depth=1 git@github.com:neovim/neovim.git
```
当然可以指定版本，但是就不能用 --depth 选项了，下面我指定了 0.9 版本进行拉取
```
git clone -b release-0.9 https://github.com/neovim/neovim.git
```

2. 去 Nvim 的 Release 页下载

[Releases · neovim/neovim · GitHub](https://hotakus.tech/blog/go.html?u=aHR0cHM6Ly9naXRodWIuY29tL25lb3ZpbS9uZW92aW0vcmVsZWFzZXM=)



> 2. 开始编译

 开始编译前，请确保安装了必备的工具链，apt 包管理器和 yum 包管理器的安装是不同的，一般来说最知名的 Ubuntu 和 Centos 就分别使用的这两种包管理器。

1. 安装工具链

Apt 包管理器：
```
sudo apt install build-essential make cmake
```

2. 编译

进入到刚刚拉取的仓库里 `cd neovim`

```
make CMAKE_BUILD_TYPE=RelWithDebInfo -j4
```

    1. 默认安装路径
    
    它有一个默认安装路径 /usr/local/bin/，当然，你可以更改这路径，在上面这条命令后加一条 CMAKE_INSTALL_PREFIX=path 参数，path 是你要安装的路径，如果不是默认的路径，你需要在 ~/.bashrc 文件里 export 你的 path 然后 source 你的 ~/.bashrc。
    
    2. 默认参数
    
    可以看到，官方的默认参数是build参数是RelWithDebInfo，我为了加快make进程，使用了-j4参数，对于Intel的CPU一般这个参数是你核心数的两倍，比如你2核心，那么可以设置成4，12核心则可设置为24
    
    3. 可能出现的问题
    
    make 过程中会自动下载很多的依赖包， 一般网络良好即可，若出现超时之类的报错，那么重新执行上面的 make 即可，若是网络较差，就需要非常多次的执行 make 命令，就比较蛋疼。总之，超时错误不用担心。
    
    如果是缺什么软件或库比如 Python 的话，就安装后再 make 就行了，我推荐在 conda 环境下 make。
    
    4. 编译完成
    
    编译完成后，使用 sudo 执行👇
    
    ```
    sudo make install
    ```
    这会将你的 nvim 安装到你上面指定的路径。
