# [`venv`](https://docs.python.org/zh-cn/3/library/venv.html#module-venv) --- 创建虚拟环境

_在 3.3 版本加入._

**源码：** [Lib/venv/](https://github.com/python/cpython/tree/3.12/Lib/venv/)

---

`venv` 模块支持创建轻量的“虚拟环境”，每个虚拟环境将拥有它们自己独立的安装在其 [`site`](https://docs.python.org/zh-cn/3/library/site.html#module-site) 目录中的 Python 软件包集合。 虚拟环境是在现有的 Python 安装版基础之上创建的，这被称为虚拟环境的“基础”Python，并且还可选择与基础环境中的软件包隔离开来，这样只有在虚拟环境中显式安装的软件包才是可用的。

当在一个虚拟环境内使用时，常见安装工具如 [pip](https://pypi.org/project/pip/) 将把 Python 软件包安装到虚拟环境中而无需显式地指明这一点。

虚拟环境是（主要的特性）：

- 用来包含支持一个项目（库或应用程序）所需的特定 Python 解释器、软件库和二进制文件。 它们在默认情况下与其他虚拟环境中的软件以及操作系统中安装的 Python 解释器和库保持隔离。
- 包含在一个目录中，根据惯例被命名为项目目录下的`venv` 或 `.venv`，或是有许多虚拟环境的容器目录下，如 `~/.virtualenvs`。
- 不可签入 Git 等源代码控制系统。
- 被视为是可丢弃性的 —— 应当能够简单地删除并从头开始重建。 你不应在虚拟环境中放置任何项目代码。
- 不被视为是可移动或可复制的 —— 你只能在目标位置重建相同的环境。

请参阅 [**PEP 405**](https://peps.python.org/pep-0405/) 了解有关 Python 虚拟环境的更多背景信息。

参见

[Python Packaging User Guide: Creating and using virtual environments](https://packaging.python.org/guides/installing-using-pip-and-virtual-environments/#create-and-use-virtual-environments)

[可用性](https://docs.python.org/zh-cn/3/library/intro.html#availability): 非 Emscripten，非 WASI。

此模块在 WebAssembly 平台 `wasm32-emscripten` 和 `wasm32-wasi` 上不适用或不可用。 请参阅 [WebAssembly 平台](https://docs.python.org/zh-cn/3/library/intro.html#wasm-availability) 了解详情。

## 创建虚拟环境

通过执行 `venv` 指令来创建一个 [虚拟环境](https://docs.python.org/zh-cn/3/library/venv.html#venv-def):

```
python -m venv /path/to/new/virtual/environment
```

运行此命令将创建目标目录（父目录若不存在也将创建），并放置一个 `pyvenv.cfg` 文件在其中，文件中有一个 `home` 键，它的值指向运行此命令的 Python 安装（目标目录的常用名称是 `.venv`）。它还会创建一个 `bin` 子目录（在 Windows 上是 `Scripts`），其中包含 Python 二进制文件的副本或符号链接（视创建环境时使用的平台或参数而定）。它还会创建一个（初始为空的） `lib/pythonX.Y/site-packages` 子目录（在 Windows 上是 `Lib\site-packages`）。如果指定了一个现有的目录，这个目录就将被重新使用。

_在 3.5 版本发生变更:_ 现在推荐使用 `venv` 来创建虚拟环境。

_自 3.6 版本弃用:_ `pyvenv` 是针对 Python 3.3 和 3.4 创建虚拟环境的推荐工具，并 [在 Python 3.6 中被弃用](https://docs.python.org/zh-cn/3/whatsnew/3.6.html#whatsnew36-venv)。

在 Windows 上，调用 `venv` 命令如下:

```
c:\>Python35\python -m venv c:\path\to\myenv
```

或者，如果已经为 [Python 安装](https://docs.python.org/zh-cn/3/using/windows.html#using-on-windows) 配置好 `PATH` 和 `PATHEXT` 变量:

```
c:\>python -m venv c:\path\to\myenv
```

本命令如果以 `-h` 参数运行，将显示可用的选项:

```
usage: venv [-h] [--system-site-packages] [--symlinks | --copies] [--clear]
            [--upgrade] [--without-pip] [--prompt PROMPT] [--upgrade-deps]
            ENV_DIR [ENV_DIR ...]

Creates virtual Python environments in one or more target directories.

positional arguments:
  ENV_DIR               A directory to create the environment in.

optional arguments:
  -h, --help            show this help message and exit
  --system-site-packages
                        Give the virtual environment access to the system
                        site-packages dir.
  --symlinks            Try to use symlinks rather than copies, when symlinks
                        are not the default for the platform.
  --copies              Try to use copies rather than symlinks, even when
                        symlinks are the default for the platform.
  --clear               Delete the contents of the environment directory if it
                        already exists, before environment creation.
  --upgrade             Upgrade the environment directory to use this version
                        of Python, assuming Python has been upgraded in-place.
  --without-pip         Skips installing or upgrading pip in the virtual
                        environment (pip is bootstrapped by default)
  --prompt PROMPT       Provides an alternative prompt prefix for this
                        environment.
  --upgrade-deps        Upgrade core dependencies (pip) to the
                        latest version in PyPI

Once an environment has been created, you may wish to activate it, e.g. by
sourcing an activate script in its bin directory.
```

_在 3.12 版本发生变更:_ `setuptools` 不再是核心的 venv 依赖项。

_在 3.9 版本发生变更:_ 添加 `--upgrade-deps` 选项，用于将 pip + setuptools 升级到 PyPI 上的最新版本

_在 3.4 版本发生变更:_ 默认安装 pip，并添加 `--without-pip` 和 `--copies` 选项

_在 3.4 版本发生变更:_ 在早期版本中，如果目标目录已存在，将引发错误，除非使用了 `--clear` 或 `--upgrade` 选项。

备注

虽然 Windows 支持符号链接，但不推荐使用它们。特别注意，在文件资源管理器中双击 `python.exe` 将立即解析符号链接，并忽略虚拟环境。

备注

在 Microsoft Windows 上，为了启用 `Activate.ps1` 脚本，可能需要修改用户的执行策略。可以运行以下 PowerShell 命令来执行此操作：

PS C:> Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser

参阅 [About Execution Policies](https://go.microsoft.com/fwlink/?LinkID=135170) 以获取更多信息。

生成的 `pyvenv.cfg` 文件还包括 `include-system-site-packages` 键，如果运行 `venv` 时带有 `--system-site-packages` 选项，则键值为 `true`，否则为 `false`。

除非采用 `--without-pip` 选项，否则将会调用 [`ensurepip`](https://docs.python.org/zh-cn/3/library/ensurepip.html#module-ensurepip) 将 `pip` 引导到虚拟环境中。

可以向 `venv` 传入多个路径，此时将根据给定的选项，在所给的每个路径上创建相同的虚拟环境。

## 虚拟环境是如何实现的

当运行虚拟环境中的 Python 解释器时，[`sys.prefix`](https://docs.python.org/zh-cn/3/library/sys.html#sys.prefix) 和 [`sys.exec_prefix`](https://docs.python.org/zh-cn/3/library/sys.html#sys.exec_prefix) 将指向该虚拟环境的相应目录，而 [`sys.base_prefix`](https://docs.python.org/zh-cn/3/library/sys.html#sys.base_prefix) 和 [`sys.base_exec_prefix`](https://docs.python.org/zh-cn/3/library/sys.html#sys.base_exec_prefix) 将指向用于创建该虚拟环境的基础 Python 的相应目录。 只需检测 `sys.prefix != sys.base_prefix` 就足以确定当前解释器是否运行于虚拟环境中。

一个虚拟环境可以通过位于其二进制文件目录目录 (在 POSIX 上为 `bin`；在 Windows 上为 `Scripts` ) 中的脚本来“激活”。 这会将该目录添加到你的 `PATH`，这样运行 **python** 时就会发起调用虚拟环境的 Python 解释器，从而可以运行该目录中安装的脚本而不必使用其完整路径。 激活脚本的发起调用方式是平台专属的 (`*<venv>*` 必须用包含虚拟环境目录的路径来替换):

| 平台       | Shell                                   | 用于激活虚拟环境的命令               |
| :--------- | :-------------------------------------- | :----------------------------------- |
| POSIX      | bash/zsh                                | `$ source *<venv>*/bin/activate`     |
| fish       | `$ source *<venv>*/bin/activate.fish`   |                                      |
| csh/tcsh   | `$ source *<venv>*/bin/activate.csh`    |                                      |
| PowerShell | `$ *<venv>*/bin/Activate.ps1`           |                                      |
| Windows    | cmd.exe                                 | `C:\> *<venv>*\Scripts\activate.bat` |
| PowerShell | `PS C:\> *<venv>*\Scripts\Activate.ps1` |                                      |

_在 3.4 版本加入:_ **fish** 和 **csh** 激活脚本。

_在 3.8 版本加入:_ 在 POSIX 上安装 PowerShell 激活脚本，以支持 PowerShell Core。

激活一个虚拟环境的操作 _不是必需的_，因为你完全可以在发起调用 Python 时指明特定虚拟环境的 Python 解释器的完整路径。 更进一步地说，安装在虚拟环境中的所有脚本也都可以在不激活该虚拟环境的情况下运行。

为了达成此目的，安装到虚拟环境中的脚本将包含一个以“井号叹号”打头的行用来指定虚拟环境的 Python 解释器，例如 `#!/*<path-to-venv>*/bin/python`。 这意味着无论 `PATH` 的值是什么该脚本都将使用指定的解释器运行。 在 Windows 上对“井号叹号”行的处理将在你安装了 [适用于Windows的Python启动器](https://docs.python.org/zh-cn/3/using/windows.html#launcher) 的情况下获得支持。 这样，在 Windows 资源管理器窗口中双击一个已安装的脚本应当会使用正确的解释器运行它而无需激活相应虚拟环境或设置 `PATH`。

当一个虚拟环境已被激活时，`VIRTUAL_ENV` 环境变量会被设为该虚拟环境的路径。 由于使用虚拟环境并不需要显式地激活它，因此 `VIRTUAL_ENV` 并不能被用来可靠地确定是否正在使用虚拟环境。

警告

因为安装在虚拟环境中的脚本不应要求必须激活该虚拟环境，所以它们的“井号叹号”行会包含虚拟环境的绝对路径。 因为这一点，所以虚拟环境在通常情况下都是不可移植的。 你应当保证提供重建一个虚拟环境的简便方式（举例来说，如果你准备了需求文件 `requirements.txt`，则可以使用虚拟环境的 `pip` 执行 `pip install -r requirements.txt` 来安装虚拟环境所需的所有软件包）。 如果出于某种原因你需要将虚拟环境移动到一个新的位置，则你应当在目标位置上重建它并删除旧位置上的虚拟环境。 如果出于某种原因你移动了一个虚拟环境的上级目录，你也应当在新位置上重建该虚拟环境。 否则，安装到该虚拟环境的软件包可能无法正常工作。

你可以通过在 shell 中输入 `deactivate` 来取消激活一个虚拟环境。 取消激活的实现机制依赖于具体平台并且属于内部实现细节（通常，将会使用一个脚本或者 shell 函数）。
