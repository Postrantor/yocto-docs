---
tip: translate by baidu@2023-06-07 17:11:50
...
---
title: Conserving Disk Space
----------------------------

# Conserving Disk Space During Builds

To help conserve disk space during builds, you can add the following statement to your project\'s `local.conf` configuration file found in the `Build Directory`{.interpreted-text role="term"}:

> 为了在生成过程中节省磁盘空间，您可以将以下语句添加到项目的“本地.conf”配置文件中，该文件位于“生成目录”｛.depreted text role=“term”｝中：

```
INHERIT += "rm_work"
```

Adding this statement deletes the work directory used for building a recipe once the recipe is built. For more information on \"rm_work\", see the `ref-classes-rm-work`{.interpreted-text role="ref"} class in the Yocto Project Reference Manual.

> 添加此语句会在配方生成后删除用于生成配方的工作目录。有关“rm_work”的更多信息，请参阅《Yocto 项目参考手册》中的“ref classes rm work”｛.depreted text role=“ref”｝类。

When you inherit this class and build a `core-image-sato` image for a `qemux86-64` machine from an Ubuntu 22.04 x86-64 system, you end up with a final disk usage of 22 Gbytes instead of &MIN_DISK_SPACE; Gbytes. However, &MIN_DISK_SPACE_RM_WORK; Gbytes of initial free disk space are still needed to create temporary files before they can be deleted.

> 当您继承这个类并从 Ubuntu 22.04 x86-64 系统为“qemux86-64”机器构建“核心映像 sato”映像时，最终磁盘使用量为 22 GB，而不是&MIN_disk_SPACE；GB。但是，&MIN_DISK_SPACE_RM_WORK；在删除临时文件之前，仍需要 GB 的初始可用磁盘空间来创建临时文件。

# Purging Duplicate Shared State Cache Files

After multiple build iterations, the Shared State (sstate) cache can contain duplicate cache files for a given package, while only the most recent one is likely to be reusable. The following command purges all but the newest sstate cache file for each package:

> 在多次构建迭代之后，共享状态（sstate）缓存可以包含给定包的重复缓存文件，而只有最近的一个可能是可重用的。以下命令为每个包清除除最新 sstate 缓存文件外的所有文件：

```
sstate-cache-management.sh --remove-duplicated --cache-dir=build/sstate-cache
```

This command will ask you to confirm the deletions it identifies.

> 此命令将要求您确认它所标识的删除。

::: note
::: title
Note
:::

The duplicated sstate cache files of one package must have the same architecture, which means that sstate cache files with multiple architectures are not considered as duplicate.

> 一个包的重复 sstate 缓存文件必须具有相同的体系结构，这意味着具有多个体系结构的 sstate 高速缓存文件不被视为重复。
> :::

Run `sstate-cache-management.sh` for more details about this script.

> 有关此脚本的详细信息，请运行“sstate-cache-management.sh”。
