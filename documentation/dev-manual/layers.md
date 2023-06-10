---
tip: translate by openai@2023-06-10 10:54:14
...
---
title: Understanding and Creating Layers
----------------------------------------

The OpenEmbedded build system supports organizing `Metadata`{.interpreted-text role="term"} into multiple layers. Layers allow you to isolate different types of customizations from each other. For introductory information on the Yocto Project Layer Model, see the \"`overview-manual/yp-intro:the yocto project layer model`{.interpreted-text role="ref"}\" section in the Yocto Project Overview and Concepts Manual.

> 开放式嵌入式构建系统支持将“元数据”组织成多个层次。层次允许您将不同类型的定制隔离开来。有关 Yocto 项目层模型的介绍性信息，请参阅 Yocto 项目概述和概念手册中的“Yocto 项目层模型概述”部分。

# Creating Your Own Layer

::: note
::: title
Note
:::

It is very easy to create your own layers to use with the OpenEmbedded build system, as the Yocto Project ships with tools that speed up creating layers. This section describes the steps you perform by hand to create layers so that you can better understand them. For information about the layer-creation tools, see the \"``bsp-guide/bsp:creating a new bsp layer using the \`\`bitbake-layers\`\` script``{.interpreted-text role="ref"}\" section in the Yocto Project Board Support Package (BSP) Developer\'s Guide and the \"``dev-manual/layers:creating a general layer using the \`\`bitbake-layers\`\` script``{.interpreted-text role="ref"}\" section further down in this manual.

> 很容易使用 OpenEmbedded 构建系统创建自己的图层，因为 Yocto Project 配备了加快创建图层的工具。本节介绍手动执行的步骤，以便您可以更好地理解它们。有关图层创建工具的信息，请参阅 Yocto Project 板支持包（BSP）开发者指南中的“使用“bitbake-layers”脚本创建新的 BSP 图层”部分，以及本手册中下面的“使用“bitbake-layers”脚本创建通用图层”部分。
> :::

Follow these general steps to create your layer without using tools:

1. *Check Existing Layers:* Before creating a new layer, you should be sure someone has not already created a layer containing the Metadata you need. You can see the :oe_layerindex:[OpenEmbedded Metadata Index \<\>]{.title-ref} for a list of layers from the OpenEmbedded community that can be used in the Yocto Project. You could find a layer that is identical or close to what you need.

> 在创建新层之前，您应该确保没有人已经创建了包含所需元数据的层。您可以查看 OpenEmbedded 元数据索引 <>，获取来自 OpenEmbedded 社区的层列表，这些层可以在 Yocto 项目中使用。您可以找到与您需要的完全相同或接近的层。

2. *Create a Directory:* Create the directory for your layer. When you create the layer, be sure to create the directory in an area not associated with the Yocto Project `Source Directory`{.interpreted-text role="term"} (e.g. the cloned `poky` repository).

> 2. *创建目录：*为您的层创建目录。创建层时，请确保在与 Yocto 项目源目录无关的区域（例如克隆的 `poky` 存储库）中创建目录。

While not strictly required, prepend the name of the directory with the string \"meta-\". For example:

```
meta-mylayer
meta-GUI_xyz
meta-mymachine
```

With rare exceptions, a layer\'s name follows this form:

```
meta-root_name
```

Following this layer naming convention can save you trouble later when tools, components, or variables \"assume\" your layer name begins with \"meta-\". A notable example is in configuration files as shown in the following step where layer names without the \"meta-\" string are appended to several variables used in the configuration.

> 遵循这种图层命名约定可以使您以后避免麻烦，因为工具、组件或变量“假定”您的图层名称以“meta-”开头。 一个显着的例子是在下一步中显示的配置文件中，没有“meta-”字符串的图层名称被附加到用于配置的几个变量中。

3. *Create a Layer Configuration File:* Inside your new layer folder, you need to create a `conf/layer.conf` file. It is easiest to take an existing layer configuration file and copy that to your layer\'s `conf` directory and then modify the file as needed.

> 在新层文件夹中，您需要创建一个 `conf/layer.conf` 文件。最简单的方法是将现有的层配置文件复制到您层的 `conf` 目录，然后根据需要修改该文件。

The `meta-yocto-bsp/conf/layer.conf` file in the Yocto Project :yocto\_[git:%60Source](git:%60Source) Repositories \</poky/tree/meta-yocto-bsp/conf\>\` demonstrates the required syntax. For your layer, you need to replace \"yoctobsp\" with a unique identifier for your layer (e.g. \"machinexyz\" for a layer named \"meta-machinexyz\"):

> 文件 `meta-yocto-bsp/conf/layer.conf` 在 Yocto 项目的 [git:Source](git:Source) 存储库 <poky/tree/meta-yocto-bsp/conf> 中展示了所需的语法。对于您的层，您需要将“yoctobsp”替换为您层的唯一标识符（例如名为“meta-machinexyz”的层的“machinexyz”）：

```
# We have a conf and classes directory, add to BBPATH
BBPATH .= ":${LAYERDIR}"

# We have recipes-* directories, add to BBFILES
BBFILES += "${LAYERDIR}/recipes-*/*/*.bb \
            ${LAYERDIR}/recipes-*/*/*.bbappend"

