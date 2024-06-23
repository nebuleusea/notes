# `conda list`

列出 conda 环境中已安装的软件包。

```
usage: conda list [-h] [-n ENVIRONMENT | -p PATH] [--json] [-v] [-q]
                  [--show-channel-urls] [--reverse] [-c] [-f] [--explicit]
                  [--md5] [-e] [-r] [--no-pip]
                  [regex]
```

## 位置参数

- regex

  仅列出与此正则表达式匹配的包。

## 命名参数

- --show-channel-urls

  显示频道网址。覆盖conda config --show show_channel_urls给出的值。

- --reverse

  按相反顺序列出已安装的软件包。

- -c, --canonical

  仅输出包的规范名称。

- -f, --full-name

  仅搜索全名，即 ^<regex>$。 --full-name NAME 与正则表达式 '^NAME$' 相同。

- --explicit

  使用 URL 显式列出所有已安装的 conda 软件包（输出可由 conda create --file 使用）。

- --md5

  使用 --explicit 时添加 MD5 哈希和。

- -e, --export

  输出明确的、机器可读的需求字符串，而不是人类可读的包列表。此输出可由 conda create --file 使用。

- -r, --revisions

  列出修订历史记录。

- --no-pip

  不要包含仅 pip 安装的软件包。

## 目标环境规范

- -n, --name

  环境名称。

- -p, --prefix

  环境位置的完整路径（即前缀）。

## 输出、提示和流程控制选项

- --json

  将所有输出报告为 json。适合以编程方式使用 conda。

- -v, --verbose

  可以多次使用。一次用于详细输出，两次用于 INFO 日志记录，三次用于 DEBUG 日志记录，四次用于 TRACE 日志记录。

- -q, --quiet

  不显示进度条。

例子：

列出当前环境中的所有包：

```
conda list
```

以相反的顺序列出所有包：

```
conda list --reverse
```

列出安装到环境“myenv”中的所有软件包：

```
conda list -n myenv
```

使用正则表达式列出以字母“py”开头的所有包：

```
conda list ^py
```

保存包以供将来使用：

```
conda list --export > package-list.txt
```

从导出文件重新安装包：

```
conda create -n myenv --file package-list.txt
```
