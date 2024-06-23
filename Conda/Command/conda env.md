# `conda env`

```
usage: conda env [-h] command ...
```

1. **位置参数**

   - command

     可能的选择：配置、创建、导出、列表、删除、更新

## `conda env config`

配置conda环境。

```
usage: conda env config [-h] {vars} ...
```

例子：

```
conda env config vars list
conda env config --append channels conda-forge
```

### `conda env config vars`

与与 Conda 环境关联的环境变量进行交互。

```
usage: conda env config vars [-h] {list,set,unset} ...
```

例子：

```
conda env config vars list -n my_env
conda env config vars set MY_VAR=something OTHER_THING=ohhhhya
conda env config vars unset MY_VAR
```

#### `conda env config vars list`

列出 conda 环境的环境变量。

```
usage: conda env config vars list [-h] [-n ENVIRONMENT | -p PATH] [--json]
                                  [-v] [-q]
```

1. **目标环境规范**

   - -n, --name

     环境名称。

   - -p, --prefix

     环境位置的完整路径（即前缀）。

2. **输出、提示和流程控制选项**

   - --json

     将所有输出报告为 json。适合以编程方式使用 conda。

   - -v, --verbose

     可以多次使用。一次用于详细输出，两次用于 INFO 日志记录，三次用于 DEBUG 日志记录，四次用于 TRACE 日志记录。

   - -q, --quiet

     不显示进度条。

例子：

```
conda env config vars list -n my_env
```

#### `conda env config vars set`

为 conda 环境设置环境变量。

```
usage: conda env config vars set [-h] [-n ENVIRONMENT | -p PATH] [vars ...]
```

1. **位置参数**

   - vars

     以 <KEY>=<VALUE> 形式设置的环境变量，以空格分隔

2. **目标环境规范**

   - -n, --name

     环境名称。

   - -p, --prefix

     环境位置的完整路径（即前缀）。

例子：

```
conda env config vars set MY_VAR=weee
```

#### `conda env config vars unset`

取消设置 conda 环境的环境变量。

```
usage: conda env config vars unset [-h] [-n ENVIRONMENT | -p PATH] [vars ...]
```

1. **位置参数**

   - vars

     要以 <KEY> 形式取消设置的环境变量，以空格分隔

2. **目标环境规范**

   - -n, --name

     环境名称。

   - -p, --prefix

     环境位置的完整路径（即前缀）。

例子：

```
conda env config vars unset MY_VAR
```

## `conda env create`

根据环境定义文件创建环境。

如果使用environment.yml文件（默认），您可以在文件的第一行使用“name:envname”命名环境，或者可以使用-n/--name参数在CLI命令中指定环境名称。 CLI 中指定的名称将覆盖在environment.yml 文件中指定的名称。

除非您位于包含环境定义文件的目录中，否则请使用 -f 指定要使用的环境定义文件的文件路径。

```
usage: conda env create [-h] [-f FILE] [-n ENVIRONMENT | -p PATH] [-C] [-k]
                        [--offline] [--no-default-packages] [--json] [-v] [-q]
                        [-d] [-y] [--solver {classic}] [--subdir SUBDIR]
                        [remote_definition]
```

1. **位置参数**

   - remote_definition

     远程环境定义/IPython 笔记本

2. **命名参数**

   - -f, --file

     环境定义文件（默认：environment.yml）

   - --no-default-packages

     忽略 .condarc 文件中的 create_default_packages。

   - --solver

     可能的选择：经典选择要使用的解算器后端。

   - --subdir, --platform

     可能的选择：emscripten-wasm32、wasi-wasm32、freebsd-64、linux-32、linux-64、linux-aarch64、linux-armv6l、linux-armv7l、linux-ppc64、linux-ppc64le、linux-riscv64、linux-s390x 、 osx-64、osx-arm64、win-32、win-64、win-arm64、zos-z使用为此平台构建的包。新环境将配置为记住此选择。格式应类似于“osx-64”、“linux-32”、“win-64”等。默认为当前（本机）平台。

3. **目标环境规范**

   - -n, --name

     环境名称。

   - -p, --prefix

     环境位置的完整路径（即前缀）。

