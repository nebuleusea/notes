# `conda clean`

删除未使用的包和缓存。

```
usage: conda clean [-h] [-a] [-i] [-p] [-t] [-f] [-c [TEMPFILES ...]] [-l]
                   [--json] [-v] [-q] [-d] [-y]
```

## 移除目标

- -a, --all

  删除索引缓存、锁定文件、未使用的缓存包、tarball 和日志文件。

- -i, --index-cache

  删除索引缓存。

- -p, --packages

  从可写包缓存中删除未使用的包。警告：这不会检查使用符号链接安装回包缓存的包。

- -t, --tarballs

  删除缓存的包 tarball。

- -f, --force-pkgs-dirs

  删除*所有*可写的包缓存。该选项不包含在 --all 标志中。警告：这将破坏使用符号链接安装到包缓存的包的环境。

- -c, --tempfiles

  删除因正在使用而无法删除的临时文件。 --tempfiles 标志的参数是应找到并删除临时文件的环境的路径（或路径列表）。

- -l, --logfiles

  删除日志文件。

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

```
conda clean --tarballs
```
