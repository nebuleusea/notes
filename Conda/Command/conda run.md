# `conda run`

在 conda 环境中运行可执行文件。

```
usage: conda run [-h] [-n ENVIRONMENT | -p PATH] [-v] [--dev]
                 [--debug-wrapper-scripts] [--cwd CWD] [--no-capture-output]
                 ...
```

## 位置参数

- executable_call

  可执行文件名称，以及在调用时传递给可执行文件的附加参数。

## 命名参数

- -v, --verbose

  可以多次使用。一次用于详细输出，两次用于 INFO 日志记录，三次用于 DEBUG 日志记录，四次用于 TRACE 日志记录。

- --dev

  将CONDA_EXE设置为python -m conda，假设当前工作目录包含 conda 开发源的根目录。这主要用于测试期间，我们针对旧的 Python 版本测试新的 conda 源。

- --debug-wrapper-scripts

  设置后，在实现时，shell 包装脚本将使用 echo 命令将调试信息打印到 stderr（标准错误）。

- --cwd

  运行命令的当前工作目录。如果未指定目录，则默认为用户的当前工作目录。

- --no-capture-output, --live-stream

  不捕获 stdout/stderr（标准输出/标准错误）。

## 目标环境规范

- -n, --name

  环境名称。

- -p, --prefix

  环境位置的完整路径（即前缀）。

例子：

```
$ conda create -y -n my-python-env python=3
$ conda run -n my-python-env python --version
```
