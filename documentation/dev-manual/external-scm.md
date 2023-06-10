---
tip: translate by openai@2023-06-10 10:47:42
...
---
title: Using an External SCM
----------------------------

If you\'re working on a recipe that pulls from an external Source Code Manager (SCM), it is possible to have the OpenEmbedded build system notice new recipe changes added to the SCM and then build the resulting packages that depend on the new recipes by using the latest versions. This only works for SCMs from which it is possible to get a sensible revision number for changes. Currently, you can do this with Apache Subversion (SVN), Git, and Bazaar (BZR) repositories.

> 如果您正在使用外部源代码管理器（SCM）来构建菜谱，则可以让 OpenEmbedded 构建系统注意到 SCM 中添加的新菜谱，然后使用最新版本构建依赖于新菜谱的结果软件包。这只适用于可以获得有意义的修订号以检测更改的 SCM。目前，您可以使用 Apache Subversion（SVN），Git 和 Bazaar（BZR）存储库来实现此目的。

To enable this behavior, the `PV`{.interpreted-text role="term"} of the recipe needs to reference `SRCPV`{.interpreted-text role="term"}. Here is an example:

> 要启用此行为，需要将食谱的 PV 引用 SRCPV。以下是一个例子：

```
PV = "1.2.3+git${SRCPV}"
```

Then, you can add the following to your `local.conf`:

```
SRCREV:pn-PN = "${AUTOREV}"
```

`PN`{.interpreted-text role="term"} is the name of the recipe for which you want to enable automatic source revision updating.

If you do not want to update your local configuration file, you can add the following directly to the recipe to finish enabling the feature:

```
SRCREV = "${AUTOREV}"
```

The Yocto Project provides a distribution named `poky-bleeding`, whose configuration file contains the line:

```
require conf/distro/include/poky-floating-revisions.inc
```

This line pulls in the listed include file that contains numerous lines of exactly that form:

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

::: note
::: title
Note
:::

The `poky-bleeding` distribution is not tested on a regular basis. Keep this in mind if you use it.
:::
