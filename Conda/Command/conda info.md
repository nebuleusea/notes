# `conda info`



显示有关当前 conda 安装的信息。



```
usage: conda info [-h] [--json] [-v] [-q] [-a] [--base] [-e] [-s]
                  [--unsafe-channels]
```

## 命名参数

- -a, --all

  --all已弃用并将在 24.9 中删除。使用--verbose代替。

- --base

  显示基础环境路径。

- -e, --envs

  列出所有已知的 conda 环境。

- -s, --system

  列出环境变量。

- --unsafe-channels

  显示公开令牌的通道列表。

## 输出、提示和流程控制选项

- --json

  将所有输出报告为 json。适合以编程方式使用 conda。

- -v, --verbose

  可以多次使用。一次用于详细输出，两次用于 INFO 日志记录，三次用于 DEBUG 日志记录，四次用于 TRACE 日志记录。

- -q, --quiet

  不显示进度条。