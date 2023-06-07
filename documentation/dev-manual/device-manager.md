---
tip: translate by baidu@2023-06-07 17:11:48
...
---
title: Selecting a Device Manager
---------------------------------

The Yocto Project provides multiple ways to manage the device manager (`/dev`):

> Yocto 项目提供了多种管理设备管理器（“/dev”）的方法：

- Persistent and Pre-Populated `/dev`: For this case, the `/dev` directory is persistent and the required device nodes are created during the build.

> -持久化和预填充的“/dev/”：在这种情况下，“/dev”目录是持久化的，并且在构建过程中创建所需的设备节点。

- Use `devtmpfs` with a Device Manager: For this case, the `/dev` directory is provided by the kernel as an in-memory file system and is automatically populated by the kernel at runtime. Additional configuration of device nodes is done in user space by a device manager like `udev` or `busybox-mdev`.

> -将“devtmpfs”与设备管理器一起使用：在这种情况下，“/dev”目录由内核作为内存中的文件系统提供，并在运行时由内核自动填充。设备节点的额外配置是由诸如“udev”或“busybox-mdev”之类的设备管理器在用户空间中完成的。

# Using Persistent and Pre-Populated `/dev`

To use the static method for device population, you need to set the `USE_DEVFS`{.interpreted-text role="term"} variable to \"0\" as follows:

> 要使用静态方法进行设备填充，您需要将 `use_DEVFS`｛.explored text role=“term”｝变量设置为\“0\”，如下所示：

```
USE_DEVFS = "0"
```

The content of the resulting `/dev` directory is defined in a Device Table file. The `IMAGE_DEVICE_TABLES`{.interpreted-text role="term"} variable defines the Device Table to use and should be set in the machine or distro configuration file. Alternatively, you can set this variable in your `local.conf` configuration file.

> 生成的“/dev/”目录的内容在设备表文件中定义。`IMAGE_DEVICE_TABLES`｛.explored text role=“term”｝变量定义了要使用的设备表，应在机器或发行版配置文件中设置。或者，您可以在“local.conf”配置文件中设置此变量。

If you do not define the `IMAGE_DEVICE_TABLES`{.interpreted-text role="term"} variable, the default `device_table-minimal.txt` is used:

> 如果未定义 `IMAGE_DEVICE_TABLES`｛.explored text role=“term”｝变量，则使用默认的 `DEVICE_table-minimal.txt`：

```
IMAGE_DEVICE_TABLES = "device_table-mymachine.txt"
```

The population is handled by the `makedevs` utility during image creation:

> 在图像创建过程中，填充由“makedevs”实用程序处理：

# Using `devtmpfs` and a Device Manager

To use the dynamic method for device population, you need to use (or be sure to set) the `USE_DEVFS`{.interpreted-text role="term"} variable to \"1\", which is the default:

> 要使用设备填充的动态方法，您需要使用（或确保设置）`use_DEVFS`｛.depredicted text role=“term”｝变量为\“1\”，这是默认值：

```
USE_DEVFS = "1"
```

With this setting, the resulting `/dev` directory is populated by the kernel using `devtmpfs`. Make sure the corresponding kernel configuration variable `CONFIG_DEVTMPFS` is set when building you build a Linux kernel.

> 使用此设置，内核将使用“devtmpfs”填充生成的“/dev”目录。在构建 Linux 内核时，请确保设置了相应的内核配置变量“CONFIG_DEVTMPFS”。

All devices created by `devtmpfs` will be owned by `root` and have permissions `0600`.

> “devtmpfs”创建的所有设备都将归“root”所有，并具有“0600”权限。

To have more control over the device nodes, you can use a device manager like `udev` or `busybox-mdev`. You choose the device manager by defining the `VIRTUAL-RUNTIME_dev_manager` variable in your machine or distro configuration file. Alternatively, you can set this variable in your `local.conf` configuration file:

> 为了更好地控制设备节点，可以使用类似“udev”或“busybox-mdev”的设备管理器。您可以通过在机器或发行版配置文件中定义“VIRTUAL-RUNTIME_dev_manager”变量来选择设备管理器。或者，您可以在“local.conf”配置文件中设置此变量：

```
VIRTUAL-RUNTIME_dev_manager = "udev"

# Some alternative values
# VIRTUAL-RUNTIME_dev_manager = "busybox-mdev"
# VIRTUAL-RUNTIME_dev_manager = "systemd"
```
