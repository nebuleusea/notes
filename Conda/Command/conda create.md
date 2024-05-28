# `conda create`



从指定包列表创建新的 conda 环境。

要使用新创建的环境，请使用“conda activate envname”。此命令需要 -n NAME 或 -p PREFIX 选项。



```
usage: conda create [-h] [--clone ENV] (-n ENVIRONMENT | -p PATH) [-c CHANNEL]
                    [--use-local] [--override-channels]
                    [--repodata-fn REPODATA_FNS] [--experimental {jlap,lock}]
                    [--no-lock] [--repodata-use-zst | --no-repodata-use-zst]
                    [--strict-channel-priority] [--no-channel-priority]
                    [--no-deps | --only-deps] [--no-pin] [--copy]
                    [--no-shortcuts] [--shortcuts-only SHORTCUTS_ONLY] [-C]
                    [-k] [--offline] [--json] [-v] [-q] [-d] [-y]
                    [--download-only] [--show-channel-urls] [--file FILE]
                    [--no-default-packages] [--subdir SUBDIR]
                    [--solver {classic}] [-m] [--dev]
                    [package_spec ...]
```

## 位置参数

- package_spec

  要在 conda 环境中安装或更新的软件包列表。

## 命名参数

- --clone

  创建一个新环境作为现有本地环境的副本。

- --file

  从给定文件中读取包版本。可以传递重复的文件规范（例如--file=file1 --file=file2）。

- -m, --mkdir

  --mkdir正在等待弃用，并将在 25.3 中删除。多余的论证。

- --dev

  在包装器脚本中使用sys.executable -m conda而不是 CONDA_EXE。这主要用于测试期间，我们针对旧的 Python 版本测试新的 conda 源。

## 目标环境规范

- -n, --name

  环境名称。

- -p, --prefix

  环境位置的完整路径（即前缀）。

## 频道定制

- -c, --channel

  搜索包的附加渠道。这些是按照给定顺序搜索的 URL（包括使用“ [file://](file:///) ”语法的本地目录或简单的路径，如“/home/conda/mychan”或“../mychan”）。然后，搜索 .condarc 中的默认值或通道（除非给出 --override-channels）。您可以使用“defaults”来获取 conda 的默认包。您还可以使用任何名称，并且 .condarc channel_alias 值将被添加到前面。默认的channel_alias是https://conda.anaconda.org/。

- --use-local

  使用本地构建的包。与“-c local”相同。

- --override-channels

  不搜索默认或 .condarc 频道。需要--频道。

- --repodata-fn

  指定配置通道的远程服务器上或本地备份中的 repodata 的文件名。 Conda 将尝试您指定的任何内容，但如果您的规格不满足您在此处指定的内容，最终将回退到 repodata.json。这用于采用较小且时间范围缩小的 repodata。您可以多次传递此标志。首先尝试最左边的条目，然后自动为您添加后备到 repodata.json。有关详细信息，请参阅 conda config --describe repodata_fns。

- --experimental

  可能的选择：jlap、lockjlap：从repodata.jlap下载增量包索引数据；隐含“锁”的意思。 lock：读取、更新索引（repodata.json）缓存时使用锁定。现已启用。

- --no-lock

  读取、更新索引 (repodata.json) 缓存时禁用锁定。

- --repodata-use-zst, --no-repodata-use-zst

  检查/不检查 repodata.json.zst。默认启用。 （默认值：空）

- --subdir, --platform

  可能的选择：emscripten-wasm32、wasi-wasm32、freebsd-64、linux-32、linux-64、linux-aarch64、linux-armv6l、linux-armv7l、linux-ppc64、linux-ppc64le、linux-riscv64、linux-s390x 、 osx-64、osx-arm64、win-32、win-64、win-arm64、zos-z使用为此平台构建的包。新环境将配置为记住此选择。格式应类似于“osx-64”、“linux-32”、“win-64”等。默认为当前（本机）平台。

## 解算器模式修改器

- --strict-channel-priority

  如果具有相同名称的包出现在较高优先级通道中，则不考虑较低优先级通道中的包。

- --no-channel-priority

  包版本优先于通道优先级。覆盖conda config --show channel_priority给出的值。

- --no-deps

  请勿安装、更新、删除或更改依赖项。这将导致环境破坏和行为不一致。使用风险自负。

- --only-deps

  仅安装依赖项。

- --no-pin

  忽略固定文件。

- --no-default-packages

  忽略 .condarc 文件中的 create_default_packages。

- --solver

  可能的选择：经典选择要使用的解算器后端。

## 包链接和安装时选项

- --copy

  使用副本而不是硬链接或软链接安装所有软件包。

- --no-shortcuts

  不要安装开始菜单快捷方式

- --shortcuts-only

  仅安装此包名称的快捷方式。可以多次使用。

## 网络选项

- -C, --use-index-cache

  使用通道索引文件的缓存，即使它已过期。如果您不希望 conda 检查是否存在新版本的 repodata 文件，这非常有用，这将节省带宽。

- -k, --insecure

  允许 conda 执行“不安全”的 SSL 连接和传输。相当于将“ssl_verify”设置为“false”。

- --offline

  离线模式。不要连接到互联网。

## 输出、提示和流程控制选项

- --json

  将所有输出报告为 json。适合以编程方式使用 conda。

- -v, --verbose

  可以多次使用。一次用于详细输出，两次用于 INFO 日志记录，三次用于 DEBUG 日志记录，四次用于 TRACE 日志记录。

- -q, --quiet

  不显示进度条。

- -d, --dry-run

  只显示本来会做的事情。

- -y, --yes

  自动将任何确认值设置为“是”。不会要求用户确认任何添加、删除、备份等操作。

- --download-only

  解决环境并确保填充包缓存，但在取消链接包并将其链接到前缀之前退出。

- --show-channel-urls

  显示频道网址。覆盖conda config --show show_channel_urls给出的值。



例子：

创建包含“sqlite”包的环境：

```
conda create -n myenv sqlite
```

创建一个环境 (env2) 作为现有环境 (env1) 的克隆：

```
conda create -n env2 --clone path/to/file/env1
```