BBFILE_COLLECTIONS += "yoctobsp"
BBFILE_PATTERN_yoctobsp = "^${LAYERDIR}/"
BBFILE_PRIORITY_yoctobsp = "5"
LAYERVERSION_yoctobsp = "4"
LAYERSERIES_COMPAT_yoctobsp = "dunfell"
```

Following is an explanation of the layer configuration file:

- `BBPATH`{.interpreted-text role="term"}: Adds the layer\'s root directory to BitBake\'s search path. Through the use of the `BBPATH`{.interpreted-text role="term"} variable, BitBake locates class files (`.bbclass`), configuration files, and files that are included with `include` and `require` statements. For these cases, BitBake uses the first file that matches the name found in `BBPATH`{.interpreted-text role="term"}. This is similar to the way the `PATH` variable is used for binaries. It is recommended, therefore, that you use unique class and configuration filenames in your custom layer.

> BBPATH：将层的根目录添加到 BitBake 的搜索路径中。通过使用 BBPATH 变量，BitBake 可以定位类文件（.bbclass），配置文件以及使用 include 和 require 语句包含的文件。在这些情况下，BitBake 使用在 BBPATH 中找到的第一个与名称匹配的文件。这与 PATH 变量用于二进制文件的方式类似。因此，建议您在自定义层中使用唯一的类和配置文件名称。

- `BBFILES`{.interpreted-text role="term"}: Defines the location for all recipes in the layer.
- `BBFILE_COLLECTIONS`{.interpreted-text role="term"}: Establishes the current layer through a unique identifier that is used throughout the OpenEmbedded build system to refer to the layer. In this example, the identifier \"yoctobsp\" is the representation for the container layer named \"meta-yocto-bsp\".

> - `BBFILE_COLLECTIONS`：通过唯一标识符建立当前层，该标识符在 OpenEmbedded 构建系统中被用来指代该层。在本例中，标识符“yoctobsp”代表容器层名为“meta-yocto-bsp”。

- `BBFILE_PATTERN`{.interpreted-text role="term"}: Expands immediately during parsing to provide the directory of the layer.
- `BBFILE_PRIORITY`{.interpreted-text role="term"}: Establishes a priority to use for recipes in the layer when the OpenEmbedded build finds recipes of the same name in different layers.

> - BBFILE_PRIORITY：当 OpenEmbedded 构建在不同的层中发现同名的配方时，为层中的配方设置优先级。

- `LAYERVERSION`{.interpreted-text role="term"}: Establishes a version number for the layer. You can use this version number to specify this exact version of the layer as a dependency when using the `LAYERDEPENDS`{.interpreted-text role="term"} variable.

> - `LAYERVERSION`：为图层设置一个版本号。您可以使用此版本号在使用 `LAYERDEPENDS` 变量时将此图层的确切版本指定为依赖项。

- `LAYERDEPENDS`{.interpreted-text role="term"}: Lists all layers on which this layer depends (if any).
- `LAYERSERIES_COMPAT`{.interpreted-text role="term"}: Lists the :yocto_wiki:[Yocto Project \</Releases\>]{.title-ref} releases for which the current version is compatible. This variable is a good way to indicate if your particular layer is current.

> -`LAYERSERIES_COMPAT`：列出当前版本兼容的 Yocto 项目发布版本。这个变量是一个很好的方式来表明你的特定层是否是当前的。

4. *Add Content:* Depending on the type of layer, add the content. If the layer adds support for a machine, add the machine configuration in a `conf/machine/` file within the layer. If the layer adds distro policy, add the distro configuration in a `conf/distro/` file within the layer. If the layer introduces new recipes, put the recipes you need in `recipes-*` subdirectories within the layer.

> 4. *添加内容：* 根据层的类型，添加内容。如果层添加机器支持，在层内的 `conf/machine/` 文件中添加机器配置。如果层添加发行版策略，在层内的 `conf/distro/` 文件中添加发行版配置。如果层引入新的食谱，在层内的 `recipes-*` 子目录中添加需要的食谱。

::: note
::: title
Note
:::

For an explanation of layer hierarchy that is compliant with the Yocto Project, see the \"`bsp-guide/bsp:example filesystem layout`{.interpreted-text role="ref"}\" section in the Yocto Project Board Support Package (BSP) Developer\'s Guide.

> 为了了解符合 Yocto 项目的层次结构的解释，请参见 Yocto 项目板支持包(BSP)开发者指南中的“bsp-guide / bsp：示例文件系统布局”部分。
> :::

5. *Optionally Test for Compatibility:* If you want permission to use the Yocto Project Compatibility logo with your layer or application that uses your layer, perform the steps to apply for compatibility. See the \"`dev-manual/layers:making sure your layer is compatible with yocto project`{.interpreted-text role="ref"}\" section for more information.

> 5. *可选择性地测试兼容性：* 如果您想获得使用 Yocto 项目兼容性标志与您的层或使用您的层的应用程序的权限，请执行申请兼容性的步骤。有关更多信息，请参见“dev-manual / layers：确保您的层与 yocto 项目兼容”部分。

# Following Best Practices When Creating Layers

To create layers that are easier to maintain and that will not impact builds for other machines, you should consider the information in the following list:

> 为了创建更容易维护的层，而不会影响其他机器的构建，您应该考虑以下列表中的信息：

- *Avoid \"Overlaying\" Entire Recipes from Other Layers in Your Configuration:* In other words, do not copy an entire recipe into your layer and then modify it. Rather, use an append file (`.bbappend`) to override only those parts of the original recipe you need to modify.

> 避免在配置中“叠加”整个配方：换句话说，不要将整个配方复制到您的层中，然后修改它。而是使用附加文件（`.bbappend`）仅覆盖您需要修改的原始配方的部分。

- *Avoid Duplicating Include Files:* Use append files (`.bbappend`) for each recipe that uses an include file. Or, if you are introducing a new recipe that requires the included file, use the path relative to the original layer directory to refer to the file. For example, use `require recipes-core/`[package]{.title-ref}`/`[file]{.title-ref}`.inc` instead of `require` [file]{.title-ref}`.inc`. If you\'re finding you have to overlay the include file, it could indicate a deficiency in the include file in the layer to which it originally belongs. If this is the case, you should try to address that deficiency instead of overlaying the include file. For example, you could address this by getting the maintainer of the include file to add a variable or variables to make it easy to override the parts needing to be overridden.

> 避免重复包含文件：对于使用包含文件的每个配方，使用附加文件（`.bbappend`）。或者，如果您引入了一个需要包含文件的新配方，请使用相对于原始图层目录的路径来引用该文件。例如，使用 `require recipes-core/`[package]{.title-ref}`/`[file]{.title-ref}`.inc` 而不是 `require` [file]{.title-ref}`.inc`。如果您发现必须叠加包含文件，可能表明原始层中的包含文件存在缺陷。如果是这种情况，您应该尝试解决该缺陷，而不是叠加包含文件。例如，您可以通过让包含文件的维护者添加一个或多个变量来解决此问题，以便轻松覆盖需要覆盖的部分。

- *Structure Your Layers:* Proper use of overrides within append files and placement of machine-specific files within your layer can ensure that a build is not using the wrong Metadata and negatively impacting a build for a different machine. Following are some examples:

> 结构化您的层：在附加文件中适当使用覆盖，并将特定于机器的文件放置在您的层中，可以确保构建不使用错误的元数据，从而不会对不同机器的构建产生负面影响。下面是一些例子：

- *Modify Variables to Support a Different Machine:* Suppose you have a layer named `meta-one` that adds support for building machine \"one\". To do so, you use an append file named `base-files.bbappend` and create a dependency on \"foo\" by altering the `DEPENDS`{.interpreted-text role="term"} variable:

> - *修改变量以支持不同机器：* 假设您有一个名为 `meta-one` 的层，它为构建机器“one”提供支持。为此，您使用名为 `base-files.bbappend` 的附加文件，并通过更改 `DEPENDS`{.interpreted-text role="term"}变量来创建对“foo”的依赖：

```
```
DEPENDS = "foo"
```

The dependency is created during any build that includes the layer `meta-one`. However, you might not want this dependency for all machines. For example, suppose you are building for machine \"two\" but your `bblayers.conf` file has the `meta-one` layer included. During the build, the `base-files` for machine \"two\" will also have the dependency on `foo`.

To make sure your changes apply only when building machine \"one\", use a machine override with the `DEPENDS`{.interpreted-text role="term"} statement:

