---
tip: translate by openai@2023-06-10 10:43:53
...
---
title: Selecting a Device Manager
---------------------------------

The Yocto Project provides multiple ways to manage the device manager (`/dev`):

- Persistent and Pre-Populated `/dev`: For this case, the `/dev` directory is persistent and the required device nodes are created during the build.
- Use `devtmpfs` with a Device Manager: For this case, the `/dev` directory is provided by the kernel as an in-memory file system and is automatically populated by the kernel at runtime. Additional configuration of device nodes is done in user space by a device manager like `udev` or `busybox-mdev`.

> 使用设备管理器使用 devtmpfs：在这种情况下，内核将/dev 目录提供为内存文件系统，并在运行时由内核自动填充。通过像 udev 或 busybox-mdev 这样的设备管理器在用户空间中进行其他设备节点的配置。

# Using Persistent and Pre-Populated `/dev`

To use the static method for device population, you need to set the `USE_DEVFS`{.interpreted-text role="term"} variable to \"0\" as follows:

```
USE_DEVFS = "0"
```

The content of the resulting `/dev` directory is defined in a Device Table file. The `IMAGE_DEVICE_TABLES`{.interpreted-text role="term"} variable defines the Device Table to use and should be set in the machine or distro configuration file. Alternatively, you can set this variable in your `local.conf` configuration file.

> 结果 `/dev` 目录的内容由设备表文件定义。`IMAGE_DEVICE_TABLES`{.interpreted-text role="term"}变量定义了要使用的设备表，应该在机器或发行版配置文件中设置。或者，您也可以在 `local.conf` 配置文件中设置此变量。

If you do not define the `IMAGE_DEVICE_TABLES`{.interpreted-text role="term"} variable, the default `device_table-minimal.txt` is used:

```
IMAGE_DEVICE_TABLES = "device_table-mymachine.txt"
```

The population is handled by the `makedevs` utility during image creation:

# Using `devtmpfs` and a Device Manager

To use the dynamic method for device population, you need to use (or be sure to set) the `USE_DEVFS`{.interpreted-text role="term"} variable to \"1\", which is the default:

> 要使用动态方法来配置设备，您需要使用（或确保设置）`USE_DEVFS` 变量为“1”，这是默认值：

```
USE_DEVFS = "1"
```

With this setting, the resulting `/dev` directory is populated by the kernel using `devtmpfs`. Make sure the corresponding kernel configuration variable `CONFIG_DEVTMPFS` is set when building you build a Linux kernel.

> 在这种设置下，内核会使用 `devtmpfs` 填充 `/dev` 目录。确保在构建 Linux 内核时，相应的内核配置变量 `CONFIG_DEVTMPFS` 被设置。

All devices created by `devtmpfs` will be owned by `root` and have permissions `0600`.

To have more control over the device nodes, you can use a device manager like `udev` or `busybox-mdev`. You choose the device manager by defining the `VIRTUAL-RUNTIME_dev_manager` variable in your machine or distro configuration file. Alternatively, you can set this variable in your `local.conf` configuration file:

> 要对设备节点拥有更多的控制权，您可以使用设备管理器，如 udev 或 busybox-mdev。您可以通过在机器或发行版配置文件中定义 VIRTUAL-RUNTIME_dev_manager 变量来选择设备管理器。或者，您可以在 local.conf 配置文件中设置此变量：

```
VIRTUAL-RUNTIME_dev_manager = "udev"

# Some alternative values
# VIRTUAL-RUNTIME_dev_manager = "busybox-mdev"
# VIRTUAL-RUNTIME_dev_manager = "systemd"
```
