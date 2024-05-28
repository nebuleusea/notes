# `conda init`



初始化 conda 以进行 shell 交互。



```
usage: conda init [-h] [--all] [--user] [--no-user] [--system] [--reverse]
                  [--json] [-v] [-q] [-d]
                  [SHELLS ...]
```

## 位置参数

- SHELLS

  可能的选择：bash、fish、tcsh、xonsh、zsh、powershell要初始化的一个或多个 shell。如果未给出，则默认值为 'bash'（在 unix 上）和 'cmd.exe' & 'powershell'（在 Windows 上）。使用“--all”标志来初始化所有 shell。可用的 shell: ['bash', 'fish', 'powershell', 'tcsh', 'xonsh', 'zsh']

## 命名参数

- --all

  初始化所有当前可用的 shell。

- -d, --dry-run

  只显示本来会做的事情。

## 设置类型

- --user

  为当前用户初始化 conda（默认）。

- --no-user

  不要为当前用户初始化 conda。

- --system

  为系统上的所有用户初始化 conda。

- --reverse

  撤消最后一个 conda init 的效果。

## 输出、提示和流程控制选项

- --json

  将所有输出报告为 json。适合以编程方式使用 conda。

- -v, --verbose

  可以多次使用。一次用于详细输出，两次用于 INFO 日志记录，三次用于 DEBUG 日志记录，四次用于 TRACE 日志记录。

- -q, --quiet

  不显示进度条。



conda 功能的关键部分要求它直接与调用 conda 的 shell 交互。 conda activate和conda deactivate命令是 shell 级命令。也就是说，它们影响正在交互的 shell 上下文的状态（例如环境变量）。其他核心命令（例如 conda create和conda install）也必须与 shell 环境交互。因此，它们以特定于每个 shell 的方式实现。必须配置每个 shell 才能使用它们。

此命令对您的系统进行特定的更改，并针对每个 shell 进行自定义。要查看系统上之前受影响的特定文件和位置，请使用“--dry-run”标志。要查看每个位置正在或将要进行的确切更改，请使用“--verbose”标志。

重要提示：运行conda init后，大多数 shell 需要关闭并重新启动才能使更改生效。