```
DEPENDS:one = "foo"
```

You should follow the same strategy when using `:append` and `:prepend` operations:

```
DEPENDS:append:one = " foo"
DEPENDS:prepend:one = "foo "
```

As an actual example, here\'s a snippet from the generic kernel include file `linux-yocto.inc`, wherein the kernel compile and link options are adjusted in the case of a subset of the supported architectures:

```
DEPENDS:append:aarch64 = " libgcc"
KERNEL_CC:append:aarch64 = " ${TOOLCHAIN_OPTIONS}"
KERNEL_LD:append:aarch64 = " ${TOOLCHAIN_OPTIONS}"

DEPENDS:append:nios2 = " libgcc"
KERNEL_CC:append:nios2 = " ${TOOLCHAIN_OPTIONS}"
KERNEL_LD:append:nios2 = " ${TOOLCHAIN_OPTIONS}"

DEPENDS:append:arc = " libgcc"
KERNEL_CC:append:arc = " ${TOOLCHAIN_OPTIONS}"
KERNEL_LD:append:arc = " ${TOOLCHAIN_OPTIONS}"

KERNEL_FEATURES:append:qemuall=" features/debug/printk.scc"
```
```

- *Place Machine-Specific Files in Machine-Specific Locations:* When you have a base recipe, such as `base-files.bb`, that contains a `SRC_URI`{.interpreted-text role="term"} statement to a file, you can use an append file to cause the build to use your own version of the file. For example, an append file in your layer at `meta-one/recipes-core/base-files/base-files.bbappend` could extend `FILESPATH`{.interpreted-text role="term"} using `FILESEXTRAPATHS`{.interpreted-text role="term"} as follows:

> *将特定于机器的文件放置在特定于机器的位置：*当您有一个基本配方，例如 `base-files.bb`，其中包含一个 `SRC_URI`{.interpreted-text role="term"} 语句到一个文件，您可以使用附加文件来使构建使用您自己的版本的文件。例如，您在层中的 `meta-one/recipes-core/base-files/base-files.bbappend` 中的附加文件可以使用 `FILESEXTRAPATHS`{.interpreted-text role="term"} 扩展 `FILESPATH`{.interpreted-text role="term"}，如下所示：

```
```
FILESEXTRAPATHS:prepend := "${THISDIR}/${BPN}:"
```

The build for machine \"one\" will pick up your machine-specific file as long as you have the file in `meta-one/recipes-core/base-files/base-files/`. However, if you are building for a different machine and the `bblayers.conf` file includes the `meta-one` layer and the location of your machine-specific file is the first location where that file is found according to `FILESPATH`{.interpreted-text role="term"}, builds for all machines will also use that machine-specific file.

You can make sure that a machine-specific file is used for a particular machine by putting the file in a subdirectory specific to the machine. For example, rather than placing the file in `meta-one/recipes-core/base-files/base-files/` as shown above, put it in `meta-one/recipes-core/base-files/base-files/one/`. Not only does this make sure the file is used only when building for machine \"one\", but the build process locates the file more quickly.

