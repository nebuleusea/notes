## 1.配置中文环境

### 1.打开配置

```shell
sudo dpkg-reconfigure locales
```

### 2.选择zh_CN.UTF-8作为默认语言

### 3.修改locale文件

```shell
vim /etc/default/locale
```

```
LANG=zh_CN.UTF-8
LANGUAGE=zh_CN.UTF-8
```

### 4.重启终端

### 环境变量

```shell
export $PATH
```

- 查找pg_hba.conf文件的路径

```shell
(sudo) find / -name pg_hba.conf
```

- 修改用户密码

```shell
(sudo) passwd username
```

# 目录改回英文

### 方法一

```shell
export LANG=en_US
sudo pacman -S xdg-user-dirs-gtk
xdg-user-dirs-gtk-update
export LANG=zh_CH
```

方法二

```shell
sudo vim ~/.config/user-dirs.dirs
XDG_DESKTOP_DIR="$HOME/Desktop"
XDG_DOWNLOAD_DIR="$HOME/Downloads"
XDG_TEMPLATES_DIR="$HOME/Templates"
XDG_PUBLICSHARE_DIR="$HOME/Public"
XDG_DOCUMENTS_DIR="$HOME/Documents"
XDG_MUSIC_DIR="$HOME/Music"
XDG_PICTURES_DIR="$HOME/Pictures"
XDG_VIDEOS_DIR="$HOME/Videos"
```

```
ssh-keygen -t rsa -C 邮箱
# 3次回车
cd .ssh
cat id_rsa_pub
```

```shell
 ssh-keygen -R <url>
```
