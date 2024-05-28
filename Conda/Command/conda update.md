# `conda update`



将 conda 软件包更新到最新的兼容版本。

此命令接受包名称列表，并将它们更新为与环境中所有其他包兼容的最新版本。

Conda 尝试安装所请求软件包的最新版本。为此，它可能会更新一些已安装的软件包，或安装其他软件包。要防止更新现有包，请使用 --no-update-deps 选项。这可能会强制 conda 安装所请求包的旧版本，并且不会阻止安装其他依赖项包。



```
usage: conda update [-h] [-n ENVIRONMENT | -p PATH] [-c CHANNEL] [--use-local]
                    [--override-channels] [--repodata-fn REPODATA_FNS]
                    [--experimental {jlap,lock}] [--no-lock]
                    [--repodata-use-zst | --no-repodata-use-zst]
                    [--strict-channel-priority] [--no-channel-priority]
                    [--no-deps | --only-deps] [--no-pin] [--copy]
                    [--no-shortcuts] [--shortcuts-only SHORTCUTS_ONLY] [-C]
                    [-k] [--offline] [--json] [-v] [-q] [-d] [-y]
                    [--download-only] [--show-channel-urls] [--file FILE]
                    [--solver {classic}] [--force-reinstall]
                    [--freeze-installed | --update-deps | -S | --update-all | --update-specs]
                    [--clobber]
                    [package_spec ...]
```

## 位置参数

- package_spec

  要在 conda 环境中安装或更新的软件包列表。

## 命名参数

- --file

  从给定文件中读取包版本。可以传递重复的文件规范（例如--file=file1 --file=file2）。

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

- --solver

  可能的选择：经典选择要使用的解算器后端。

- --force-reinstall

  确保卸载并重新安装当前操作的任何用户请求的包，即使该包已存在于环境中。

- --freeze-installed, --no-update-deps

  不要更新或更改已安装的依赖项。

- --update-deps

  更新具有可用更新的依赖项。

- -S, --satisfied-skip-solve

  如果满足要求的规格，请尽早退出并且不要运行求解器。还跳过“aggressive_update_packages”配置设置配置的激进更新。使用“conda info --describeaggressive_update_packages”查看您的设置。 --satisfied-skip-solve 类似于“pip install”的默认行为。

- --update-all, --all

  更新环境中所有已安装的软件包。

- --update-specs

  根据提供的规格进行更新。

## 包链接和安装时选项

- --copy

  使用副本而不是硬链接或软链接安装所有软件包。

- --no-shortcuts

  不要安装开始菜单快捷方式

- --shortcuts-only

  仅安装此包名称的快捷方式。可以多次使用。

- --clobber

  允许破坏包内的重叠文件路径，并抑制相关警告。

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

> conda update -n myenv scipy