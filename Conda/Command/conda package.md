# `conda package`



创建低级 conda 包。 （实验性）



```
usage: conda package [-h] [-n ENVIRONMENT | -p PATH] [-w PATH [PATH ...]] [-r]
                     [-u] [--pkg-name PKG_NAME] [--pkg-version PKG_VERSION]
                     [--pkg-build PKG_BUILD]
```

## 命名参数

- -w, --which

  给定某个文件的 PATH，打印该文件来自哪个 conda 包。

- -r, --reset

  删除所有未跟踪的文件并退出。

- -u, --untracked

  显示所有未跟踪的文件并退出。

- --pkg-name

  指定正在创建的包的包名称。

- --pkg-version

  指定正在创建的包的包版本。

- --pkg-build

  指定正在创建的包的包内部版本号。

## 目标环境规范

- -n, --name

  环境名称。

- -p, --prefix

  环境位置的完整路径（即前缀）。