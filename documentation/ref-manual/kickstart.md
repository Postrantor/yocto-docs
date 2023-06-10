---
tip: translate by openai@2023-06-07 22:11:17
...
---
title: OpenEmbedded Kickstart (`.wks`) Reference
------------------------------------------

# Introduction {#openembedded-kickstart-wks-reference}

The current Wic implementation supports only the basic kickstart partitioning commands: `partition` (or `part` for short) and `bootloader`.

::: note
::: title
Note
:::

Future updates will implement more commands and options. If you use anything that is not specifically supported, results can be unpredictable.
:::

This chapter provides a reference on the available kickstart commands. The information lists the commands, their syntax, and meanings. Kickstart commands are based on the Fedora kickstart versions but with modifications to reflect Wic capabilities. You can see the original documentation for those commands at the following link: [https://pykickstart.readthedocs.io/en/latest/kickstart-docs.html](https://pykickstart.readthedocs.io/en/latest/kickstart-docs.html)

> 本章提供了可用 kickstart 命令的参考。信息列出了命令、其语法和含义。Kickstart 命令基于 Fedora kickstart 版本，但已经修改以反映 Wic 功能。您可以在以下链接中查看有关这些命令的原始文档：[https://pykickstart.readthedocs.io/en/latest/kickstart-docs.html](https://pykickstart.readthedocs.io/en/latest/kickstart-docs.html)

# Command: part or partition

Either of these commands creates a partition on the system and uses the following syntax:

```
part [mntpoint]
partition [mntpoint]
```

If you do not provide mntpoint, Wic creates a partition but does not mount it.

The `mntpoint` is where the partition is mounted and must be in one of the following forms:

- `/path`: For example, \"/\", \"/usr\", or \"/home\"
- `swap`: The created partition is used as swap space

Specifying a mntpoint causes the partition to automatically be mounted. Wic achieves this by adding entries to the filesystem table (fstab) during image generation. In order for Wic to generate a valid fstab, you must also provide one of the `--ondrive`, `--ondisk`, or `--use-uuid` partition options as part of the command.

> 指定 mntpoint 会导致分区自动挂载。Wic 通过在图像生成期间添加条目到文件系统表(fstab)来实现这一点。为了使 Wic 生成有效的 fstab，您还必须在命令中提供 `--ondrive`、`--ondisk` 或 `--use-uuid` 分区选项之一。

::: note
::: title
Note
:::

The mount program must understand the PARTUUID syntax you use with `--use-uuid` and non-root *mountpoint*, including swap. The default configuration of BusyBox in OpenEmbedded supports this, but this may be disabled in custom configurations.

> mount 程序必须理解您使用 `--use-uuid` 和非 root *mountpoint* 所使用的 PARTUUID 语法，包括交换分区。OpenEmbedded 中的 BusyBox 的默认配置支持这一点，但是自定义配置可能会禁用它。
> :::

Here is an example that uses \"/\" as the mountpoint. The command uses `--ondisk` to force the partition onto the `sdb` disk:

```
part / --source rootfs --ondisk sdb --fstype=ext3 --label platform --align 1024
```

Here is a list that describes other supported options you can use with the `part` and `partition` commands:

- `--size`: The minimum partition size. Specify as an integer value optionally followed by one of the units \"k\" / \"K\" for kibibyte, \"M\" for mebibyte and \"G\" for gibibyte. The default unit if none is given is \"M\". You do not need this option if you use `--source`.

> `--size`：最小分区大小。指定为整数值，可选择以"k" / "K"表示 Kibibyte，"M"表示 Mebibyte，"G"表示 Gibibyte 的单位。如果没有指定单位，默认单位为"M"。如果使用 `--source`，则不需要此选项。

- `--fixed-size`: The exact partition size. Specify as an integer value optionally followed by one of the units \"k\" / \"K\" for kibibyte, \"M\" for mebibyte and \"G\" for gibibyte. The default unit if none is given is \"M\". Cannot be specify together with `--size`. An error occurs when assembling the disk image if the partition data is larger than `--fixed-size`.

> `--fixed-size`：精确的分区大小。以整数值指定，可选择以“k”/“K”表示 Kibibyte，“M”表示 Mebibyte，“G”表示 Gibibyte 的单位。如果没有指定单位，默认为“M”。不能与 `--size` 一起指定。如果分区数据大于 `--fixed-size`，组装磁盘映像时会出现错误。

- `--source`: This option is a Wic-specific option that names the source of the data that populates the partition. The most common value for this option is \"rootfs\", but you can use any value that maps to a valid source plugin. For information on the source plugins, see the \"`dev-manual/wic:using the wic plugin interface`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> 这个选项是 Wic 特定的选项，用于指定填充分区的数据源。最常用的值是“rootfs”，但您也可以使用任何映射到有效源插件的值。有关源插件的信息，请参阅 Yocto 项目开发任务手册中的“使用 wic 插件接口”部分。

If you use `--source rootfs`, Wic creates a partition as large as needed and fills it with the contents of the root filesystem pointed to by the `-r` command-line option or the equivalent root filesystem derived from the `-e` command-line option. The filesystem type used to create the partition is driven by the value of the `--fstype` option specified for the partition. See the entry on `--fstype` that follows for more information.

> 如果您使用 `--source rootfs`，Wic 会根据需要创建一个分区，并使用由 `-r` 命令行选项指向的根文件系统的内容或由 `-e` 命令行选项派生的等效根文件系统填充该分区。用于创建分区的文件系统类型由为该分区指定的 `--fstype` 选项的值驱动。有关更多信息，请参阅下面的 `--fstype` 条目。

If you use `--source plugin-name`, Wic creates a partition as large as needed and fills it with the contents of the partition that is generated by the specified plugin name using the data pointed to by the `-r` command-line option or the equivalent root filesystem derived from the `-e` command-line option. Exactly what those contents are and filesystem type used are dependent on the given plugin implementation.

> 如果您使用 `--source plugin-name`，Wic 会创建一个尽可能大的分区，并使用由指定插件名称生成的分区内容填充它，该内容由 `-r` 命令行选项指向或由 `-e` 命令行选项派生的等效根文件系统提供。这些内容究竟是什么以及使用的文件系统类型取决于给定的插件实现。

If you do not use the `--source` option, the `wic` command creates an empty partition. Consequently, you must use the `--size` option to specify the size of the empty partition.

> 如果您不使用 `--source` 选项，`wic` 命令将创建一个空分区。因此，您必须使用 `--size` 选项来指定空分区的大小。

- `--ondisk` or `--ondrive`: Forces the partition to be created on a particular disk.
- `--fstype`: Sets the file system type for the partition. Valid values are:

  - `btrfs`
  - `erofs`
  - `ext2`
  - `ext3`
  - `ext4`
  - `squashfs`
  - `swap`
  - `vfat`
- `--fsoptions`: Specifies a free-form string of options to be used when mounting the filesystem. This string is copied into the `/etc/fstab` file of the installed system and should be enclosed in quotes. If not specified, the default string is \"defaults\".

> `--fsoptions`：指定要在挂载文件系统时使用的自由形式的选项字符串。此字符串将复制到安装系统的 `/etc/fstab` 文件中，应使用引号括起来。如果未指定，则默认字符串为“defaults”。

- `--label label`: Specifies the label to give to the filesystem to be made on the partition. If the given label is already in use by another filesystem, a new label is created for the partition.

> `- `--label label`：指定要在分区上创建的文件系统的标签。如果给定的标签已被另一个文件系统使用，则为分区创建一个新标签。

- `--active`: Marks the partition as active.
- `--align (in KBytes)`: This option is a Wic-specific option that says to start partitions on boundaries given x KBytes.
- `--offset`: This option is a Wic-specific option that says to place a partition at exactly the specified offset. If the partition cannot be placed at the specified offset, the image build will fail. Specify as an integer value optionally followed by one of the units \"s\" / \"S\" for 512 byte sector, \"k\" / \"K\" for kibibyte, \"M\" for mebibyte and \"G\" for gibibyte. The default unit if none is given is \"k\".

> `--offset`：这个选项是 Wic 特定的选项，表示在指定的偏移量处放置一个分区。如果无法在指定的偏移量处放置分区，则映像构建将失败。指定为整数值，可选地后跟“s”/“S”（512 字节扇区）、“k”/“K”（KiB）、“M”（MiB）和“G”（GiB）单位之一。如果未指定单位，则默认单位为“k”。

- `--no-table`: This option is a Wic-specific option. Using the option reserves space for the partition and causes it to become populated. However, the partition is not added to the partition table.

> `-`--no-table`：此选项是 Wic 特定选项。使用该选项会为分区保留空间，并使其变得充满。但是，该分区不会添加到分区表中。

- `--exclude-path`: This option is a Wic-specific option that excludes the given relative path from the resulting image. This option is only effective with the rootfs source plugin.

> `--exclude-path`：此选项是 Wic 特定的选项，可以从生成的映像中排除给定的相对路径。此选项仅在 rootfs 源插件中有效。

- `--extra-space`: This option is a Wic-specific option that adds extra space after the space filled by the content of the partition. The final size can exceed the size specified by the `--size` option. The default value is 10M. Specify as an integer value optionally followed by one of the units \"k\" / \"K\" for kibibyte, \"M\" for mebibyte and \"G\" for gibibyte. The default unit if none is given is \"M\".

> `--extra-space`：这个选项是 Wic 特有的，它会在分区内容填充完成后添加额外的空间，最终大小可以超过 `--size` 选项指定的大小。默认值为 10M。可以指定一个整数值，可选地后接"k" / "K"（Kibibyte）、"M"（Mebibyte）或"G"（Gibibyte）等单位。如果没有指定单位，默认单位为"M"。

- `--overhead-factor`: This option is a Wic-specific option that multiplies the size of the partition by the option\'s value. You must supply a value greater than or equal to \"1\". The default value is \"1.3\".

> `- `--overhead-factor`: 此选项是 Wic 特定的选项，可将分区的大小乘以该选项的值。您必须提供大于或等于“1”的值。默认值为“1.3”。

- `--part-name`: This option is a Wic-specific option that specifies a name for GPT partitions.
- `--part-type`: This option is a Wic-specific option that specifies the partition type globally unique identifier (GUID) for GPT partitions. You can find the list of partition type GUIDs at `GUID_Partition_Table#Partition_type_GUIDs`{.interpreted-text role="wikipedia"}.

> `-`--part-type `：此选项是Wic专用选项，用于指定GPT分区的全局唯一标识符（GUID）。您可以在GUID_Partition_Table#Partition_type_GUIDs`{.interpreted-text role="wikipedia"}中找到分区类型 GUID 的列表。

- `--use-uuid`: This option is a Wic-specific option that causes Wic to generate a random GUID for the partition. The generated identifier is used in the bootloader configuration to specify the root partition.

> `-`--use-uuid`：此选项是 Wic 特定的选项，它会导致 Wic 为分区生成一个随机的 GUID。生成的标识符用于引导程序配置中指定根分区。

- `--uuid`: This option is a Wic-specific option that specifies the partition UUID.
- `--fsuuid`: This option is a Wic-specific option that specifies the filesystem UUID. You can generate or modify `WKS_FILE`{.interpreted-text role="term"} with this option if a preconfigured filesystem UUID is added to the kernel command line in the bootloader configuration before you run Wic.

> `-`--fsuuid `: 这个选项是Wic特定的选项，用于指定文件系统UUID。如果在运行Wic之前在引导加载程序配置中向内核命令行添加了预先配置的文件系统UUID，则可以使用此选项生成或修改` WKS_FILE`{.interpreted-text role="term"}。

- `--system-id`: This option is a Wic-specific option that specifies the partition system ID, which is a one byte long, hexadecimal parameter with or without the 0x prefix.

> `-`--system-id`: 这个选项是 Wic 特定的选项，用于指定分区系统 ID，它是一个以十六进制表示的一个字节长度的参数，可以带有或不带 0x 前缀。

- `--mkfs-extraopts`: This option specifies additional options to pass to the `mkfs` utility. Some default options for certain filesystems do not take effect. See Wic\'s help on kickstart (i.e. `wic help kickstart`).

> `-`--mkfs-extraopts `: 此选项用于指定传递给` mkfs `实用程序的其他选项。某些默认文件系统选项不会生效。请参阅Wic的Kickstart帮助（即` wic help kickstart`）。

# Command: bootloader

This command specifies how the bootloader should be configured and supports the following options:

::: note
::: title
Note
:::

Bootloader functionality and boot partitions are implemented by the various source plugins that implement bootloader functionality. The bootloader command essentially provides a means of modifying bootloader configuration.

> 引导程序功能和引导分区由实现引导程序功能的各种源插件实现。引导程序命令实际上提供了一种修改引导程序配置的方法。
> :::

- `--append`: Specifies kernel parameters. These parameters will be added to the syslinux `APPEND`{.interpreted-text role="term"} or `grub` kernel command line.

> `-`--append `：指定内核参数。这些参数将被添加到syslinux的` APPEND `或` grub` 内核命令行中。

- `--configfile`: Specifies a user-defined configuration file for the bootloader. You can provide a full pathname for the file or a file located in the `canned-wks` folder. This option overrides all other bootloader options.

> `--configfile`：为引导程序指定一个用户定义的配置文件。您可以为文件提供完整的路径名或位于 `canned-wks` 文件夹中的文件。此选项将覆盖所有其他引导程序选项。

- `--ptable`: Specifies the partition table format. Valid values are:
  - `msdos`
  - `gpt`
- `--timeout`: Specifies the number of seconds before the bootloader times out and boots the default option.