In summary, you need to place all files referenced from `SRC_URI`{.interpreted-text role="term"} in a machine-specific subdirectory within the layer in order to restrict those files to machine-specific builds.
```

- *Perform Steps to Apply for Yocto Project Compatibility:* If you want permission to use the Yocto Project Compatibility logo with your layer or application that uses your layer, perform the steps to apply for compatibility. See the \"`dev-manual/layers:making sure your layer is compatible with yocto project`{.interpreted-text role="ref"}\" section for more information.

> 如果您想使用 Yocto Project 兼容标志与您的层或使用您的层的应用程序一起使用，请执行申请兼容性的步骤。有关更多信息，请参见“dev-manual/layers：确保您的层与 yocto project 兼容”部分。

- *Follow the Layer Naming Convention:* Store custom layers in a Git repository that use the `meta-layer_name` format.
- *Group Your Layers Locally:* Clone your repository alongside other cloned `meta` directories from the `Source Directory`{.interpreted-text role="term"}.

> *在本地分组你的层：* 从 `源目录` 中克隆其他克隆的 `meta` 目录旁边克隆你的存储库。

# Making Sure Your Layer is Compatible With Yocto Project

When you create a layer used with the Yocto Project, it is advantageous to make sure that the layer interacts well with existing Yocto Project layers (i.e. the layer is compatible with the Yocto Project). Ensuring compatibility makes the layer easy to be consumed by others in the Yocto Project community and could allow you permission to use the Yocto Project Compatible Logo.

> 当你使用 Yocto 项目创建一个层时，最好确保该层与现有的 Yocto 项目层很好地交互（即该层与 Yocto 项目兼容）。确保兼容性可以使该层更容易被 Yocto 项目社区中的其他人使用，并可以允许你使用 Yocto 项目兼容标志。

::: note
::: title
Note
:::

Only Yocto Project member organizations are permitted to use the Yocto Project Compatible Logo. The logo is not available for general use. For information on how to become a Yocto Project member organization, see the :yocto_home:[Yocto Project Website \<\>]{.title-ref}.

> 只有 Yocto 项目成员组织才能使用 Yocto 项目兼容徽标。该徽标不供一般使用。关于如何成为 Yocto 项目成员组织的信息，请参见：yocto_home:[Yocto 项目网站\<\>]{.title-ref}。
> :::

The Yocto Project Compatibility Program consists of a layer application process that requests permission to use the Yocto Project Compatibility Logo for your layer and application. The process consists of two parts:

> 该 Yocto 项目兼容性计划包括一个层应用程序过程，要求获得使用 Yocto 项目兼容性徽标的许可，以用于您的层和应用程序。该过程包括两个部分：

1. Successfully passing a script (`yocto-check-layer`) that when run against your layer, tests it against constraints based on experiences of how layers have worked in the real world and where pitfalls have been found. Getting a \"PASS\" result from the script is required for successful compatibility registration.

> 通过脚本（`yocto-check-layer`）的成功运行，将您的层与基于实际世界中图层工作方式及发现的陷阱的约束进行测试。脚本的“PASS”结果是兼容注册成功的必要条件。

2. Completion of an application acceptance form, which you can find at :yocto_home:[/webform/yocto-project-compatible-registration]{.title-ref}.

To be granted permission to use the logo, you need to satisfy the following:

- Be able to check the box indicating that you got a \"PASS\" when running the script against your layer.
- Answer \"Yes\" to the questions on the form or have an acceptable explanation for any questions answered \"No\".
- Be a Yocto Project Member Organization.

The remainder of this section presents information on the registration form and on the `yocto-check-layer` script.

## Yocto Project Compatible Program Application

Use the form to apply for your layer\'s approval. Upon successful application, you can use the Yocto Project Compatibility Logo with your layer and the application that uses your layer.

> 使用表格申请您层的批准。成功申请后，您可以使用 Yocto 项目兼容标志与您的层和使用您层的应用程序一起使用。

To access the form, use this link: :yocto_home:[/webform/yocto-project-compatible-registration]{.title-ref}. Follow the instructions on the form to complete your application.

> 访问表格，请使用此链接：:yocto_home:[/webform/yocto-project-compatible-registration]{.title-ref}。按照表格上的说明完成申请。

The application consists of the following sections:

- *Contact Information:* Provide your contact information as the fields require. Along with your information, provide the released versions of the Yocto Project for which your layer is compatible.

> - *联系信息：*按照要求填写您的联系信息。除了您的信息外，还需提供与 Yocto Project 兼容的发布版本。

- *Acceptance Criteria:* Provide \"Yes\" or \"No\" answers for each of the items in the checklist. There is space at the bottom of the form for any explanations for items for which you answered \"No\".

> - *可接受标准：* 为清单中的每个项目提供“是”或“否”的答案。对于您回答“否”的项目，表格底部有空间可以提供解释。

- *Recommendations:* Provide answers for the questions regarding Linux kernel use and build success.

## `yocto-check-layer` Script

The `yocto-check-layer` script provides you a way to assess how compatible your layer is with the Yocto Project. You should run this script prior to using the form to apply for compatibility as described in the previous section. You need to achieve a \"PASS\" result in order to have your application form successfully processed.

> 脚本 `yocto-check-layer` 为您提供了一种评估您的层与 Yocto 项目的兼容性的方法。在使用前一节中描述的申请兼容性的表格之前，您应该运行此脚本。为了使您的申请表格成功处理，您需要获得“PASS”的结果。

The script divides tests into three areas: COMMON, BSP, and DISTRO. For example, given a distribution layer (DISTRO), the layer must pass both the COMMON and DISTRO related tests. Furthermore, if your layer is a BSP layer, the layer must pass the COMMON and BSP set of tests.

> 脚本将测试分为三个领域：COMMON，BSP 和 DISTRO。例如，给定一个发行层（DISTRO），该层必须通过 COMMON 和 DISTRO 相关的测试。此外，如果您的层是 BSP 层，则该层必须通过 COMMON 和 BSP 的测试集。

To execute the script, enter the following commands from your build directory:

```
$ source oe-init-build-env
$ yocto-check-layer your_layer_directory
```

Be sure to provide the actual directory for your layer as part of the command.

Entering the command causes the script to determine the type of layer and then to execute a set of specific tests against the layer. The following list overviews the test:

> 输入命令会导致脚本确定层的类型，然后对层执行一系列特定的测试。 下面的列表概述了测试：

- `common.test_readme`: Tests if a `README` file exists in the layer and the file is not empty.
- `common.test_parse`: Tests to make sure that BitBake can parse the files without error (i.e. `bitbake -p`).
- `common.test_show_environment`: Tests that the global or per-recipe environment is in order without errors (i.e. `bitbake -e`).
- `common.test_world`: Verifies that `bitbake world` works.
- `common.test_signatures`: Tests to be sure that BSP and DISTRO layers do not come with recipes that change signatures.
- `common.test_layerseries_compat`: Verifies layer compatibility is set properly.
- `bsp.test_bsp_defines_machines`: Tests if a BSP layer has machine configurations.
- `bsp.test_bsp_no_set_machine`: Tests to ensure a BSP layer does not set the machine when the layer is added.
- `bsp.test_machine_world`: Verifies that `bitbake world` works regardless of which machine is selected.
- `bsp.test_machine_signatures`: Verifies that building for a particular machine affects only the signature of tasks specific to that machine.
- `distro.test_distro_defines_distros`: Tests if a DISTRO layer has distro configurations.
- `distro.test_distro_no_set_distros`: Tests to ensure a DISTRO layer does not set the distribution when the layer is added.

# Enabling Your Layer

Before the OpenEmbedded build system can use your new layer, you need to enable it. To enable your layer, simply add your layer\'s path to the `BBLAYERS`{.interpreted-text role="term"} variable in your `conf/bblayers.conf` file, which is found in the `Build Directory`{.interpreted-text role="term"}. The following example shows how to enable your new `meta-mylayer` layer (note how your new layer exists outside of the official `poky` repository which you would have checked out earlier):

> 在 OpenEmbedded 构建系统使用您的新层之前，您需要启用它。要启用您的层，只需将您的层路径添加到您的 conf / bblayers.conf 文件中的 BBLAYERS 变量中，该文件位于 Build Directory 中。以下示例显示如何启用新的 meta-mylayer 层（注意，您的新层位于先前您已检查出的官方 poky 存储库之外）：

```
# POKY_BBLAYERS_CONF_VERSION is increased each time build/conf/bblayers.conf
# changes incompatibly
POKY_BBLAYERS_CONF_VERSION = "2"
BBPATH = "${TOPDIR}"
BBFILES ?= ""
BBLAYERS ?= " \
    /home/user/poky/meta \
    /home/user/poky/meta-poky \
    /home/user/poky/meta-yocto-bsp \
    /home/user/mystuff/meta-mylayer \
    "
