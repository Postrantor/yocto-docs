---
tip: translate by openai@2023-06-10 10:44:40
...
---
title: Conserving Disk Space
----------------------------

# Conserving Disk Space During Builds

To help conserve disk space during builds, you can add the following statement to your project\'s `local.conf` configuration file found in the `Build Directory`:

> 在构建过程中为了节省磁盘空间，您可以在 `Build Directory` 中的 `local.conf` 配置文件中添加以下语句：

```
INHERIT += "rm_work"
```

Adding this statement deletes the work directory used for building a recipe once the recipe is built. For more information on \"rm_work\", see the `ref-classes-rm-work` class in the Yocto Project Reference Manual.

> 加入这个语句会在编译配方完成后删除用于构建配方的工作目录。有关“rm_work”的更多信息，请参见 Yocto 项目参考手册中的 `ref-classes-rm-work` 类。

When you inherit this class and build a `core-image-sato` image for a `qemux86-64` machine from an Ubuntu 22.04 x86-64 system, you end up with a final disk usage of 22 Gbytes instead of &MIN_DISK_SPACE; Gbytes. However, &MIN_DISK_SPACE_RM_WORK; Gbytes of initial free disk space are still needed to create temporary files before they can be deleted.

> 当你从 Ubuntu 22.04 x86-64 系统继承这个类并为 qemux86-64 机器构建一个 core-image-sato 镜像时，最终磁盘使用量会是 22 Gbytes 而不是&MIN_DISK_SPACE; Gbytes。但是，在创建并删除临时文件之前仍需要&MIN_DISK_SPACE_RM_WORK; Gbytes 的最初可用磁盘空间。

# Purging Duplicate Shared State Cache Files

After multiple build iterations, the Shared State (sstate) cache can contain duplicate cache files for a given package, while only the most recent one is likely to be reusable. The following command purges all but the newest sstate cache file for each package:

> 经过多次构建迭代，共享状态(sstate)缓存可能会包含给定包的重复缓存文件，而只有最新的文件可能是可重用的。以下命令将清除除最新的 sstate 缓存文件之外的所有文件：

```
sstate-cache-management.sh --remove-duplicated --cache-dir=build/sstate-cache
```

This command will ask you to confirm the deletions it identifies.

::: note
::: title
Note
:::

The duplicated sstate cache files of one package must have the same architecture, which means that sstate cache files with multiple architectures are not considered as duplicate.

> 重复的 sstate 缓存文件必须具有相同的架构，这意味着具有多个架构的 sstate 缓存文件不被视为重复。
> :::

Run `sstate-cache-management.sh` for more details about this script.
