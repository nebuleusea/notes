# `conda config`

修改 .condarc 中的配置值。

这是根据 git config 命令建模的。默认情况下写入用户 .condarc 文件 (/home/docs/.condarc)。使用 --show-sources 标志显示计算机上所有已识别的配置位置。

```
usage: conda config [-h] [--json] [-v] [-q] [--system | --env | --file FILE]
                    [--show [SHOW ...] | --show-sources | --validate |
                    --describe [DESCRIBE ...] | --write-default]
                    [--get [KEY ...] | --append KEY VALUE | --prepend KEY
                    VALUE | --set KEY VALUE | --remove KEY VALUE |
                    --remove-key KEY | --stdin]
```

## 输出、提示和流程控制选项

- --json

  将所有输出报告为 json。适合以编程方式使用 conda。

- -v, --verbose

  可以多次使用。一次用于详细输出，两次用于 INFO 日志记录，三次用于 DEBUG 日志记录，四次用于 TRACE 日志记录。

- -q, --quiet

  不显示进度条。

## 配置文件位置选择

如果没有这些标志之一，则使用“/home/docs/.condarc”中的用户配置文件。

- --system

  写入位于“/home/docs/checkouts/readthedocs.org/user_builds/continuumio-conda/envs/latest/.condarc”的系统 .condarc 文件。

- --env

  写入活动 conda 环境 .condarc 文件（<无活动环境>）。如果没有活动环境，请写入用户配置文件 (/home/docs/.condarc)。

- --file

  写入给定文件。

## 配置子命令

- --show

  显示计算和编译的配置值。如果未给出参数，则显示所有配置值的信息。

- --show-sources

  显示所有已识别的配置源。

- --validate

  验证所有配置源。迭代所有 .condarc 文件并检查解析错误。

- --describe

  描述给定的配置参数。如果未给出参数，则显示所有配置参数的信息。

- --write-default

  将默认配置写入文件。相当于conda config --describe > ~/.condarc。

## 配置修饰符

- --get

  获取配置值。

- --append

  将一个配置值添加到列表键的末尾。

- --prepend, --add

  将一个配置值添加到列表键的开头。

- --set

  设置布尔值或字符串键。

- --remove

  从列表键中删除配置值。这将删除该值的所有实例。

- --remove-key

  删除配置键（及其所有值）。

- --stdin

  应用通过 stdin 传输的 yaml 格式给出的配置信息。

有关 .condarc 中所有选项的详细信息，请参阅conda config --describe或[https://conda.io/docs/config.html 。](https://conda.io/docs/config.html)

例子：

显示计算和编译的所有配置值：

```
conda config --show
```

显示所有已识别的配置源：

```
conda config --show-sources
```

将所有可用配置选项的描述打印到命令行：

```
conda config --describe
```

将“channel_priority”配置选项的描述打印到命令行：

```
conda config --describe channel_priority
```

添加 conda-canary 通道：

```
conda config --add channels conda-canary
```

将当前激活环境的输出详细程度设置为级别 3（最高）：

```
conda config --set verbosity 3 --env
```

将“conda-forge”通道添加为“默认”通道的备份：

```
conda config --append channels conda-forge
```

用于设置conda配置文件中的show_channel_urls选项,设置为yes，则在运行conda install等命令时，将显示正在使用的通道的URL

```
conda config --set show_channel_urls yes
```
