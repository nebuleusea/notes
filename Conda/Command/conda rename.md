# `conda rename`

重命名现有环境。

此命令通过名称 (-n/--name) 或其前缀 (-p/--prefix) 重命名 conda 环境。

基础环境和当前活动环境无法重命名。

```
usage: conda rename [-h] [-n ENVIRONMENT | -p PATH] [--force] [-d] destination
```

## 位置参数

- destination

  conda 环境的新名称。

## 命名参数

- --force

  强制重命名环境。

- -d, --dry-run

  仅显示当前命令、参数和其他标志将执行的操作。

## 目标环境规范

- -n, --name

  环境名称。

- -p, --prefix

  环境位置的完整路径（即前缀）。

例子：

```
conda rename -n test123 test321

conda rename --name test123 test321

conda rename -p path/to/test123 test321

conda rename --prefix path/to/test123 test321
```
