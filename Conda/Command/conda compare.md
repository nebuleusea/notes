# `conda compare`



比较 conda 环境之间的包。



```
usage: conda compare [-h] [--json] [-v] [-q] [-n ENVIRONMENT | -p PATH] file
```

## 位置参数

- file

  要进行比较的环境文件的路径。

## 输出、提示和流程控制选项

- --json

  将所有输出报告为 json。适合以编程方式使用 conda。

- -v, --verbose

  可以多次使用。一次用于详细输出，两次用于 INFO 日志记录，三次用于 DEBUG 日志记录，四次用于 TRACE 日志记录。

- -q, --quiet

  不显示进度条。

## 目标环境规范

- -n, --name

  环境名称。

- -p, --prefix

  环境位置的完整路径（即前缀）。



例子：

将当前环境中的包与当前工作目录中的“environment.yml”进行比较：

```
conda compare environment.yml
```

将安装到环境“myenv”中的软件包与不同目录中的“environment.yml”进行比较：

```
conda compare -n myenv path/to/file/environment.yml
```