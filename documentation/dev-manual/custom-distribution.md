---
tip: translate by openai@2023-06-10 10:22:21
...
---
title: Creating Your Own Distribution
-------------------------------------

When you build an image using the Yocto Project and do not alter any distribution `Metadata`{.interpreted-text role="term"}, you are creating a Poky distribution. If you wish to gain more control over package alternative selections, compile-time options, and other low-level configurations, you can create your own distribution.

> 当您使用 Yocto 项目构建图像而不更改任何发行版 `Metadata` 时，您正在创建一个 Poky 发行版。如果您希望获得对包替代选择、编译时选项和其他低级配置的更多控制权，您可以创建自己的发行版。

To create your own distribution, the basic steps consist of creating your own distribution layer, creating your own distribution configuration file, and then adding any needed code and Metadata to the layer. The following steps provide some more detail:

> 创建自己的发行版，基本步骤包括创建自己的发行版层，创建自己的发行版配置文件，然后添加任何需要的代码和元数据到该层。以下步骤提供了更多的细节：

- *Create a layer for your new distro:* Create your distribution layer so that you can keep your Metadata and code for the distribution separate. It is strongly recommended that you create and use your own layer for configuration and code. Using your own layer as compared to just placing configurations in a `local.conf` configuration file makes it easier to reproduce the same build configuration when using multiple build machines. See the \"``dev-manual/layers:creating a general layer using the \`\`bitbake-layers\`\` script``{.interpreted-text role="ref"}\" section for information on how to quickly set up a layer.

> 创建一个新发行版的层：创建您的发行版层，以便可以将元数据和代码分开保存。强烈建议您创建并使用自己的层来配置和编码。与将配置放置在 `local.conf` 配置文件中相比，使用自己的层可以更容易地重现多台构建机器上的相同构建配置。有关如何快速设置层的信息，请参阅“使用 `bitbake-layers` 脚本创建一般层”部分。

- *Create the distribution configuration file:* The distribution configuration file needs to be created in the `conf/distro` directory of your layer. You need to name it using your distribution name (e.g. `mydistro.conf`).

> *创建分发配置文件：*需要在您的层的 `conf/distro` 目录下创建分发配置文件。您需要使用您的分发名称（例如 `mydistro.conf`）来命名它。

::: note
::: title
Note
:::

The `DISTRO`{.interpreted-text role="term"} variable in your `local.conf` file determines the name of your distribution.
:::

You can split out parts of your configuration file into include files and then \"require\" them from within your distribution configuration file. Be sure to place the include files in the `conf/distro/include` directory of your layer. A common example usage of include files would be to separate out the selection of desired version and revisions for individual recipes.

> 你可以把你的配置文件的部分内容拆分成包含文件，然后从你的发行版配置文件中“引用”它们。请务必将包含文件放置在你的层的 `conf/distro/include` 目录下。使用包含文件的一个常见的例子是把各个食谱所需的版本和修订分离开来。

Your configuration file needs to set the following required variables:

- `DISTRO_NAME`{.interpreted-text role="term"}
- `DISTRO_VERSION`{.interpreted-text role="term"}

These following variables are optional and you typically set them from the distribution configuration file:

- `DISTRO_FEATURES`{.interpreted-text role="term"}
- `DISTRO_EXTRA_RDEPENDS`{.interpreted-text role="term"}
- `DISTRO_EXTRA_RRECOMMENDS`{.interpreted-text role="term"}
- `TCLIBC`{.interpreted-text role="term"}

::: tip
::: title
Tip
:::

If you want to base your distribution configuration file on the very basic configuration from OE-Core, you can use `conf/distro/defaultsetup.conf` as a reference and just include variables that differ as compared to `defaultsetup.conf`. Alternatively, you can create a distribution configuration file from scratch using the `defaultsetup.conf` file or configuration files from another distribution such as Poky as a reference.

> 如果你想基于 OE-Core 的非常基础的配置文件来配置分发，你可以使用 `conf/distro/defaultsetup.conf` 作为参考，并且只包含与 `defaultsetup.conf` 不同的变量。或者，你可以从头开始创建一个分发配置文件，以 `defaultsetup.conf` 文件或其他分发（如 Poky）的配置文件为参考。
> :::

- *Provide miscellaneous variables:* Be sure to define any other variables for which you want to create a default or enforce as part of the distribution configuration. You can include nearly any variable from the `local.conf` file. The variables you use are not limited to the list in the previous bulleted item.

> - *提供其他变量：* 确保定义任何其他变量，您希望为其创建默认值或强制作为发行版配置的一部分。您可以包括来自 `local.conf` 文件的几乎任何变量。您使用的变量不限于前面项目中的列表。

- *Point to Your distribution configuration file:* In your `local.conf` file in the `Build Directory`{.interpreted-text role="term"}, set your `DISTRO`{.interpreted-text role="term"} variable to point to your distribution\'s configuration file. For example, if your distribution\'s configuration file is named `mydistro.conf`, then you point to it as follows:

> 在构建目录中的 `local.conf` 文件中，将 `DISTRO` 变量设置为指向您的发行版配置文件。例如，如果您的发行版配置文件名为 `mydistro.conf`，则可以如下指向它：

```
DISTRO = "mydistro"
```

- *Add more to the layer if necessary:* Use your layer to hold other information needed for the distribution:

  - Add recipes for installing distro-specific configuration files that are not already installed by another recipe. If you have distro-specific configuration files that are included by an existing recipe, you should add an append file (`.bbappend`) for those. For general information and recommendations on how to add recipes to your layer, see the \"`dev-manual/layers:creating your own layer`{.interpreted-text role="ref"}\" and \"`dev-manual/layers:following best practices when creating layers`{.interpreted-text role="ref"}\" sections.

> 添加特定发行版本未被其他配方安装的配置文件的配方。如果你有特定发行版本的配置文件被现有的配方包含，你应该为这些文件添加一个附加文件（`.bbappend`）。有关如何添加配方到你的层的一般信息和建议，请参阅“`dev-manual/layers:creating your own layer`{.interpreted-text role="ref"}”和“`dev-manual/layers:following best practices when creating layers`{.interpreted-text role="ref"}”章节。

- Add any image recipes that are specific to your distribution.
- Add a `psplash` append file for a branded splash screen, using the `SPLASH_IMAGES`{.interpreted-text role="term"} variable.
- Add any other append files to make custom changes that are specific to individual recipes.

For information on append files, see the \"`dev-manual/layers:appending other layers metadata with your layer`{.interpreted-text role="ref"}\" section.

> 有关附加文件的信息，请参见“dev-manual / layers：使用您的图层附加其他图层元数据”部分。