```

BitBake parses each `conf/layer.conf` file from the top down as specified in the `BBLAYERS`{.interpreted-text role="term"} variable within the `conf/bblayers.conf` file. During the processing of each `conf/layer.conf` file, BitBake adds the recipes, classes and configurations contained within the particular layer to the source directory.

> BitBake 按照 `conf/bblayers.conf` 文件中的 `BBLAYERS` 变量指定的顺序从上到下解析每个 `conf/layer.conf` 文件。在处理每个 `conf/layer.conf` 文件的过程中，BitBake 将特定层中包含的配方、类和配置添加到源目录中。

# Appending Other Layers Metadata With Your Layer

A recipe that appends Metadata to another recipe is called a BitBake append file. A BitBake append file uses the `.bbappend` file type suffix, while the corresponding recipe to which Metadata is being appended uses the `.bb` file type suffix.

> 一种向另一种食谱添加元数据的食谱被称为 BitBake 附加文件。BitBake 附加文件使用 `.bbappend` 文件类型后缀，而要添加元数据的相应食谱使用 `.bb` 文件类型后缀。

You can use a `.bbappend` file in your layer to make additions or changes to the content of another layer\'s recipe without having to copy the other layer\'s recipe into your layer. Your `.bbappend` file resides in your layer, while the main `.bb` recipe file to which you are appending Metadata resides in a different layer.

> 你可以在你的层中使用 `.bbappend` 文件来对另一层的配方内容做出添加或更改，而无需将另一层的配方复制到你的层中。你的 `.bbappend` 文件位于你的层中，而你要附加元数据的主 `.bb` 配方文件则位于另一层中。

Being able to append information to an existing recipe not only avoids duplication, but also automatically applies recipe changes from a different layer into your layer. If you were copying recipes, you would have to manually merge changes as they occur.

> 能够将信息附加到现有配方不仅可以避免重复，而且还可以自动将来自不同层的配方更改应用到您的层中。如果您复制配方，则必须在发生变化时手动合并。

When you create an append file, you must use the same root name as the corresponding recipe file. For example, the append file `someapp_3.1.bbappend` must apply to `someapp_3.1.bb`. This means the original recipe and append filenames are version number-specific. If the corresponding recipe is renamed to update to a newer version, you must also rename and possibly update the corresponding `.bbappend` as well. During the build process, BitBake displays an error on starting if it detects a `.bbappend` file that does not have a corresponding recipe with a matching name. See the `BB_DANGLINGAPPENDS_WARNONLY`{.interpreted-text role="term"} variable for information on how to handle this error.

> 当你创建一个附加文件时，你必须使用与相应的配方文件相同的根名称。例如，附加文件 `someapp_3.1.bbappend` 必须应用于 `someapp_3.1.bb`。这意味着原始配方和附加文件名是特定于版本号的。如果相应的配方被重命名以更新到更新版本，你也必须重命名并可能更新相应的 `.bbappend` 文件。在构建过程中，如果 BitBake 检测到一个没有相应配方和匹配名称的 `.bbappend` 文件，它会在启动时显示一个错误。有关如何处理此错误的信息，请参阅 `BB_DANGLINGAPPENDS_WARNONLY`{.interpreted-text role="term"}变量。

## Overlaying a File Using Your Layer

As an example, consider the main formfactor recipe and a corresponding formfactor append file both from the `Source Directory`{.interpreted-text role="term"}. Here is the main formfactor recipe, which is named `formfactor_0.0.bb` and located in the \"meta\" layer at `meta/recipes-bsp/formfactor`:

> 作为一个例子，考虑来自“源目录”的主要形式因子配方和相应的形式因子附加文件。这是位于“meta”层中的主要形式因子配方，名为“formfactor_0.0.bb”，位于“meta / recipes-bsp / formfactor”：

```
SUMMARY = "Device formfactor information"
DESCRIPTION = "A formfactor configuration file provides information about the \
target hardware for which the image is being built and information that the \
build system cannot obtain from other sources such as the kernel."
SECTION = "base"
LICENSE = "MIT"
LIC_FILES_CHKSUM = "file://${COREBASE}/meta/COPYING.MIT;md5=3da9cfbcb788c80a0384361b4de20420"
PR = "r45"

SRC_URI = "file://config file://machconfig"
S = "${WORKDIR}"

PACKAGE_ARCH = "${MACHINE_ARCH}"
INHIBIT_DEFAULT_DEPS = "1"

do_install() {
    # Install file only if it has contents
        install -d ${D}${sysconfdir}/formfactor/
        install -m 0644 ${S}/config ${D}${sysconfdir}/formfactor/
    if [ -s "${S}/machconfig" ]; then
            install -m 0644 ${S}/machconfig ${D}${sysconfdir}/formfactor/
    fi
}
```

In the main recipe, note the `SRC_URI`{.interpreted-text role="term"} variable, which tells the OpenEmbedded build system where to find files during the build.

> 在主要食谱中，注意 `SRC_URI` 变量，它告诉 OpenEmbedded 构建系统在构建过程中去哪里找文件。

Following is the append file, which is named `formfactor_0.0.bbappend` and is from the Raspberry Pi BSP Layer named `meta-raspberrypi`. The file is in the layer at `recipes-bsp/formfactor`:

> 以下是附加文件，名为 `formfactor_0.0.bbappend`，来自名为 `meta-raspberrypi` 的 Raspberry Pi BSP 层。该文件位于层的 `recipes-bsp/formfactor` 中：

```
FILESEXTRAPATHS:prepend := "${THISDIR}/${PN}:"
```

By default, the build system uses the `FILESPATH`{.interpreted-text role="term"} variable to locate files. This append file extends the locations by setting the `FILESEXTRAPATHS`{.interpreted-text role="term"} variable. Setting this variable in the `.bbappend` file is the most reliable and recommended method for adding directories to the search path used by the build system to find files.

> 默认情况下，构建系统使用 `FILESPATH` 变量来定位文件。该附加文件通过设置 `FILESEXTRAPATHS` 变量来扩展位置。将此变量设置在 `.bbappend` 文件中是构建系统查找文件时使用的最可靠和推荐的搜索路径增加目录的方法。

The statement in this example extends the directories to include `${``THISDIR`{.interpreted-text role="term"}`}/${``PN`{.interpreted-text role="term"}`}`, which resolves to a directory named `formfactor` in the same directory in which the append file resides (i.e. `meta-raspberrypi/recipes-bsp/formfactor`. This implies that you must have the supporting directory structure set up that will contain any files or patches you will be including from the layer.

> 在此示例中，该声明将目录扩展为包括 `${``THISDIR`{.interpreted-text role="term"}`}/${``PN`{.interpreted-text role="term"}`}`，该目录将解析为附加文件所在目录（即 `meta-raspberrypi/recipes-bsp/formfactor`）中名为 `formfactor` 的目录。这意味着您必须设置支持目录结构，以包含来自层的任何文件或补丁。

Using the immediate expansion assignment operator `:=` is important because of the reference to `THISDIR`{.interpreted-text role="term"}. The trailing colon character is important as it ensures that items in the list remain colon-separated.

> 使用即時展開賦值運算符 `:=` 很重要，因為它與 `THISDIR` 有關。尾部的冒號字符很重要，因為它確保列表中的項目保持以冒號分隔。

::: note
::: title
Note
:::

BitBake automatically defines the `THISDIR`{.interpreted-text role="term"} variable. You should never set this variable yourself. Using \":prepend\" as part of the `FILESEXTRAPATHS`{.interpreted-text role="term"} ensures your path will be searched prior to other paths in the final list.

> BitBake 会自动定义“THISDIR”变量。你不应该自己设置这个变量。在“FILESEXTRAPATHS”中使用“:prepend”可以确保你的路径在最终列表中搜索的优先级更高。

Also, not all append files add extra files. Many append files simply allow to add build options (e.g. `systemd`). For these cases, your append file would not even use the `FILESEXTRAPATHS`{.interpreted-text role="term"} statement.

> 此外，并不是所有的附加文件都会添加额外的文件。许多附加文件只是允许添加构建选项（例如 `systemd`）。在这些情况下，您的附加文件甚至不会使用 `FILESEXTRAPATHS` 语句。
> :::

The end result of this `.bbappend` file is that on a Raspberry Pi, where `rpi` will exist in the list of `OVERRIDES`{.interpreted-text role="term"}, the file `meta-raspberrypi/recipes-bsp/formfactor/formfactor/rpi/machconfig` will be used during `ref-tasks-fetch`{.interpreted-text role="ref"} and the test for a non-zero file size in `ref-tasks-install`{.interpreted-text role="ref"} will return true, and the file will be installed.

> 结果是，在 Raspberry Pi 上，当 OVERRIDES 列表中存在 rpi 时，在 ref-tasks-fetch 中会使用 meta-raspberrypi/recipes-bsp/formfactor/formfactor/rpi/machconfig 文件，而 ref-tasks-install 中的非零文件大小测试将返回 true，并且该文件将被安装。

## Installing Additional Files Using Your Layer

As another example, consider the main `xserver-xf86-config` recipe and a corresponding `xserver-xf86-config` append file both from the `Source Directory`{.interpreted-text role="term"}. Here is the main `xserver-xf86-config` recipe, which is named `xserver-xf86-config_0.1.bb` and located in the \"meta\" layer at `meta/recipes-graphics/xorg-xserver`:

> 作为另一个例子，考虑主要的“xserver-xf86-config”配方以及来自“源目录”的相应的“xserver-xf86-config”附加文件。这里是主要的“xserver-xf86-config”配方，它的名称是“xserver-xf86-config_0.1.bb”，位于“meta”层的“meta / recipes-graphics / xorg-xserver”中：

```
SUMMARY = "X.Org X server configuration file"
HOMEPAGE = "http://www.x.org"
SECTION = "x11/base"
LICENSE = "MIT"
LIC_FILES_CHKSUM = "file://${COREBASE}/meta/COPYING.MIT;md5=3da9cfbcb788c80a0384361b4de20420"
PR = "r33"

