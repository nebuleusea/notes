# `conda remove`



从指定的 conda 环境中删除包列表。

使用--all标志删除所有包和环境本身。

此命令还将删除依赖于任何指定包的任何包——除非可以找到没有该依赖项的替换包。如果您希望跳过此依赖项检查并仅删除请求的包，请添加“--force”选项。但请注意，这可能会导致环境损坏，因此请谨慎使用。



```
usage: conda remove [-h] [-n ENVIRONMENT | -p PATH] [-c CHANNEL] [--use-local]
                    [--override-channels] [--repodata-fn REPODATA_FNS]
                    [--experimental {jlap,lock}] [--no-lock]
                    [--repodata-use-zst | --no-repodata-use-zst] [--features]
                    [--force-remove] [--no-pin] [--solver {classic}] [-C] [-k]
                    [--offline] [--json] [-v] [-q] [-d] [-y] [--all]
                    [--keep-env] [--dev]
                    [package_name ...]
```

## 位置参数

- package_name

  要从环境中删除的包名称。

## 命名参数

- --all

  删除所有包，即整个环境。

- --keep-env

  与--all一起使用，删除所有包但保留环境。

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

## 解算器模式修改器

- --features

  删除功能（而不是包）。

- --force-remove, --force

  强制删除某个包，而不删除依赖于该包的包。使用此选项通常会使您的环境处于损坏且不一致的状态。

- --no-pin

  忽略适用于当前操作的固定包。这些固定的包可能来自 .condarc 文件或 <TARGET_ENVIRONMENT>/conda-meta/pinned 中的文件。

- --solver

  可能的选择：经典选择要使用的解算器后端。

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



例子：

从当前活动环境中删除包“scipy”：

```
conda remove scipy
```

从环境“myenv”中删除包列表：

```
conda remove -n myenv scipy curl wheel
```

从环境myenv和环境本身中删除所有包：

```
conda remove -n myenv --all
```

从环境myenv中删除所有包但保留环境：

```
conda remove -n myenv --all --keep-env
```