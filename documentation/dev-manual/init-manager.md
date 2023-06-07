---
tip: translate by baidu@2023-06-07 17:12:11
...
---
title: Selecting an Initialization Manager
------------------------------------------

By default, the Yocto Project uses `SysVinit <Init#SysV-style>`{.interpreted-text role="wikipedia"} as the initialization manager. There is also support for BusyBox init, a simpler implementation, as well as support for `systemd <Systemd>`{.interpreted-text role="wikipedia"}, which is a full replacement for init with parallel starting of services, reduced shell overhead, increased security and resource limits for services, and other features that are used by many distributions.

> 默认情况下，Yocto 项目使用 `SysVinit<Init#SysV style>`{.depreted text role=“wikipedia”}作为初始化管理器。此外，还支持 BusyBox-init，这是一种更简单的实现，以及对 `systemd<systemd>`{.depredicted text role=“wikipedia”}的支持，它通过并行启动服务、减少 shell 开销、增加服务的安全性和资源限制，以及许多发行版使用的其他功能，完全取代了 init。

Within the system, SysVinit and BusyBox init treat system components as services. These services are maintained as shell scripts stored in the `/etc/init.d/` directory.

> 在系统中，SysVinit 和 BusyBoxinit 将系统组件视为服务。这些服务作为 shell 脚本进行维护，存储在“/etc/init.d/”目录中。

SysVinit is more elaborate than BusyBox init and organizes services in different run levels. This organization is maintained by putting links to the services in the `/etc/rcN.d/` directories, where [N/]{.title-ref} is one of the following options: \"S\", \"0\", \"1\", \"2\", \"3\", \"4\", \"5\", or \"6\".

> SysVinit 比 BusyBox-init 更复杂，并且在不同的运行级别中组织服务。通过将服务链接放在 `/etc/rcN.d/` 目录中来维护此组织，其中[N/]｛.title-ref｝是以下选项之一：\“S\”、\“0\”、\”1\“、\”2\“、”3\“、\“4\”、“5\”或\“6\”。

::: note
::: title
Note
:::

Each runlevel has a dependency on the previous runlevel. This dependency allows the services to work properly.

> 每个运行级别都依赖于上一个运行级别。这种依赖关系允许服务正常工作。
> :::

Both SysVinit and BusyBox init are configured through the `/etc/inittab` file, with a very similar syntax, though of course BusyBox init features are more limited.

> SysVinit 和 BusyBox init 都是通过“/etc/inittab”文件配置的，语法非常相似，当然 BusyBox 的 init 功能更为有限。

In comparison, systemd treats components as units. Using units is a broader concept as compared to using a service. A unit includes several different types of entities. `Service` is one of the types of entities. The runlevel concept in SysVinit corresponds to the concept of a target in systemd, where target is also a type of supported unit.

