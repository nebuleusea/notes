# Neovim

`GitHub`项目地址[Neovim](https://github.com/neovim/neovim)

```shell
curl -LO https://github.com/neovim/neovim/releases/latest/download/nvim.appimage
chmod u+x nvim.appimage
./nvim.appimage
mv nvim.appimage /usr/local/bin/nvim
```

# Lazygit

`GitHub`项目地址[Lazygit](https://github.com/jesseduffield/lazygit)

[Releases地址](https://github.com/jesseduffield/lazygit/releases)



# Zsh

```shell
# Debian/Ubuntu
sudo apt-get install zsh
# Centos/Redhat
sudo yum install zsh
```

## Oh My Zsh

   [Oh My Zsh GitHub](https://github.com/ohmyzsh/ohmyzsh)

   - **Oh My Zsh插件**
     - 官方 [Oh My Zsh plugins GitHub](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins)
       - dirhistory ALT搭配左右方向键后退前进cd过的路径
       - copybuffer CTRL+O复制当前命令行命令到剪切板
       - copyfile 复制文件内容到剪切板
       - copypath 复制当前路径到剪切板
       - web-search 在命令行输入问题，快速在浏览器搜索
       - z 类似于模糊搜索目录，快速跳转到之前cd过的目录，不需要一层一层cd
     - 第三方
       - zsh-autosuggestions 命令提示 [zsh-autosuggestions GitHub](https://github.com/zsh-users/zsh-autosuggestions)
       - zsh-syntax-highlighting 命令高亮提示 [zsh-syntax-highlighting GitHub](https://github.com/zsh-users/zsh-syntax-highlighting)

   - **Powerlevel10k主题**
     [Powerlevel10k GitHub](https://github.com/romkatv/powerlevel10k)
     1. Clone the repository:
     ```shell
     git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
     ```
       Users in China can use the official mirror on gitee.com for faster download.
     ```shell
     git clone --depth=1 https://gitee.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
     ```
   - Set **ZSH_THEME="powerlevel10k/powerlevel10k"** in **~/.zshrc**.

     - Nerd Fonts字体
       [Nerd Fonts GitHub](https://github.com/ryanoasis/nerd-fonts)
       If you do want to clone the entire repo be sure to shallow clone:
       
       ```shell
       git clone --depth 1 <url>
       ./install.sh
       ```
