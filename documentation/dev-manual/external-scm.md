---
tip: translate by baidu@2023-06-07 17:11:57
...
---
title: Using an External SCM
----------------------------

If you\'re working on a recipe that pulls from an external Source Code Manager (SCM), it is possible to have the OpenEmbedded build system notice new recipe changes added to the SCM and then build the resulting packages that depend on the new recipes by using the latest versions. This only works for SCMs from which it is possible to get a sensible revision number for changes. Currently, you can do this with Apache Subversion (SVN), Git, and Bazaar (BZR) repositories.

> 如果您正在处理从外部源代码管理器（SCM）提取的配方，则可以让 OpenEmbedded 构建系统注意到添加到 SCM 的新配方更改，然后使用最新版本构建依赖于新配方的结果包。这只适用于 SCM，从中可以获得合理的更改修订号。目前，您可以使用 ApacheSubversion（SVN）、Git 和 Bazaar（BZR）存储库来实现这一点。

To enable this behavior, the `PV`{.interpreted-text role="term"} of the recipe needs to reference `SRCPV`{.interpreted-text role="term"}. Here is an example:

> 要启用此行为，配方的 `PV`｛.depreted text role=“term”｝需要引用 `SRCV`｛.repreted text role=“term“｝。以下是一个示例：

```
PV = "1.2.3+git${SRCPV}"
```

Then, you can add the following to your `local.conf`:

> 然后，您可以将以下内容添加到“local.conf”中：

```
SRCREV:pn-PN = "${AUTOREV}"
```

`PN`{.interpreted-text role="term"} is the name of the recipe for which you want to enable automatic source revision updating.

> `PN`｛.explored text role=“term”｝是要启用自动源修订更新的配方的名称。

If you do not want to update your local configuration file, you can add the following directly to the recipe to finish enabling the feature:

> 如果您不想更新本地配置文件，可以将以下内容直接添加到配方中，以完成启用该功能：

```
SRCREV = "${AUTOREV}"
```

The Yocto Project provides a distribution named `poky-bleeding`, whose configuration file contains the line:

> Yocto 项目提供了一个名为“poky bleing”的分发版，其配置文件包含以下行：

```
require conf/distro/include/poky-floating-revisions.inc
```

This line pulls in the listed include file that contains numerous lines of exactly that form:

> 这一行拉入列出的包含文件，该文件包含许多完全相同形式的行：

```
#SRCREV:pn-opkg-native ?= "${AUTOREV}"
#SRCREV:pn-opkg-sdk ?= "${AUTOREV}"
#SRCREV:pn-opkg ?= "${AUTOREV}"
#SRCREV:pn-opkg-utils-native ?= "${AUTOREV}"
#SRCREV:pn-opkg-utils ?= "${AUTOREV}"
SRCREV:pn-gconf-dbus ?= "${AUTOREV}"
SRCREV:pn-matchbox-common ?= "${AUTOREV}"
SRCREV:pn-matchbox-config-gtk ?= "${AUTOREV}"
SRCREV:pn-matchbox-desktop ?= "${AUTOREV}"
SRCREV:pn-matchbox-keyboard ?= "${AUTOREV}"
SRCREV:pn-matchbox-panel-2 ?= "${AUTOREV}"
SRCREV:pn-matchbox-themes-extra ?= "${AUTOREV}"
SRCREV:pn-matchbox-terminal ?= "${AUTOREV}"
SRCREV:pn-matchbox-wm ?= "${AUTOREV}"
SRCREV:pn-settings-daemon ?= "${AUTOREV}"
SRCREV:pn-screenshot ?= "${AUTOREV}"
. . .
```

These lines allow you to experiment with building a distribution that tracks the latest development source for numerous packages.

> 这些行允许您尝试构建一个跟踪众多包的最新开发源的分发版。

::: note
::: title
Note
:::

The `poky-bleeding` distribution is not tested on a regular basis. Keep this in mind if you use it.

> 没有定期测试“出血量过大”的分布。如果你使用它，请记住这一点。
> :::