> 相比之下，systemd 将组件视为单元。与使用服务相比，使用单元是一个更广泛的概念。一个单元包括几种不同类型的实体 `“服务”是实体类型之一。SysVinit 中的运行级概念对应于 systemd 中的目标概念，其中目标也是一种受支持的单元类型。

In systems with SysVinit or BusyBox init, services load sequentially (i.e. one by one) during init and parallelization is not supported. With systemd, services start in parallel. This method can have an impact on the startup performance of a given service, though systemd will also provide more services by default, therefore increasing the total system boot time. systemd also substantially increases system size because of its multiple components and the extra dependencies it pulls.

> 在具有 SysVinit 或 BusyBox-init 的系统中，服务在 init 期间按顺序（即逐个）加载，并且不支持并行化。有了 systemd，服务可以并行启动。这种方法可能会对给定服务的启动性能产生影响，尽管默认情况下 systemd 也会提供更多服务，从而增加系统的总启动时间。systemd 还大大增加了系统的大小，因为它有多个组件和额外的依赖关系。

On the contrary, BusyBox init is the simplest and the lightest solution and also comes with BusyBox mdev as device manager, a lighter replacement to `udev <Udev>`{.interpreted-text role="wikipedia"}, which SysVinit and systemd both use.

> 相反，BusyBox-init 是最简单、最轻的解决方案，还附带了 BusyBox-mdev 作为设备管理器，是 SysVinit 和 systemd 都使用的 `udev<udev>`{.depreted text role=“wikipedia”}的更轻的替代品。

The \"`device-manager`{.interpreted-text role="ref"}\" chapter has more details about device managers.

> “`device-manager`｛.explored text role=”ref“｝”一章提供了有关设备管理器的更多详细信息。

# Using SysVinit with udev

SysVinit with the udev device manager corresponds to the default setting in Poky. This corresponds to setting:

> 带有 udev 设备管理器的 SysVinit 对应于 Poky 中的默认设置。这对应于设置：

```
INIT_MANAGER = "sysvinit"
```

# Using BusyBox init with BusyBox mdev

BusyBox init with BusyBox mdev is the simplest and lightest solution for small root filesystems. All you need is BusyBox, which most systems have anyway:

> 带有 BusyBox-mdev 的 BusyBox-init 是适用于小型根文件系统的最简单、最轻的解决方案。您所需要的只是 BusyBox，大多数系统都有：

```
INIT_MANAGER = "mdev-busybox"
```

# Using systemd

The last option is to use systemd together with the udev device manager. This is the most powerful and versatile solution, especially for more complex systems:

> 最后一个选项是将 systemd 与 udev 设备管理器一起使用。这是功能最强大、用途最广泛的解决方案，尤其适用于更复杂的系统：

```
INIT_MANAGER = "systemd"
```

This will enable systemd and remove sysvinit components from the image. See :yocto\_[git:%60meta/conf/distro/include/init-manager-systemd.inc](git:%60meta/conf/distro/include/init-manager-systemd.inc) \</poky/tree/meta/conf/distro/include/init-manager-systemd.inc\>\` for exact details on what this does.

> 这将启用 systemd 并从映像中删除 sysvinit 组件。请参阅：yocto\_[git:%60meta/conf/distro/include/init-manager-systemd.inc]（git:%6 meta/conf/distro/include/init-manager-systemd.inc）\</poky/tree/meta/conf/distro/include/init-anager-systed.inc\>\`以获取有关此操作的详细信息。

## Controling systemd from the target command line

Here is a quick reference for controling systemd from the command line on the target. Instead of opening and sometimes modifying files, most interaction happens through the `systemctl` and `journalctl` commands:

> 以下是从目标上的命令行控制 systemd 的快速参考。大多数交互都是通过“systemctl”和“journalctl”命令进行的，而不是打开有时修改文件：

- `systemctl status`: show the status of all services
- `systemctl status <service>`: show the status of one service
- `systemctl [start|stop] <service>`: start or stop a service
- `systemctl [enable|disable] <service>`: enable or disable a service at boot time
- `systemctl list-units`: list all available units
- `journalctl -a`: show all logs for all services
- `journalctl -f`: show only the last log entries, and keep printing updates as they arrive
- `journalctl -u`: show only logs from a particular service

## Using systemd-journald without a traditional syslog daemon

Counter-intuitively, `systemd-journald` is not a syslog runtime or provider, and the proper way to use `systemd-journald` as your sole logging mechanism is to effectively disable syslog entirely by setting these variables in your distribution configuration file:

> 与直觉相反，“systemd journal”不是系统日志运行时或提供程序，使用“systemd 日记”作为唯一日志记录机制的正确方法是通过在分发配置文件中设置以下变量来有效地完全禁用系统日志：

```
VIRTUAL-RUNTIME_syslog = ""
VIRTUAL-RUNTIME_base-utils-syslog = ""
```

Doing so will prevent `rsyslog` / `busybox-syslog` from being pulled in by default, leaving only `systemd-journald`.

> 这样做将防止默认情况下拉入“rsyslog”/“busybox syslog”，只留下“systemd journed”。

## Summary

The Yocto Project supports three different initialization managers, offering increasing levels of complexity and functionality:

> Yocto 项目支持三种不同的初始化管理器，提供了越来越高的复杂性和功能级别：

---

```
                                BusyBox init    SysVinit              systemd
```

---

Size                              Small           Small                 Big[^1]

> 大小-小-大[^1]

Complexity                        Small           Medium                High

> 复杂性中小型-高

Support for boot profiles         No              Yes (\"runlevels\")   Yes (\"targets\")

> 支持启动配置文件否是（“runlevels”）是（“targets”）

Services defined as               Shell scripts   Shell scripts         Description files

> 服务定义为外壳脚本外壳脚本描述文件

Starting services in parallel     No              No                    Yes

> 并行启动服务否否是

Setting service resource limits   No              No                    Yes

> 设置服务资源限制否否是

Support service isolation         No              No                    Yes

> 支持服务隔离否否是

Integrated logging                No              No                    Yes

> 集成日志记录否否是

---

[^1]: Using systemd increases the `core-image-minimal` image size by 160% for `qemux86-64` on Mickledore (4.2), compared to SysVinit.


> [^1]：与 SysVinit 相比，使用 systemd 将 Mickledore（4.2）上“qemux86-64”的“核心图像最小”图像大小增加了 160%。