SRC_URI = "file://xorg.conf"

S = "${WORKDIR}"

CONFFILES:${PN} = "${sysconfdir}/X11/xorg.conf"

PACKAGE_ARCH = "${MACHINE_ARCH}"
ALLOW_EMPTY:${PN} = "1"

do_install () {
 if test -s ${WORKDIR}/xorg.conf; then
     install -d ${D}/${sysconfdir}/X11
     install -m 0644 ${WORKDIR}/xorg.conf ${D}/${sysconfdir}/X11/
 fi
}
```

Following is the append file, which is named `xserver-xf86-config_%.bbappend` and is from the Raspberry Pi BSP Layer named `meta-raspberrypi`. The file is in the layer at `recipes-graphics/xorg-xserver`:

> 以下是附加文件，命名为“xserver-xf86-config_％.bbappend”，来自名为“meta-raspberrypi”的 Raspberry Pi BSP 层。该文件位于层中的“recipes-graphics / xorg-xserver”中：

```
FILESEXTRAPATHS:prepend := "${THISDIR}/${PN}:"

SRC_URI:append:rpi = " \
    file://xorg.conf.d/98-pitft.conf \
    file://xorg.conf.d/99-calibration.conf \
"
do_install:append:rpi () {
    PITFT="${@bb.utils.contains("MACHINE_FEATURES", "pitft", "1", "0", d)}"
    if [ "${PITFT}" = "1" ]; then
        install -d ${D}/${sysconfdir}/X11/xorg.conf.d/
        install -m 0644 ${WORKDIR}/xorg.conf.d/98-pitft.conf ${D}/${sysconfdir}/X11/xorg.conf.d/
        install -m 0644 ${WORKDIR}/xorg.conf.d/99-calibration.conf ${D}/${sysconfdir}/X11/xorg.conf.d/
    fi
}

FILES:${PN}:append:rpi = " ${sysconfdir}/X11/xorg.conf.d/*"
```

Building off of the previous example, we once again are setting the `FILESEXTRAPATHS`{.interpreted-text role="term"} variable. In this case we are also using `SRC_URI`{.interpreted-text role="term"} to list additional source files to use when `rpi` is found in the list of `OVERRIDES`{.interpreted-text role="term"}. The `ref-tasks-install`{.interpreted-text role="ref"} task will then perform a check for an additional `MACHINE_FEATURES`{.interpreted-text role="term"} that if set will cause these additional files to be installed. These additional files are listed in `FILES`{.interpreted-text role="term"} so that they will be packaged.

> 基于前面的例子，我们再次设置 `FILESEXTRAPATHS` 变量。在这种情况下，我们还使用 `SRC_URI` 来列出额外的源文件，以便在 `OVERRIDES` 列表中找到 `rpi` 时使用。然后，`ref-tasks-install` 任务将执行一个额外的 `MACHINE_FEATURES` 检查，如果设置，将导致这些附加文件被安装。这些附加文件列在 `FILES` 中，以便它们将被打包。

# Prioritizing Your Layer

Each layer is assigned a priority value. Priority values control which layer takes precedence if there are recipe files with the same name in multiple layers. For these cases, the recipe file from the layer with a higher priority number takes precedence. Priority values also affect the order in which multiple `.bbappend` files for the same recipe are applied. You can either specify the priority manually, or allow the build system to calculate it based on the layer\'s dependencies.

> 每个层都分配一个优先级值。优先级值控制在多个层中存在同名配方文件时，哪个层优先。在这种情况下，优先级数字较高的层中的配方文件具有优先权。优先级值还会影响多个同一配方的 `.bbappend` 文件应用的顺序。您可以手动指定优先级，也可以让构建系统根据层的依赖关系计算优先级。

To specify the layer\'s priority manually, use the `BBFILE_PRIORITY`{.interpreted-text role="term"} variable and append the layer\'s root name:

```
BBFILE_PRIORITY_mylayer = "1"
```

::: note
::: title
Note
:::

It is possible for a recipe with a lower version number `PV`{.interpreted-text role="term"} in a layer that has a higher priority to take precedence.

> 可能会发生这样的情况：在优先级较高的层中，版本号较低的配方会被优先采纳。

Also, the layer priority does not currently affect the precedence order of `.conf` or `.bbclass` files. Future versions of BitBake might address this.

> 此外，层级优先级目前不会影响 `.conf` 或 `.bbclass` 文件的优先顺序。BitBake 的未来版本可能会解决这个问题。
> :::

# Managing Layers

You can use the BitBake layer management tool `bitbake-layers` to provide a view into the structure of recipes across a multi-layer project. Being able to generate output that reports on configured layers with their paths and priorities and on `.bbappend` files and their applicable recipes can help to reveal potential problems.

> 你可以使用 BitBake 层管理工具 `bitbake-layers` 来提供跨多层项目的配方结构的视图。能够生成报告配置层及其路径和优先级以及 `.bbappend` 文件及其适用配方的输出，有助于揭示潜在问题。

For help on the BitBake layer management tool, use the following command:

```
$ bitbake-layers --help
```

The following list describes the available commands:

- `help:` Displays general help or help on a specified command.
- `show-layers:` Shows the current configured layers.
- `show-overlayed:` Lists overlayed recipes. A recipe is overlayed when a recipe with the same name exists in another layer that has a higher layer priority.

> - `show-overlayed`：列出覆盖的食谱。当具有较高层次优先级的其他层中存在具有相同名称的食谱时，食谱被覆盖。

- `show-recipes:` Lists available recipes and the layers that provide them.
- `show-appends:` Lists `.bbappend` files and the recipe files to which they apply.
- `show-cross-depends:` Lists dependency relationships between recipes that cross layer boundaries.
- `add-layer:` Adds a layer to `bblayers.conf`.
- `remove-layer:` Removes a layer from `bblayers.conf`
- `flatten:` Flattens the layer configuration into a separate output directory. Flattening your layer configuration builds a \"flattened\" directory that contains the contents of all layers, with any overlayed recipes removed and any `.bbappend` files appended to the corresponding recipes. You might have to perform some manual cleanup of the flattened layer as follows:

> 扁平化层次配置，将其分离到单独的输出目录。扁平化层次配置会构建一个“扁平化”的目录，其中包含所有层次的内容，叠加的配方被删除，任何 `.bbappend` 文件被附加到相应的配方中。您可能需要手动清理扁平化层次，如下所示：

- Non-recipe files (such as patches) are overwritten. The flatten command shows a warning for these files.
- Anything beyond the normal layer setup has been added to the `layer.conf` file. Only the lowest priority layer\'s `layer.conf` is used.
- Overridden and appended items from `.bbappend` files need to be cleaned up. The contents of each `.bbappend` end up in the flattened recipe. However, if there are appended or changed variable values, you need to tidy these up yourself. Consider the following example. Here, the `bitbake-layers` command adds the line `#### bbappended ...` so that you know where the following lines originate:

