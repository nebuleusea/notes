## virtualenv

[virtualenv](http://www.virtualenv.org/) 是目前最流行的 python 虚拟环境配置工具。它不仅同时支持 python2 和 python3，而且可以为每个虚拟环境指定 python 解释器，并选择不继承基础版本的包。

## virtualenvwrapper

顾名思义，[virtualenvwrapper](https://bitbucket.org/dhellmann/virtualenvwrapper) 是对 [virtualenv](http://www.virtualenv.org/) 的一个封装，目的是使后者更好用。

关于为什么使用 shell 脚本开发，作者专门 [进行了解释](http://virtualenvwrapper.readthedocs.org/en/latest/design.html) 。

virtualenvwrapper 还有针对 vim 用户和 emacs 用户的 [扩展](http://virtualenvwrapper.readthedocs.org/en/latest/extensions.html) 。

virtualenvwrapper 能支持 `bash/ksh/zsh` ，所以我们可以看出，它不支持 Windows。

## virtualenvwrapper-win

由于 virtualenvwrapper 基于 shell 开发，因此不能在 Windows 系统上使用。但我们可以使用针对 Windows batch shell 的 [virtualenvwrapper-win](https://pypi.python.org/pypi/virtualenvwrapper-win)。

## venv

Python 从3.3 版本开始，自带了一个虚拟环境 [venv](https://docs.python.org/3/library/venv.html)，在 [PEP-405](http://legacy.python.org/dev/peps/pep-0405/) 中可以看到它的详细介绍。它的很多操作都和 virtualenv 类似。

因为是从 3.3 版本开始自带的，这个工具也仅仅支持 python 3.3 和以后版本。所以，要在 python2 上使用虚拟环境，依然要利用 [virtualenv](http://www.virtualenv.org/) 。

在 \*nix 系统上，可以直接执行 `pyvenv /path/to/new/virtual/enviorment` 来创建一个虚拟环境，在 Windows 系统上，则可以使用 `python -m venv myenv` 来创建。

**2015-04-18 更新：**

pyvenv 3.4 在 Ubuntu 14.04 下有 bug，如下：

```python
1pyvenv ➤ python3 -m venv blog
2Error: Command '['/home/zrong/pyvenv/blog/bin/python3', '-Im', 'ensurepip', '--upgrade', '--default-pip']' returned non-zero exit status 1
3pyvenv ➤ pyvenv-3.4 --without-pip blog
```

解决方法是创建一个不含 pip 的虚拟环境，然后手动安装 pip ：

```python
1pyvenv-3.4 --without-pip venvdir
2source venvdir/bin/activate
3curl https://bootstrap.pypa.io/get-pip.py | python
4source venvdir/bin/activate
```

参见：

- [pyvenv-3.4 error: returned non-zero exit status 1](http://askubuntu.com/questions/488529/pyvenv-3-4-error-returned-non-zero-exit-status-1)
- [pyvenv-3.4 returned non-zero exit status 1](http://stackoverflow.com/questions/24123150/pyvenv-3-4-returned-non-zero-exit-status-1)

## pyenv

我们可以用许多方法让不同的 Python 版本在系统上共存。

例如在 OS X 上，如果使用官方提供的 DMG 版本安装，那么自带的 python2 和新安装的 python3 是可以共存的。python3 可以使用 `python3` 来调用，甚至 `pip` 都可以使用 `pip3` 来调用。

但如果还有其它小版本需要共存么？我要记忆多少命令呢？

[pyenv](https://github.com/yyuu/pyenv) 用来解决这类问题。它可以安装、卸载、编译、管理多个 python 版本，并随时将其中一个设置为工作环境。

pyenv 不支持 Windows 系统。

## pywin

Windows 上有一个 pyenv 的替代品，是 [pywin](https://github.com/davidmarble/pywin) 。它用来在多个安装的 Python 版本之间进行切换，也支持 [MSYS/MINGW32](https://blog.zengrong.net/post/cygwin_and_mingw/) 。

## Python Launcher for Windows

Python 从3.3版本开始（又是3.3？），在 Windows 系统中自带了一个 [py.exe](https://docs.python.org/3/using/windows.html#launcher) 启动工具。如果你是使用 Python.org 官网下载的安装包安装的 Python 3.3（或更新版本）环境，那么可以直接在命令提示符中使用这个工具。

`py` 可以打开默认的 python 提示符； `py -2.7` 和 `py -3` 打开对应的 Python 版本。

上面介绍的工具中，前四个是虚拟环境切换工具，后三个是 Python 版本环境切换工具。将这两套工具结合使用，可以完美解决 python 多版本环境的问题。