4. **网络选项**

   - -C, --use-index-cache

     使用通道索引文件的缓存，即使它已过期。如果您不希望 conda 检查是否存在新版本的 repodata 文件，这非常有用，这将节省带宽。

   - -k, --insecure

     允许 conda 执行“不安全”的 SSL 连接和传输。相当于将“ssl_verify”设置为“false”。

   - --offline

     离线模式。不要连接到互联网。

5. **输出、提示和流程控制选项**

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
conda env create
conda env create -n envname
conda env create folder/envname
conda env create -f /path/to/environment.yml
conda env create -f /path/to/requirements.txt -n envname
conda env create -f /path/to/requirements.txt -p /home/user/envname
```

## `conda env export`

导出给定环境

```
usage: conda env export [-h] [-c CHANNEL] [--override-channels]
                        [-n ENVIRONMENT | -p PATH] [-f FILE] [--no-builds]
                        [--ignore-channels] [--json] [-v] [-q]
                        [--from-history]
```

1. **命名参数**

   - -c, --channel

     要包含在导出中的其他渠道

   - --override-channels

     不包括 .condarc 频道

   - -f, --file

     导出环境的文件名或路径。注意：这将默默地覆盖当前目录中任何现有的同名文件。

   - --no-builds

     从依赖项中删除构建规范

   - --ignore-channels

     请勿将频道名称包含在包名称中。

   - --from-history

     根据历史中的明确规范构建环境规范

2. **目标环境规范**

   - -n, --name

     环境名称。

   - -p, --prefix

     环境位置的完整路径（即前缀）。

3. **输出、提示和流程控制选项**

   - --json

     将所有输出报告为 json。适合以编程方式使用 conda。

   - -v, --verbose

     可以多次使用。一次用于详细输出，两次用于 INFO 日志记录，三次用于 DEBUG 日志记录，四次用于 TRACE 日志记录。

   - -q, --quiet

     不显示进度条。

例子：

```
conda export
conda export --file FILE_NAME
```

## `conda env list`

列出 Conda 环境。

```
usage: conda env list [-h] [--json] [-v] [-q]
```

1. **输出、提示和流程控制选项**

   - --json

     将所有输出报告为 json。适合以编程方式使用 conda。

   - -v, --verbose

     可以多次使用。一次用于详细输出，两次用于 INFO 日志记录，三次用于 DEBUG 日志记录，四次用于 TRACE 日志记录。

   - -q, --quiet

     不显示进度条。

例子：

```
conda env list
conda env list --json
```

## `conda env remove`

删除环境。

删除提供的环境。您必须先停用现有环境，然后才能将其删除。

```
usage: conda env remove [-h] [-n ENVIRONMENT | -p PATH] [--solver {classic}]
                        [--json] [-v] [-q] [-d] [-y]
```

1. **命名参数**

   - --solver

     可能的选择：经典选择要使用的解算器后端。

2. **目标环境规范**

   - -n, --name

     环境名称。

   - -p, --prefix

     环境位置的完整路径（即前缀）。

3. **输出、提示和流程控制选项**

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
conda env remove --name FOO
conda env remove -n FOO
```

## `conda env update`

根据环境文件更新当前环境。

```
usage: conda env update [-h] [-n ENVIRONMENT | -p PATH] [-f FILE] [--prune]
                        [--json] [-v] [-q] [--solver {classic}]
                        [remote_definition]
```

1. **位置参数**

   - remote_definition

     远程环境定义/IPython 笔记本

2. 命名参数

   - -f, --file

     环境定义（默认：environment.yml）

   - --prune

     删除environment.yml中未定义的已安装软件包

   - --solver

     可能的选择：经典选择要使用的解算器后端。

3. **目标环境规范**

   - -n, --name

     环境名称。

   - -p, --prefix

     环境位置的完整路径（即前缀）。

4. **输出、提示和流程控制选项**

   - --json

     将所有输出报告为 json。适合以编程方式使用 conda。

   - -v, --verbose

     可以多次使用。一次用于详细输出，两次用于 INFO 日志记录，三次用于 DEBUG 日志记录，四次用于 TRACE 日志记录。

   - -q, --quiet

     不显示进度条。

例子：

```
conda env update
conda env update -n=foo
conda env update -f=/path/to/environment.yml
conda env update --name=foo --file=environment.yml
conda env update vader/deathstar
```