> 被覆盖和附加的项目需要从 `.bbappend` 文件中清理。每个 `.bbappend` 的内容最终会出现在平坦的配方中。但是，如果有附加或更改的变量值，您需要自己整理这些值。考虑以下示例。在这里，`bitbake-layers` 命令添加行 `#### bbappended ...`，以便您知道以下行的来源：

```
```
...
DESCRIPTION = "A useful utility"
...
EXTRA_OECONF = "--enable-something"
...

#### bbappended from meta-anotherlayer ####

DESCRIPTION = "Customized utility"
EXTRA_OECONF += "--enable-somethingelse"
```

Ideally, you would tidy up these utilities as follows:

```
...
DESCRIPTION = "Customized utility"
...
EXTRA_OECONF = "--enable-something --enable-somethingelse"
...
```
```

- `layerindex-fetch`: Fetches a layer from a layer index, along with its dependent layers, and adds the layers to the `conf/bblayers.conf` file.
- `layerindex-show-depends`: Finds layer dependencies from the layer index.
- `save-build-conf`: Saves the currently active build configuration (`conf/local.conf`, `conf/bblayers.conf`) as a template into a layer. This template can later be used for setting up builds via `` `TEMPLATECONF``{.interpreted-text role="term"}[. For information about saving and using configuration templates, see \":ref:\`dev-manual/custom-template-configuration-directory:creating a custom template configuration directory]{.title-ref}\".

> - `save-build-conf`：将当前活动的构建配置（`conf/local.conf`，`conf/bblayers.conf`）保存为图层模板。此模板可以稍后通过 `TEMPLATECONF` 使用来设置构建。有关保存和使用配置模板的信息，请参见“创建自定义模板配置目录”。

- `create-layer`: Creates a basic layer.
- `create-layers-setup`: Writes out a configuration file and/or a script that can replicate the directory structure and revisions of the layers in a current build. For more information, see \"`dev-manual/layers:saving and restoring the layers setup`{.interpreted-text role="ref"}\".

> `-` 创建层设置：写出一个配置文件和/或脚本，可以复制当前构建中层的目录结构和修订。有关更多信息，请参见“dev-manual /层：保存和恢复层设置”。

# Creating a General Layer Using the `bitbake-layers` Script

The `bitbake-layers` script with the `create-layer` subcommand simplifies creating a new general layer.

::: note
::: title
Note
:::

- For information on BSP layers, see the \"`bsp-guide/bsp:bsp layers`{.interpreted-text role="ref"}\" section in the Yocto Project Board Specific (BSP) Developer\'s Guide.

> 对于 BSP 层的信息，请参见 Yocto Project Board Specific（BSP）开发者指南中的“bsp-guide / bsp：bsp 层”部分。

- In order to use a layer with the OpenEmbedded build system, you need to add the layer to your `bblayers.conf` configuration file. See the \"``dev-manual/layers:adding a layer using the \`\`bitbake-layers\`\` script``{.interpreted-text role="ref"}\" section for more information.

> 为了使用 OpenEmbedded 构建系统中的图层，您需要将图层添加到 bblayers.conf 配置文件中。有关更多信息，请参阅“dev-manual / layers：使用“bitbake-layers”脚本添加图层”部分。
> :::

The default mode of the script\'s operation with this subcommand is to create a layer with the following:

- A layer priority of 6.
- A `conf` subdirectory that contains a `layer.conf` file.
- A `recipes-example` subdirectory that contains a further subdirectory named `example`, which contains an `example.bb` recipe file.
- A `COPYING.MIT`, which is the license statement for the layer. The script assumes you want to use the MIT license, which is typical for most layers, for the contents of the layer itself.

> - 一个 `COPYING.MIT`，这是层的许可证声明。脚本假定您想为层本身使用通常用于大多数层的 MIT 许可证。

- A `README` file, which is a file describing the contents of your new layer.

In its simplest form, you can use the following command form to create a layer. The command creates a layer whose name corresponds to \"your_layer_name\" in the current directory:

> 简单的说，您可以使用以下命令表单来创建一个图层。该命令创建一个名称与当前目录中的“your_layer_name”对应的图层：

```
$ bitbake-layers create-layer your_layer_name
```

As an example, the following command creates a layer named `meta-scottrif` in your home directory:

```
$ cd /usr/home
$ bitbake-layers create-layer meta-scottrif
NOTE: Starting bitbake server...
Add your new layer with 'bitbake-layers add-layer meta-scottrif'
```

If you want to set the priority of the layer to other than the default value of \"6\", you can either use the `--priority` option or you can edit the `BBFILE_PRIORITY`{.interpreted-text role="term"} value in the `conf/layer.conf` after the script creates it. Furthermore, if you want to give the example recipe file some name other than the default, you can use the `--example-recipe-name` option.

> 如果你想将图层的优先级设置为默认值“6”以外的值，你可以使用 `--priority` 选项，或者在脚本创建之后，编辑 `conf/layer.conf` 中的 `BBFILE_PRIORITY` 值。此外，如果你想给示例配方文件起一个默认以外的名字，你可以使用 `--example-recipe-name` 选项。

The easiest way to see how the `bitbake-layers create-layer` command works is to experiment with the script. You can also read the usage information by entering the following:

> 最简单的方法来了解 `bitbake-layers create-layer` 命令如何工作是通过实验脚本。您还可以通过输入以下内容来阅读使用信息：

```
$ bitbake-layers create-layer --help
NOTE: Starting bitbake server...
usage: bitbake-layers create-layer [-h] [--priority PRIORITY]
                                   [--example-recipe-name EXAMPLERECIPE]
                                   layerdir

