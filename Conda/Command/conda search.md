# `conda search`



使用 MatchSpec 格式搜索包并显示关联信息。

MatchSpec 是 conda 包的查询语言。



```
usage: conda search [-h] [--envs] [-i] [--subdir SUBDIR]
                    [--skip-flexible-search] [-c CHANNEL] [--use-local]
                    [--override-channels] [--repodata-fn REPODATA_FNS]
                    [--experimental {jlap,lock}] [--no-lock]
                    [--repodata-use-zst | --no-repodata-use-zst] [-C] [-k]
                    [--offline] [--json] [-v] [-q]
```

## 命名参数

- --envs

  搜索当前用户的所有环境。如果以管理员身份（在 Windows 上）或 UID 0（在 UNIX 上）运行，则搜索系统上的所有已知环境。

- -i, --info

  提供有关每个包的详细信息。

- --subdir, --platform

  搜索给定的子目录。格式应类似于“osx-64”、“linux-32”、“win-64”等。默认是搜索当前平台。

- --skip-flexible-search

  如果初始搜索失败，请勿执行灵活搜索。

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



例子：

搜索名为“scikit-learn”的特定包：

```
conda search scikit-learn
```

搜索包名中包含“scikit”的包：

```
conda search *scikit*
```

请注意，您的 shell 可能会在将命令交给 conda 之前扩展“*”。因此，有时需要在查询周围使用单引号或双引号：

```
conda search '*scikit'
conda search "*scikit*"
```

搜索 64 位 Linux 的软件包（默认情况下，显示适用于您当前平台的软件包）：

```
conda search numpy[subdir=linux-64]
```

搜索特定版本的包：

```
conda search 'numpy>=1.12'
```

在特定频道上搜索包：

```
conda search conda-forge::numpy
conda search 'numpy[channel=conda-forge, subdir=osx-64]'
```