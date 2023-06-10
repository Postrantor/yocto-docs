---
tip: translate by openai@2023-06-10 10:51:34
...
---
title: Selecting an Initialization Manager
------------------------------------------

By default, the Yocto Project uses `SysVinit <Init#SysV-style>`, which is a full replacement for init with parallel starting of services, reduced shell overhead, increased security and resource limits for services, and other features that are used by many distributions.

> 默认情况下，Yocto 项目使用 SysVinit 作为初始化管理器。还支持 BusyBox init，一个更简单的实现，以及支持 systemd，它是一个用于替换 init 的完整替代品，可以并行启动服务，减少 shell 开销，增加服务的安全性和资源限制，以及许多分发版使用的其他功能。

Within the system, SysVinit and BusyBox init treat system components as services. These services are maintained as shell scripts stored in the `/etc/init.d/` directory.

> 在系统中，SysVinit 和 BusyBox init 将系统组件视为服务。这些服务被维护为存储在 `/etc/init.d/` 目录中的 shell 脚本。

SysVinit is more elaborate than BusyBox init and organizes services in different run levels. This organization is maintained by putting links to the services in the `/etc/rcN.d/` directories, where [N/] is one of the following options: \"S\", \"0\", \"1\", \"2\", \"3\", \"4\", \"5\", or \"6\".

> SysVinit 比 BusyBox init 更精细，并且将服务组织到不同的运行级别中。这种组织是通过在 `/etc/rcN.d/` 目录中放置服务的链接来维护的，其中[N/]是以下选项之一：“S”、“0”、“1”、“2”、“3”、“4”、“5”或“6”。

::: note
::: title
Note
:::

Each runlevel has a dependency on the previous runlevel. This dependency allows the services to work properly.
:::

Both SysVinit and BusyBox init are configured through the `/etc/inittab` file, with a very similar syntax, though of course BusyBox init features are more limited.

> 两个 SysVinit 和 BusyBox init 都是通过 `/etc/inittab` 文件进行配置，语法非常相似，但是当然 BusyBox init 的功能更为有限。

In comparison, systemd treats components as units. Using units is a broader concept as compared to using a service. A unit includes several different types of entities. `Service` is one of the types of entities. The runlevel concept in SysVinit corresponds to the concept of a target in systemd, where target is also a type of supported unit.

> 比较而言，systemd 将组件视为单元。与使用服务相比，使用单元是一个更广泛的概念。单元包括几种不同类型的实体。`服务` 是其中一种类型的实体。SysVinit 中的运行级别概念对应于 systemd 中的目标概念，其中目标也是支持的单元的一种类型。

In systems with SysVinit or BusyBox init, services load sequentially (i.e. one by one) during init and parallelization is not supported. With systemd, services start in parallel. This method can have an impact on the startup performance of a given service, though systemd will also provide more services by default, therefore increasing the total system boot time. systemd also substantially increases system size because of its multiple components and the extra dependencies it pulls.

> 在使用 SysVinit 或 BusyBox init 的系统中，服务会按顺序(即一个接一个)加载，不支持并行化。使用 systemd，服务可以并行启动。这种方法可能会影响给定服务的启动性能，但 systemd 也会默认提供更多的服务，因此增加了整个系统的启动时间。systemd 也会大大增加系统的大小，因为它有多个组件，还有它拉取的额外依赖关系。

On the contrary, BusyBox init is the simplest and the lightest solution and also comes with BusyBox mdev as device manager, a lighter replacement to `udev <Udev>`, which SysVinit and systemd both use.

> 反之，BusyBox init 是最简单和最轻量级的解决方案，并配有 BusyBox mdev 作为设备管理器，这是一个替代 udev 的轻量级替代品，SysVinit 和 systemd 都使用它。

The \"`device-manager`\" chapter has more details about device managers.

# Using SysVinit with udev

SysVinit with the udev device manager corresponds to the default setting in Poky. This corresponds to setting:

```
INIT_MANAGER = "sysvinit"
```

# Using BusyBox init with BusyBox mdev

BusyBox init with BusyBox mdev is the simplest and lightest solution for small root filesystems. All you need is BusyBox, which most systems have anyway:

> BusyBox init 和 BusyBox mdev 是为小型根文件系统最简单、最轻量的解决方案。你所需要的只是大多数系统都有的 BusyBox。

```
INIT_MANAGER = "mdev-busybox"
```

# Using systemd

The last option is to use systemd together with the udev device manager. This is the most powerful and versatile solution, especially for more complex systems:

> 最后一个选择是使用 systemd 和 udev 设备管理器一起使用。这是最强大和最灵活的解决方案，特别是对于更复杂的系统来说。

```
INIT_MANAGER = "systemd"
```

This will enable systemd and remove sysvinit components from the image. See :yocto_[git:%60meta/conf/distro/include/init-manager-systemd.inc](git:%60meta/conf/distro/include/init-manager-systemd.inc) \</poky/tree/meta/conf/distro/include/init-manager-systemd.inc\>\` for exact details on what this does.

> 这将启用 systemd 并从映像中删除 sysvinit 组件。有关此操作的确切详细信息，请参阅：yocto_[git：`meta/conf/distro/include/init-manager-systemd.inc`](git:%60meta/conf/distro/include/init-manager-systemd.inc%60) \</poky/tree/meta/conf/distro/include/init-manager-systemd.inc\>\。

## Controling systemd from the target command line

Here is a quick reference for controling systemd from the command line on the target. Instead of opening and sometimes modifying files, most interaction happens through the `systemctl` and `journalctl` commands:

> 以下是从命令行控制 systemd 的快速参考。大多数交互是通过 `systemctl` 和 `journalctl` 命令完成的，而不是打开和修改文件：

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

> 反常识地，`systemd-journald` 不是一个 syslog 运行时或提供者，而正确的使用 `systemd-journald` 作为您唯一的日志机制的方法是通过在您的发行版配置文件中设置这些变量来有效地禁用 syslog：

```
VIRTUAL-RUNTIME_syslog = ""
VIRTUAL-RUNTIME_base-utils-syslog = ""
```

Doing so will prevent `rsyslog` / `busybox-syslog` from being pulled in by default, leaving only `systemd-journald`.

## Summary

The Yocto Project supports three different initialization managers, offering increasing levels of complexity and functionality:

---

```
                                BusyBox init    SysVinit              systemd
```

---

Size                              Small           Small                 Big[^1]

Complexity                        Small           Medium                High

Support for boot profiles         No              Yes (\"runlevels\")   Yes (\"targets\")

Services defined as               Shell scripts   Shell scripts         Description files

Starting services in parallel     No              No                    Yes

Setting service resource limits   No              No                    Yes

Support service isolation         No              No                    Yes

Integrated logging                No              No                    Yes
---------------------------------------------------------------------------

[^1]: Using systemd increases the `core-image-minimal` image size by 160% for `qemux86-64` on Mickledore (4.2), compared to SysVinit.