Create a basic layer

positional arguments:
  layerdir              Layer directory to create

optional arguments:
  -h, --help            show this help message and exit
  --priority PRIORITY, -p PRIORITY
                        Layer directory to create
  --example-recipe-name EXAMPLERECIPE, -e EXAMPLERECIPE
                        Filename of the example recipe
```

# Adding a Layer Using the `bitbake-layers` Script

Once you create your general layer, you must add it to your `bblayers.conf` file. Adding the layer to this configuration file makes the OpenEmbedded build system aware of your layer so that it can search it for metadata.

> 一旦你创建了你的通用层，你必须将它添加到你的 `bblayers.conf` 文件中。将层添加到此配置文件中可以使 OpenEmbedded 构建系统意识到你的层，以便它可以搜索元数据。

Add your layer by using the `bitbake-layers add-layer` command:

```
$ bitbake-layers add-layer your_layer_name
```

Here is an example that adds a layer named `meta-scottrif` to the configuration file. Following the command that adds the layer is another `bitbake-layers` command that shows the layers that are in your `bblayers.conf` file:

> 这里是一个示例，它将一个名为'meta-scottrif'的层添加到配置文件中。在添加层的命令之后，是另一个 `bitbake-layers` 命令，它显示了你的 `bblayers.conf` 文件中的层：

```
$ bitbake-layers add-layer meta-scottrif
NOTE: Starting bitbake server...
Parsing recipes: 100% |##########################################################| Time: 0:00:49
Parsing of 1441 .bb files complete (0 cached, 1441 parsed). 2055 targets, 56 skipped, 0 masked, 0 errors.
$ bitbake-layers show-layers
NOTE: Starting bitbake server...
layer                 path                                      priority
==========================================================================
meta                  /home/scottrif/poky/meta                  5
meta-poky             /home/scottrif/poky/meta-poky             5
meta-yocto-bsp        /home/scottrif/poky/meta-yocto-bsp        5
workspace             /home/scottrif/poky/build/workspace       99
meta-scottrif         /home/scottrif/poky/build/meta-scottrif   6
```

Adding the layer to this file enables the build system to locate the layer during the build.

::: note
::: title
Note
:::

During a build, the OpenEmbedded build system looks in the layers from the top of the list down to the bottom in that order.
:::

# Saving and restoring the layers setup

Once you have a working build with the correct set of layers, it is beneficial to capture the layer setup \-\-- what they are, which repositories they come from and which SCM revisions they\'re at \-\-- into a configuration file, so that this setup can be easily replicated later, perhaps on a different machine. Here\'s how to do this:

> 一旦您拥有了正确的层次结构的工作构建，将它们的层次结构 - 它们是什么，它们来自哪些存储库以及它们处于哪个 SCM 修订版本 - 捕获到配置文件中是有益的，以便可以在以后的另一台机器上轻松复制该设置。这是如何做到的：

```
$ bitbake-layers create-layers-setup /srv/work/alex/meta-alex/
NOTE: Starting bitbake server...
NOTE: Created /srv/work/alex/meta-alex/setup-layers.json
NOTE: Created /srv/work/alex/meta-alex/setup-layers
```

The tool needs a single argument which tells where to place the output, consisting of a json formatted layer configuration, and a `setup-layers` script that can use that configuration to restore the layers in a different location, or on a different host machine. The argument can point to a custom layer (which is then deemed a \"bootstrap\" layer that needs to be checked out first), or into a completely independent location.

> 需要一个参数来告诉工具在哪里放置输出，输出包括以 JSON 格式编写的图层配置，以及一个可以使用该配置在不同位置或不同主机上恢复图层的 `setup-layers` 脚本。该参数可以指向自定义图层（然后被视为需要首先检出的“引导”图层），或者指向完全独立的位置。

The replication of the layers is performed by running the `setup-layers` script provided above:

1. Clone the bootstrap layer or some other repository to obtain the json config and the setup script that can use it.
2. Run the script directly with no options:

   ```
   alex@Zen2:/srv/work/alex/my-build$ meta-alex/setup-layers
   Note: not checking out source meta-alex, use --force-bootstraplayer-checkout to override.

   Setting up source meta-intel, revision 15.0-hardknott-3.3-310-g0a96edae, branch master
   Running 'git init -q /srv/work/alex/my-build/meta-intel'

   Running 'git remote remove origin > /dev/null 2>&1; git remote add origin git://git.yoctoproject.org/meta-intel' in /srv/work/alex/my-build/meta-intel

   ```

> 在/srv/work/alex/my-build/meta-intel 中运行 'git remote remove origin > /dev/null 2>&1; git remote add origin git://git.yoctoproject.org/meta-intel'
> Running 'git fetch -q origin || true' in /srv/work/alex/my-build/meta-intel
> Running 'git checkout -q 0a96edae609a3f48befac36af82cf1eed6786b4a' in /srv/work/alex/my-build/meta-intel

Setting up source poky, revision 4.1_M1-372-g55483d28f2, branch akanavin/setup-layers
Running 'git init -q /srv/work/alex/my-build/poky'
Running 'git remote remove origin > /dev/null 2>&1; git remote add origin git://git.yoctoproject.org/poky' in /srv/work/alex/my-build/poky
Running 'git fetch -q origin || true' in /srv/work/alex/my-build/poky

Running 'git remote remove poky-contrib > /dev/null 2>&1; git remote add poky-contrib ssh://git@push.yoctoproject.org/poky-contrib' in /srv/work/alex/my-build/poky

> 运行'/srv/work/alex/my-build/poky'中的'git remote remove poky-contrib > /dev/null 2>&1; git remote add poky-contrib ssh://git@push.yoctoproject.org/poky-contrib'
> Running 'git fetch -q poky-contrib || true' in /srv/work/alex/my-build/poky
> Running 'git checkout -q 11db0390b02acac1324e0f827beb0e2e3d0d1d63' in /srv/work/alex/my-build/poky

```

::: note
::: title
Note
:::

This will work to update an existing checkout as well.
:::

::: note
::: title
Note
:::

The script is self-sufficient and requires only python3 and git on the build machine.
:::

::: note
::: title
Note
:::


Both the `create-layers-setup` and the `setup-layers` provided several additional options that customize their behavior - you are welcome to study them via `--help` command line parameter.

> 两个`create-layers-setup`和`setup-layers`都提供了几个可以自定义其行为的附加选项 - 欢迎使用`--help`命令行参数来学习它们。
:::
```
