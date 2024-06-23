# `conda notices`

检索最新的频道通知。

Conda 通道维护人员可以选择设置用户间歇性看到的消息。其中一些通知是信息性的，而另一些则是有关通道稳定性的消息。

```
usage: conda notices [-h] [-c CHANNEL] [--use-local] [--override-channels]
                     [--repodata-fn REPODATA_FNS] [--experimental {jlap,lock}]
                     [--no-lock] [--repodata-use-zst | --no-repodata-use-zst]
                     [--json] [-v] [-q]
```

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

## 输出、提示和流程控制选项

- --json

  将所有输出报告为 json。适合以编程方式使用 conda。

- -v, --verbose

  可以多次使用。一次用于详细输出，两次用于 INFO 日志记录，三次用于 DEBUG 日志记录，四次用于 TRACE 日志记录。

- -q, --quiet

  不显示进度条。

例子：

```
conda notices

conda notices -c defaults
```
