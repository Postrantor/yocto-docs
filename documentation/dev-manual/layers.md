---
tip: translate by baidu@2023-06-07 17:12:20
...
---
title: Understanding and Creating Layers
----------------------------------------

The OpenEmbedded build system supports organizing `Metadata`{.interpreted-text role="term"} into multiple layers. Layers allow you to isolate different types of customizations from each other. For introductory information on the Yocto Project Layer Model, see the \"`overview-manual/yp-intro:the yocto project layer model`{.interpreted-text role="ref"}\" section in the Yocto Project Overview and Concepts Manual.

> OpenEmbedded 构建系统支持将“元数据”｛.explored text role=“term”｝组织到多个层中。层允许您将不同类型的自定义项彼此隔离。有关 Yocto 项目层模型的介绍性信息，请参阅 Yocto Project overview and Concepts manual 中的\“`overview manual/yp intro:the Yocto Project Layer Model`｛.explored text role=“ref”｝\”一节。

# Creating Your Own Layer

::: note
::: title
Note
:::

It is very easy to create your own layers to use with the OpenEmbedded build system, as the Yocto Project ships with tools that speed up creating layers. This section describes the steps you perform by hand to create layers so that you can better understand them. For information about the layer-creation tools, see the \"``bsp-guide/bsp:creating a new bsp layer using the \`\`bitbake-layers\`\` script``{.interpreted-text role="ref"}\" section in the Yocto Project Board Support Package (BSP) Developer\'s Guide and the \"``dev-manual/layers:creating a general layer using the \`\`bitbake-layers\`\` script``{.interpreted-text role="ref"}\" section further down in this manual.

> 创建自己的层以与 OpenEmbedded 构建系统一起使用非常容易，因为 Yocto 项目附带了加快创建层的工具。本节介绍手动创建图层的步骤，以便更好地理解它们。关于图层创建工具的信息，请参阅 Yocto Project Board Support Package（bsp）Developer\’s guide 中的“`nbsp guide/nbsp：使用\`\`bitbake layers\`\script``｛.depreted text role=“ref”｝创建新的 nbsp 层”一节和本手册下面的“`dev manual/layers:create a general layers using the \`\` bitbake layers\`\script`｛.epreted text role=“ref”}”一节。
> :::

Follow these general steps to create your layer without using tools:

> 按照以下常规步骤创建图层，而不使用工具：

1. *Check Existing Layers:* Before creating a new layer, you should be sure someone has not already created a layer containing the Metadata you need. You can see the :oe_layerindex:[OpenEmbedded Metadata Index \<\>]{.title-ref} for a list of layers from the OpenEmbedded community that can be used in the Yocto Project. You could find a layer that is identical or close to what you need.

> 1.*检查现有图层：*在创建新图层之前，应确保有人尚未创建包含所需元数据的图层。您可以看到：oe_layeindex:[OpenEmbedded Metadata Index\<\>]｛.title-ref｝，以获取可在 Yocto 项目中使用的 OpenEmbedded 社区中的层列表。你可以找到一个相同或接近你需要的图层。

2. *Create a Directory:* Create the directory for your layer. When you create the layer, be sure to create the directory in an area not associated with the Yocto Project `Source Directory`{.interpreted-text role="term"} (e.g. the cloned `poky` repository).

> 2.*创建目录：*为图层创建目录。创建层时，请确保在与 Yocto 项目“源目录”｛.depreted text role=“term”｝（例如克隆的“poky”存储库）无关的区域中创建目录。

While not strictly required, prepend the name of the directory with the string \"meta-\". For example:

> 虽然不是严格要求，但请在目录名称前加上字符串“meta-\”。例如：

```

> ```

meta-mylayer

> 变聚酯层

meta-GUI_xyz

> 元-GUI_xyz

meta-mymachine

> 元mymachine

```

> ```
> ```

With rare exceptions, a layer\'s name follows this form:

> 除了极少数例外，图层的名称如下所示：

```

> ```

meta-root_name

> meta-root名称

```

> ```
> ```

Following this layer naming convention can save you trouble later when tools, components, or variables \"assume\" your layer name begins with \"meta-\". A notable example is in configuration files as shown in the following step where layer names without the \"meta-\" string are appended to several variables used in the configuration.

> 当工具、组件或变量“假定”您的层名称以“meta-”开头时，遵循此层命名约定可以省去以后的麻烦。一个值得注意的例子是配置文件，如下面的步骤所示，其中不带“meta-”字符串的层名被附加到配置中使用的几个变量中。

3. *Create a Layer Configuration File:* Inside your new layer folder, you need to create a `conf/layer.conf` file. It is easiest to take an existing layer configuration file and copy that to your layer\'s `conf` directory and then modify the file as needed.

> 3.*创建一个层配置文件：*在新的层文件夹中，您需要创建一个“conf/Layer.conf”文件。最简单的方法是获取一个现有的层配置文件，并将其复制到层的“conf”目录中，然后根据需要修改该文件。

The `meta-yocto-bsp/conf/layer.conf` file in the Yocto Project :yocto\_[git:%60Source](git:%60Source) Repositories \</poky/tree/meta-yocto-bsp/conf\>\` demonstrates the required syntax. For your layer, you need to replace \"yoctobsp\" with a unique identifier for your layer (e.g. \"machinexyz\" for a layer named \"meta-machinexyz\"):

> yocto Project:yocto\_[git:%60Source]（git:%60Source）存储库\</poky/tree/meta-yocto-bsp/conf\>\`中的 `meta-yocto bsp/conf/layer.conf` 文件演示了所需的语法。对于您的层，您需要将“yoctobsp”替换为您的层的唯一标识符（例如，名为“meta machinexyz”的层的“machinexyz\”）：

```

> ```

# We have a conf and classes directory, add to BBPATH

> #我们有一个conf和classes目录，添加到BBPATH

BBPATH .= ":${LAYERDIR}"

> BBPATH路径=“：$｛LAYERDIR｝”


# We have recipes-* directories, add to BBFILES

> #我们有配方-*目录，添加到BBFILES

BBFILES += "${LAYERDIR}/recipes-*/*/*.bb \

> BBFILES+=“$｛LAYERDIR｝/配方-*/*/*.bb\
            ${LAYERDIR}/recipes-*/*/*.bbappend"


BBFILE_COLLECTIONS += "yoctobsp"

> BBFILE_COLLECTIONS+=“yoctobsp”

BBFILE_PATTERN_yoctobsp = "^${LAYERDIR}/"

> BBFILE_PATTERN_yoctbsp=“^$｛LAYERDIR｝/”

BBFILE_PRIORITY_yoctobsp = "5"

> BBFILE_PRIORITY_yoctops=“5”

LAYERVERSION_yoctobsp = "4"

> LAYERVERSION_yoctops=“4”图层

LAYERSERIES_COMPAT_yoctobsp = "dunfell"

> LAYERSERIES_COMPAT_yoctops=“灌篮”

```

> ```
> ```

Following is an explanation of the layer configuration file:

> 以下是对图层配置文件的说明：

- `BBPATH`{.interpreted-text role="term"}: Adds the layer\'s root directory to BitBake\'s search path. Through the use of the `BBPATH`{.interpreted-text role="term"} variable, BitBake locates class files (`.bbclass`), configuration files, and files that are included with `include` and `require` statements. For these cases, BitBake uses the first file that matches the name found in `BBPATH`{.interpreted-text role="term"}. This is similar to the way the `PATH` variable is used for binaries. It is recommended, therefore, that you use unique class and configuration filenames in your custom layer.

> -`BBPATH`｛.explored text role=“term”｝：将层的根目录添加到 BitBake 的搜索路径中。通过使用 `BBPATH`｛.explored text role=“term”｝变量，BitBake 可以定位类文件（`.bbclass`）、配置文件以及包含在 `include` 和 `require` 语句中的文件。对于这些情况，BitBake 使用与 `BBPATH` 中找到的名称相匹配的第一个文件{.expreted text role=“term”}。这类似于“PATH”变量用于二进制文件的方式。因此，建议您在自定义图层中使用唯一的类和配置文件名。

- `BBFILES`{.interpreted-text role="term"}: Defines the location for all recipes in the layer.

> -`BBFILES`｛.explored text role=“term”｝：定义层中所有配方的位置。

- `BBFILE_COLLECTIONS`{.interpreted-text role="term"}: Establishes the current layer through a unique identifier that is used throughout the OpenEmbedded build system to refer to the layer. In this example, the identifier \"yoctobsp\" is the representation for the container layer named \"meta-yocto-bsp\".

> -`BBFILE_COLLECTIONS`｛.explored text role=“term”｝：通过一个唯一标识符建立当前层，该标识符在整个 OpenEmbedded 构建系统中用于引用该层。在本例中，标识符“yoctobsp”是名为“meta yocto bsp”的容器层的表示形式。

- `BBFILE_PATTERN`{.interpreted-text role="term"}: Expands immediately during parsing to provide the directory of the layer.

> -`BBFILE_PATTERN`｛.explored text role=“term”｝：在解析过程中立即展开以提供层的目录。

- `BBFILE_PRIORITY`{.interpreted-text role="term"}: Establishes a priority to use for recipes in the layer when the OpenEmbedded build finds recipes of the same name in different layers.

> -`BBFILE_PRIORITY`｛.explored text role=“term”｝：当 OpenEmbedded 构建在不同层中找到相同名称的配方时，为层中的配方建立优先级。

- `LAYERVERSION`{.interpreted-text role="term"}: Establishes a version number for the layer. You can use this version number to specify this exact version of the layer as a dependency when using the `LAYERDEPENDS`{.interpreted-text role="term"} variable.

> -`LAYERVERSION`｛.explored text role=“term”｝：为层建立版本号。当使用 `LAYERDEPENDS`｛.explored text role=“term”｝变量时，可以使用此版本号将层的确切版本指定为依赖项。

- `LAYERDEPENDS`{.interpreted-text role="term"}: Lists all layers on which this layer depends (if any).

> -`LAYERDEPENDS`｛.explored text role=“term”｝：列出此层所依赖的所有层（如果有）。

- `LAYERSERIES_COMPAT`{.interpreted-text role="term"}: Lists the :yocto_wiki:[Yocto Project \</Releases\>]{.title-ref} releases for which the current version is compatible. This variable is a good way to indicate if your particular layer is current.

> -`LAYERSERIES_COMPAT`｛.explored text role=“term”｝：列出当前版本兼容的：yocto_wiki:[yocto Project\</Releases\>]｛.title ref｝版本。该变量是指示特定图层是否为当前图层的好方法。

4. *Add Content:* Depending on the type of layer, add the content. If the layer adds support for a machine, add the machine configuration in a `conf/machine/` file within the layer. If the layer adds distro policy, add the distro configuration in a `conf/distro/` file within the layer. If the layer introduces new recipes, put the recipes you need in `recipes-*` subdirectories within the layer.

> 4.*添加内容：*根据图层类型，添加内容。如果该层添加了对机器的支持，请在该层内的“conf/machine/”文件中添加机器配置。如果该层添加了发行版策略，则在该层内的“conf/disro/”文件中添加发行版配置。如果该层引入了新的配方，请将您需要的配方放在该层的“配方-*”子目录中。

::: note

> ：：：注释

::: title

> ：：标题

Note

> 笔记

:::

> ：：：

For an explanation of layer hierarchy that is compliant with the Yocto Project, see the \"`bsp-guide/bsp:example filesystem layout`{.interpreted-text role="ref"}\" section in the Yocto Project Board Support Package (BSP) Developer\'s Guide.

> 有关符合 Yocto 项目的层层次结构的解释，请参阅 Yocto Project Board Support Package（bsp）Developer\’s guide 中的“`nbsp guide/nbsp：示例文件系统布局`{.depreted text role=“ref”}\”一节。

:::

> ：：：

5. *Optionally Test for Compatibility:* If you want permission to use the Yocto Project Compatibility logo with your layer or application that uses your layer, perform the steps to apply for compatibility. See the \"`dev-manual/layers:making sure your layer is compatible with yocto project`{.interpreted-text role="ref"}\" section for more information.

> 5.*可选的兼容性测试：*如果您希望获得对您的层或使用您的层的应用程序使用 Yocto Project 兼容性徽标的权限，请执行应用兼容性的步骤。有关详细信息，请参阅\“`dev manual/layers:确保您的层与yocto项目兼容`{.depreted text role=“ref”}\”一节。

# Following Best Practices When Creating Layers

To create layers that are easier to maintain and that will not impact builds for other machines, you should consider the information in the following list:

> 要创建更易于维护且不会影响其他机器构建的层，您应该考虑以下列表中的信息：

- *Avoid \"Overlaying\" Entire Recipes from Other Layers in Your Configuration:* In other words, do not copy an entire recipe into your layer and then modify it. Rather, use an append file (`.bbappend`) to override only those parts of the original recipe you need to modify.

> -*避免在您的配置中“覆盖”其他层的整个配方：*换句话说，不要将整个配方复制到您的层中，然后对其进行修改。相反，使用附加文件（`.bappend`）只覆盖原始配方中需要修改的部分。

- *Avoid Duplicating Include Files:* Use append files (`.bbappend`) for each recipe that uses an include file. Or, if you are introducing a new recipe that requires the included file, use the path relative to the original layer directory to refer to the file. For example, use `require recipes-core/`[package]{.title-ref}`/`[file]{.title-ref}`.inc` instead of `require` [file]{.title-ref}`.inc`. If you\'re finding you have to overlay the include file, it could indicate a deficiency in the include file in the layer to which it originally belongs. If this is the case, you should try to address that deficiency instead of overlaying the include file. For example, you could address this by getting the maintainer of the include file to add a variable or variables to make it easy to override the parts needing to be overridden.

> -*避免重复包含文件：*对每个使用包含文件的配方使用附加文件（`.bappend`）。或者，如果要引入需要包含文件的新配方，请使用相对于原始层目录的路径来引用该文件。例如，使用 `require recipes-core/`[package]{.title-ref}`/`[file]{.titele-ref{`.inc` 代替 `require`[file]{.title-ref}`.inc`。如果您发现必须覆盖包含文件，则可能表明它最初所属层中的包含文件存在缺陷。如果是这种情况，您应该尝试解决该缺陷，而不是覆盖 include 文件。例如，您可以通过让 include 文件的维护者添加一个或多个变量来解决这个问题，从而可以轻松地覆盖需要覆盖的部分。

- *Structure Your Layers:* Proper use of overrides within append files and placement of machine-specific files within your layer can ensure that a build is not using the wrong Metadata and negatively impacting a build for a different machine. Following are some examples:

> -*构建您的层：*在附加文件中正确使用覆盖，并在层中放置机器特定文件，可以确保构建不会使用错误的元数据，从而对不同机器的构建产生负面影响。以下是一些例子：

- *Modify Variables to Support a Different Machine:* Suppose you have a layer named `meta-one` that adds support for building machine \"one\". To do so, you use an append file named `base-files.bbappend` and create a dependency on \"foo\" by altering the `DEPENDS`{.interpreted-text role="term"} variable:

> -*修改变量以支持不同的机器：*假设您有一个名为“meta one”的层，它添加了对构建机器“one”的支持。为此，您可以使用名为 `base-files.bappend` 的附加文件，并通过更改 `DEPENDS`｛.depredicted text role=“term”｝变量来创建对\“foo\”的依赖项：

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

> -*将机器特定的文件放在机器特定的位置：*当您有一个基本配方，如“base Files.bb”，其中包含对文件的“SRC_URI”｛.explored text role=“term”｝语句时，您可以使用附加文件使生成使用您自己版本的文件。例如，层中 `meta one/precipes core/base-files/base-fils.bappend` 处的附加文件可以使用 `FILESEXTRAPATHS`｛.respered text role=“term”｝扩展 `FILESPATH`｛.espered text role=“term”｝，如下所示：

```
```
FILESEXTRAPATHS:prepend := "${THISDIR}/${BPN}:"
```

The build for machine \"one\" will pick up your machine-specific file as long as you have the file in `meta-one/recipes-core/base-files/base-files/`. However, if you are building for a different machine and the `bblayers.conf` file includes the `meta-one` layer and the location of your machine-specific file is the first location where that file is found according to `FILESPATH`{.interpreted-text role="term"}, builds for all machines will also use that machine-specific file.

You can make sure that a machine-specific file is used for a particular machine by putting the file in a subdirectory specific to the machine. For example, rather than placing the file in `meta-one/recipes-core/base-files/base-files/` as shown above, put it in `meta-one/recipes-core/base-files/base-files/one/`. Not only does this make sure the file is used only when building for machine \"one\", but the build process locates the file more quickly.

In summary, you need to place all files referenced from `SRC_URI`{.interpreted-text role="term"} in a machine-specific subdirectory within the layer in order to restrict those files to machine-specific builds.
```

- *Perform Steps to Apply for Yocto Project Compatibility:* If you want permission to use the Yocto Project Compatibility logo with your layer or application that uses your layer, perform the steps to apply for compatibility. See the \"`dev-manual/layers:making sure your layer is compatible with yocto project`{.interpreted-text role="ref"}\" section for more information.

> -*执行步骤以申请 Yocto 项目兼容性：*如果您希望获得对您的层或使用您的层的应用程序使用 Yocto Project Compatibility 徽标的权限，请执行步骤以应用兼容性。有关详细信息，请参阅\“`dev manual/layers:确保您的层与yocto项目兼容`{.depreted text role=“ref”}\”一节。

- *Follow the Layer Naming Convention:* Store custom layers in a Git repository that use the `meta-layer_name` format.

> -*遵循层命名约定：*将自定义层存储在使用“meta-Layer_name”格式的 Git 存储库中。

- *Group Your Layers Locally:* Clone your repository alongside other cloned `meta` directories from the `Source Directory`{.interpreted-text role="term"}.

> -*在本地对您的层进行分组：*将您的存储库与“源目录”中的其他克隆的“元”目录一起克隆｛.depreted text role=“term”｝。

# Making Sure Your Layer is Compatible With Yocto Project

When you create a layer used with the Yocto Project, it is advantageous to make sure that the layer interacts well with existing Yocto Project layers (i.e. the layer is compatible with the Yocto Project). Ensuring compatibility makes the layer easy to be consumed by others in the Yocto Project community and could allow you permission to use the Yocto Project Compatible Logo.

> 当您创建与 Yocto 项目一起使用的图层时，确保该图层与现有 Yocto Project 图层良好交互是有利的（即该图层与 YoctoProject 兼容）。确保兼容性使 Yocto Project 社区中的其他人很容易使用该层，并允许您使用 Yocto 项目兼容徽标。

::: note
::: title
Note
:::

Only Yocto Project member organizations are permitted to use the Yocto Project Compatible Logo. The logo is not available for general use. For information on how to become a Yocto Project member organization, see the :yocto_home:[Yocto Project Website \<\>]{.title-ref}.

> 只有 Yocto 项目成员组织才允许使用 Yocto Project Compatible 徽标。该徽标不可用于一般用途。有关如何成为 Yocto 项目成员组织的信息，请参阅：Yocto_home:[Yocto Project Website\<\>]{.title-ref}。
> :::

The Yocto Project Compatibility Program consists of a layer application process that requests permission to use the Yocto Project Compatibility Logo for your layer and application. The process consists of two parts:

> Yocto 项目兼容性程序由一个层应用程序进程组成，该进程请求为您的层和应用程序使用 Yocto Project 兼容性徽标的权限。该过程由两部分组成：

1. Successfully passing a script (`yocto-check-layer`) that when run against your layer, tests it against constraints based on experiences of how layers have worked in the real world and where pitfalls have been found. Getting a \"PASS\" result from the script is required for successful compatibility registration.

> 1.成功地传递了一个脚本（“yocto-check layer”），该脚本在针对您的层运行时，根据层在现实世界中如何工作以及在哪里发现陷阱的经验，根据约束对其进行测试。成功注册兼容性需要从脚本中获得“PASS”结果。

2. Completion of an application acceptance form, which you can find at :yocto_home:[/webform/yocto-project-compatible-registration]{.title-ref}.

> 2.填写申请受理表，您可以在以下网址找到：yocto_home:[/webform/yocto 项目兼容注册]{.title-ref}。

To be granted permission to use the logo, you need to satisfy the following:

> 要获得使用徽标的权限，您需要满足以下条件：

- Be able to check the box indicating that you got a \"PASS\" when running the script against your layer.

> -在对您的层运行脚本时，可以选中表示您获得“通过”的框。

- Answer \"Yes\" to the questions on the form or have an acceptable explanation for any questions answered \"No\".

> -对表格上的问题回答“是”，或对回答的任何问题做出可接受的解释“否”。

- Be a Yocto Project Member Organization.

The remainder of this section presents information on the registration form and on the `yocto-check-layer` script.

> 本节的其余部分介绍了有关注册表和“yocto-check layer”脚本的信息。

## Yocto Project Compatible Program Application

Use the form to apply for your layer\'s approval. Upon successful application, you can use the Yocto Project Compatibility Logo with your layer and the application that uses your layer.

> 使用表格申请您所在层的批准。成功应用后，您可以将 Yocto 项目兼容性徽标与您的层和使用您的层的应用程序一起使用。

To access the form, use this link: :yocto_home:[/webform/yocto-project-compatible-registration]{.title-ref}. Follow the instructions on the form to complete your application.

> 若要访问表单，请使用以下链接：：yocto_home:[/webform/yocto 项目兼容注册]｛.title-ref｝。按照表单上的说明完成申请。

The application consists of the following sections:

> 该应用程序由以下部分组成：

- *Contact Information:* Provide your contact information as the fields require. Along with your information, provide the released versions of the Yocto Project for which your layer is compatible.

> -*联系信息：*根据字段要求提供您的联系信息。连同您的信息一起，提供与您的图层兼容的 Yocto 项目的已发布版本。

- *Acceptance Criteria:* Provide \"Yes\" or \"No\" answers for each of the items in the checklist. There is space at the bottom of the form for any explanations for items for which you answered \"No\".

> -*验收标准：*为检查表中的每个项目提供“是”或“否”的答案。表格底部有一个空格，用于对您回答“否”的项目进行任何解释。

- *Recommendations:* Provide answers for the questions regarding Linux kernel use and build success.

> -*建议：*为有关 Linux 内核使用和构建成功的问题提供答案。

## `yocto-check-layer` Script

The `yocto-check-layer` script provides you a way to assess how compatible your layer is with the Yocto Project. You should run this script prior to using the form to apply for compatibility as described in the previous section. You need to achieve a \"PASS\" result in order to have your application form successfully processed.

> “yocto check layer”脚本为您提供了一种评估层与 yocto 项目的兼容性的方法。您应该先运行此脚本，然后再使用上一节中描述的表单来应用兼容性。您需要获得“通过”的结果才能成功处理您的申请表。

The script divides tests into three areas: COMMON, BSP, and DISTRO. For example, given a distribution layer (DISTRO), the layer must pass both the COMMON and DISTRO related tests. Furthermore, if your layer is a BSP layer, the layer must pass the COMMON and BSP set of tests.

> 该脚本将测试分为三个区域：COMMON、BSP 和 DISTRO。例如，给定一个分布层（DISTRO），该层必须通过 COMMON 和 DISTRO 相关测试。此外，如果您的层是 BSP 层，则该层必须通过 COMMON 和 BSP 测试集。

To execute the script, enter the following commands from your build directory:

> 要执行该脚本，请在构建目录中输入以下命令：

```
$ source oe-init-build-env
$ yocto-check-layer your_layer_directory
```

Be sure to provide the actual directory for your layer as part of the command.

> 请确保作为命令的一部分为图层提供实际目录。

Entering the command causes the script to determine the type of layer and then to execute a set of specific tests against the layer. The following list overviews the test:

> 输入该命令将使脚本确定层的类型，然后对该层执行一组特定的测试。以下列表概述了测试：

- `common.test_readme`: Tests if a `README` file exists in the layer and the file is not empty.
- `common.test_parse`: Tests to make sure that BitBake can parse the files without error (i.e. `bitbake -p`).

> -“common.test_parse”：测试以确保 BitBake 能够无错误地解析文件（即“BitBake-p”）。

- `common.test_show_environment`: Tests that the global or per-recipe environment is in order without errors (i.e. `bitbake -e`).

> -`common.test_show_environment`：测试全局或每个配方的环境是否正常，没有错误（即 `bitbake-e`）。

- `common.test_world`: Verifies that `bitbake world` works.
- `common.test_signatures`: Tests to be sure that BSP and DISTRO layers do not come with recipes that change signatures.

> -`common.test_signatures`：测试以确保 BSP 和 DISTRO 层不会附带更改签名的配方。

- `common.test_layerseries_compat`: Verifies layer compatibility is set properly.
- `bsp.test_bsp_defines_machines`: Tests if a BSP layer has machine configurations.
- `bsp.test_bsp_no_set_machine`: Tests to ensure a BSP layer does not set the machine when the layer is added.

> -`nbsp.test_bsp_no_set_machine`：测试以确保 bsp 层在添加该层时不会设置计算机。

- `bsp.test_machine_world`: Verifies that `bitbake world` works regardless of which machine is selected.

> -`nbsp.test_machine_world`：验证无论选择哪台机器，“bitbake world”都能正常工作。

- `bsp.test_machine_signatures`: Verifies that building for a particular machine affects only the signature of tasks specific to that machine.

> -`nbsp.test_machine_signatures'：验证特定计算机的构建是否只影响该计算机特定任务的签名。

- `distro.test_distro_defines_distros`: Tests if a DISTRO layer has distro configurations.
- `distro.test_distro_no_set_distros`: Tests to ensure a DISTRO layer does not set the distribution when the layer is added.

> -`distro.test_distro_no_set_distro`：测试以确保添加层时 distro 层不会设置分布。

# Enabling Your Layer

Before the OpenEmbedded build system can use your new layer, you need to enable it. To enable your layer, simply add your layer\'s path to the `BBLAYERS`{.interpreted-text role="term"} variable in your `conf/bblayers.conf` file, which is found in the `Build Directory`{.interpreted-text role="term"}. The following example shows how to enable your new `meta-mylayer` layer (note how your new layer exists outside of the official `poky` repository which you would have checked out earlier):

> 在 OpenEmbedded 构建系统可以使用您的新层之前，您需要启用它。要启用您的层，只需将层的路径添加到您的“conf/bbliayers.conf”文件中的“BBLAYERS”｛.depredicted text role=“term”｝变量，该文件位于“构建目录”｛.epredicted textrole=”term｝中。以下示例显示了如何启用新的“元 mylayer”层（请注意，您的新层是如何存在于您之前会签出的官方“poky”存储库之外的）：

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

> BitBake 按照 `conf/blayers.conf` 文件中 `BBLAYERS`｛.explored text role=“term”｝变量的规定，从上到下解析每个 `conf/layer.conf` 文件。在处理每个“conf/layer.conf”文件的过程中，BitBake 会将特定层中包含的配方、类和配置添加到源目录中。

# Appending Other Layers Metadata With Your Layer

A recipe that appends Metadata to another recipe is called a BitBake append file. A BitBake append file uses the `.bbappend` file type suffix, while the corresponding recipe to which Metadata is being appended uses the `.bb` file type suffix.

> 将元数据附加到另一个配方的配方称为 BitBake 附加文件。BitBake 附加文件使用“.bappend”文件类型后缀，而要附加元数据的相应配方使用“.bb”文件类型前缀。

You can use a `.bbappend` file in your layer to make additions or changes to the content of another layer\'s recipe without having to copy the other layer\'s recipe into your layer. Your `.bbappend` file resides in your layer, while the main `.bb` recipe file to which you are appending Metadata resides in a different layer.

> 您可以在层中使用“.bappend”文件来添加或更改另一层的配方内容，而无需将另一层配方复制到您的层中。您的 `.bappend` 文件位于您的层中，而要向其附加元数据的主 `.bb` 配方文件位于不同的层中。

Being able to append information to an existing recipe not only avoids duplication, but also automatically applies recipe changes from a different layer into your layer. If you were copying recipes, you would have to manually merge changes as they occur.

> 能够将信息附加到现有配方中，不仅可以避免重复，还可以自动将配方更改从不同的层应用到您的层中。如果您正在复制配方，则必须手动合并发生的更改。

When you create an append file, you must use the same root name as the corresponding recipe file. For example, the append file `someapp_3.1.bbappend` must apply to `someapp_3.1.bb`. This means the original recipe and append filenames are version number-specific. If the corresponding recipe is renamed to update to a newer version, you must also rename and possibly update the corresponding `.bbappend` as well. During the build process, BitBake displays an error on starting if it detects a `.bbappend` file that does not have a corresponding recipe with a matching name. See the `BB_DANGLINGAPPENDS_WARNONLY`{.interpreted-text role="term"} variable for information on how to handle this error.

> 创建附加文件时，必须使用与相应配方文件相同的根名称。例如，附加文件“someapp_3.1.bbappend”必须应用于“someapp.3.1.bb”。这意味着原始配方和附加文件名是特定于版本号的。如果相应的配方被重命名以更新到新版本，您还必须重命名并可能更新相应的“.bappend”。在构建过程中，如果 BitBake 检测到“.bbpappend”文件没有具有匹配名称的相应配方，则在启动时显示错误。有关如何处理此错误的信息，请参阅 `BB_DANGLINGAPENDS_WARNONLY`｛.explored text role=“term”｝变量。

## Overlaying a File Using Your Layer

As an example, consider the main formfactor recipe and a corresponding formfactor append file both from the `Source Directory`{.interpreted-text role="term"}. Here is the main formfactor recipe, which is named `formfactor_0.0.bb` and located in the \"meta\" layer at `meta/recipes-bsp/formfactor`:

> 例如，考虑“源目录”中的主形状因子配方和相应的形状因子附加文件{.depreted text role=“term”}。这是主要的 formfactor 配方，名为 `formfactor_0.0.bb`，位于“meta”层的 `meta/recipes bsp/formfactor`：

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

> 在主配方中，请注意 `SRC_URI`{.interplated-text role=“term”}变量，它告诉 OpenEmbedded 构建系统在构建期间在哪里查找文件。

Following is the append file, which is named `formfactor_0.0.bbappend` and is from the Raspberry Pi BSP Layer named `meta-raspberrypi`. The file is in the layer at `recipes-bsp/formfactor`:

> 以下是名为“formfactor_0.0.bbappend”的附加文件，该文件来自名为“meta raspberrypi”的 Raspberry Pi BSP 层。该文件位于“配方 nbsp/formfactor”的层中：

```
FILESEXTRAPATHS:prepend := "${THISDIR}/${PN}:"
```

By default, the build system uses the `FILESPATH`{.interpreted-text role="term"} variable to locate files. This append file extends the locations by setting the `FILESEXTRAPATHS`{.interpreted-text role="term"} variable. Setting this variable in the `.bbappend` file is the most reliable and recommended method for adding directories to the search path used by the build system to find files.

> 默认情况下，生成系统使用 `FILESPATH`｛.respered text role=“term”｝变量来定位文件。此附加文件通过设置 `FILESEXTRAPATHS`｛.explored text role=“term”｝变量来扩展位置。在“.bappend”文件中设置此变量是将目录添加到生成系统用于查找文件的搜索路径中的最可靠和推荐的方法。

The statement in this example extends the directories to include `${``THISDIR`{.interpreted-text role="term"}`}/${``PN`{.interpreted-text role="term"}`}`, which resolves to a directory named `formfactor` in the same directory in which the append file resides (i.e. `meta-raspberrypi/recipes-bsp/formfactor`. This implies that you must have the supporting directory structure set up that will contain any files or patches you will be including from the layer.

> 本例中的语句将目录扩展为包括 `${``THIDIR`{.depreted text role=“term”}`}/${` PN `{.deploted text role=“term”}`，它解析为附加文件所在的同一目录中名为“formfactor”的目录（即“meta raspberrypi/recipes bsp/formfactor”）。这意味着您必须设置支持目录结构，该结构将包含您将从该层包含的任何文件或修补程序。

Using the immediate expansion assignment operator `:=` is important because of the reference to `THISDIR`{.interpreted-text role="term"}. The trailing colon character is important as it ensures that items in the list remain colon-separated.

> 使用立即展开赋值运算符“：=”很重要，因为它引用了“THISDIR”｛.explored text role=“term”｝。尾部冒号非常重要，因为它可以确保列表中的项目保持冒号分隔。

::: note
::: title
Note
:::

BitBake automatically defines the `THISDIR`{.interpreted-text role="term"} variable. You should never set this variable yourself. Using \":prepend\" as part of the `FILESEXTRAPATHS`{.interpreted-text role="term"} ensures your path will be searched prior to other paths in the final list.

> BitBake 自动定义 `THISDIR`｛.explored text role=“term”｝变量。您永远不应该自己设置这个变量。使用\“：prepend \”作为 `FILESEXTRAPATHS` 的一部分｛.explored text role=“term”｝可以确保在最终列表中搜索您的路径之前搜索其他路径。

Also, not all append files add extra files. Many append files simply allow to add build options (e.g. `systemd`). For these cases, your append file would not even use the `FILESEXTRAPATHS`{.interpreted-text role="term"} statement.

> 此外，并非所有附加文件都会添加额外的文件。许多附加文件只允许添加构建选项（例如“systemd”）。对于这些情况，您的附加文件甚至不会使用 `FILESEXTRAPATHS`｛.respered text role=“term”｝语句。
> :::

The end result of this `.bbappend` file is that on a Raspberry Pi, where `rpi` will exist in the list of `OVERRIDES`{.interpreted-text role="term"}, the file `meta-raspberrypi/recipes-bsp/formfactor/formfactor/rpi/machconfig` will be used during `ref-tasks-fetch`{.interpreted-text role="ref"} and the test for a non-zero file size in `ref-tasks-install`{.interpreted-text role="ref"} will return true, and the file will be installed.

> 这个 `.bappend` 文件的最终结果是在 Raspberry Pi 上，其中 `rpi` 将存在于 `OVERRIDES` 的列表{.depredicted text role=“term”}中，文件 `meta raspberrypi/recipes bsp/formfactor/formfactor/rpi/machconfig` 将在 `ref tasks fetch`｛.depreted text role=“ref”｝期间使用，并且在 `ref任务安装`｛.repreted text role=“ref”}中对非零文件大小的测试将返回 true，并且将安装该文件。

## Installing Additional Files Using Your Layer

As another example, consider the main `xserver-xf86-config` recipe and a corresponding `xserver-xf86-config` append file both from the `Source Directory`{.interpreted-text role="term"}. Here is the main `xserver-xf86-config` recipe, which is named `xserver-xf86-config_0.1.bb` and located in the \"meta\" layer at `meta/recipes-graphics/xorg-xserver`:

> 另一个例子是，考虑主“xserver-xf86-config”配方和相应的“xserver-xf86-config“附加文件，它们都来自“源目录”｛.depredicted text role=“term”｝。以下是主“xserver-xf86-config”配方，名为“xserver-xf86-config_0.1.bb”，位于“meta/precipes graphics/xorg-xserver”的“meta\”层：

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

> 以下是名为“xserver-xf86-config_%.bbappend”的附加文件，该文件来自名为“meta raspberrypi”的 Raspberry Pi BSP 层。该文件位于“recipes graphics/xorg xserver”的层中：

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

> 在上一个示例的基础上，我们再次设置 `FILESEXTRAPATHS`｛.depreted text role=“term”｝变量。在这种情况下，当在 `OVERRIDES` 的列表中找到 `rpi` 时，我们还使用 `SRC_URI`{.expreted text role=“term”}来列出要使用的其他源文件。然后，`ref tasks install`｛.depreted text role=“ref”｝任务将检查是否有额外的 `MACHINE_FEATURE`｛.repreted text role=“term”｝，如果设置了该项，将导致安装这些额外的文件。这些附加文件列在 `files`｛.respered text role=“term”｝中，以便对它们进行打包。

# Prioritizing Your Layer

Each layer is assigned a priority value. Priority values control which layer takes precedence if there are recipe files with the same name in multiple layers. For these cases, the recipe file from the layer with a higher priority number takes precedence. Priority values also affect the order in which multiple `.bbappend` files for the same recipe are applied. You can either specify the priority manually, or allow the build system to calculate it based on the layer\'s dependencies.

> 每个图层都指定了一个优先级值。如果多个层中存在具有相同名称的配方文件，则优先级值控制哪个层优先。对于这些情况，优先级较高的层中的配方文件优先。优先级值也会影响应用同一配方的多个“.bappend”文件的顺序。您可以手动指定优先级，也可以允许生成系统根据层的依赖关系计算优先级。

To specify the layer\'s priority manually, use the `BBFILE_PRIORITY`{.interpreted-text role="term"} variable and append the layer\'s root name:

> 要手动指定层的优先级，请使用 `BBFILE_priority`｛.explored text role=“term”｝变量并附加层的根名称：

```
BBFILE_PRIORITY_mylayer = "1"
```

::: note
::: title
Note
:::

It is possible for a recipe with a lower version number `PV`{.interpreted-text role="term"} in a layer that has a higher priority to take precedence.

> 在具有较高优先级的层中，版本号为“PV”｛.explored text role=“term”｝的配方可能优先。

Also, the layer priority does not currently affect the precedence order of `.conf` or `.bbclass` files. Future versions of BitBake might address this.

> 此外，层优先级当前不影响“.conf”或“.bbclass”文件的优先级。未来版本的 BitBake 可能会解决这个问题。
> :::

# Managing Layers

You can use the BitBake layer management tool `bitbake-layers` to provide a view into the structure of recipes across a multi-layer project. Being able to generate output that reports on configured layers with their paths and priorities and on `.bbappend` files and their applicable recipes can help to reveal potential problems.

> 您可以使用 BitBake 层管理工具“BitBake layers”来提供多层项目中配方结构的视图。能够生成输出，报告配置的层及其路径和优先级，以及“.bbpappend”文件及其适用的配方，有助于揭示潜在的问题。

For help on the BitBake layer management tool, use the following command:

> 有关 BitBake 层管理工具的帮助，请使用以下命令：

```
$ bitbake-layers --help
```

The following list describes the available commands:

> 以下列表介绍了可用的命令：

- `help:` Displays general help or help on a specified command.
- `show-layers:` Shows the current configured layers.
- `show-overlayed:` Lists overlayed recipes. A recipe is overlayed when a recipe with the same name exists in another layer that has a higher layer priority.

> -`show overlayed:` 列出重叠的食谱。当具有相同名称的配方存在于具有较高层优先级的另一层中时，配方被覆盖。

- `show-recipes:` Lists available recipes and the layers that provide them.
- `show-appends:` Lists `.bbappend` files and the recipe files to which they apply.
- `show-cross-depends:` Lists dependency relationships between recipes that cross layer boundaries.

> -`show cross-dependents:` 列出跨越层边界的配方之间的依赖关系。

- `add-layer:` Adds a layer to `bblayers.conf`.
- `remove-layer:` Removes a layer from `bblayers.conf`
- `flatten:` Flattens the layer configuration into a separate output directory. Flattening your layer configuration builds a \"flattened\" directory that contains the contents of all layers, with any overlayed recipes removed and any `.bbappend` files appended to the corresponding recipes. You might have to perform some manual cleanup of the flattened layer as follows:

> -`flash:` 将层配置展开到一个单独的输出目录中。扁平化您的层配置会构建一个“扁平化”目录，其中包含所有层的内容，删除任何重叠的配方，并将任何“.bbpappend”文件附加到相应的配方中。您可能需要对展开的图层执行一些手动清理，如下所示：

- Non-recipe files (such as patches) are overwritten. The flatten command shows a warning for these files.

> -非配方文件（如补丁）将被覆盖。展开命令显示了对这些文件的警告。

- Anything beyond the normal layer setup has been added to the `layer.conf` file. Only the lowest priority layer\'s `layer.conf` is used.

> -任何超出正常层设置的内容都已添加到“layer.conf”文件中。只使用优先级最低的层的“layer.conf”。

- Overridden and appended items from `.bbappend` files need to be cleaned up. The contents of each `.bbappend` end up in the flattened recipe. However, if there are appended or changed variable values, you need to tidy these up yourself. Consider the following example. Here, the `bitbake-layers` command adds the line `#### bbappended ...` so that you know where the following lines originate:

> -需要清除“.bappend”文件中被覆盖和附加的项。每个“.bappend”的内容最终都会显示在扁平的配方中。但是，如果有附加或更改的变量值，您需要自己整理这些值。考虑以下示例。这里，“bitbake layers”命令添加行“####bbapped…”以便您知道以下线条的来源：

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

> -`layerindex fetch`：从层索引中提取一个层及其相关层，并将这些层添加到 `conf/bbblayers.conf` 文件中。

- `layerindex-show-depends`: Finds layer dependencies from the layer index.
- `save-build-conf`: Saves the currently active build configuration (`conf/local.conf`, `conf/bblayers.conf`) as a template into a layer. This template can later be used for setting up builds via `` `TEMPLATECONF``{.interpreted-text role="term"}[. For information about saving and using configuration templates, see \":ref:\`dev-manual/custom-template-configuration-directory:creating a custom template configuration directory]{.title-ref}\".

> -“save build conf”：将当前活动的构建配置（“conf/local.conf”、“conf/bbblayers.conf”）作为模板保存到层中。此模板稍后可用于通过 ``TEMPLATECOF``｛.explored text role=“term”｝[设置生成。有关保存和使用配置模板的信息，请参阅\“：ref:\`dev manual/custom template configuration directory:create a custom template configuration directory]｛.title ref｝\”。

- `create-layer`: Creates a basic layer.
- `create-layers-setup`: Writes out a configuration file and/or a script that can replicate the directory structure and revisions of the layers in a current build. For more information, see \"`dev-manual/layers:saving and restoring the layers setup`{.interpreted-text role="ref"}\".

> -“create layers setup”：写出一个配置文件和/或脚本，可以复制当前生成中的目录结构和层的修订。有关更多信息，请参阅\“`dev manual/layers:save and restore the layers setup`｛.depredicted text role=“ref”｝\”。

# Creating a General Layer Using the `bitbake-layers` Script

The `bitbake-layers` script with the `create-layer` subcommand simplifies creating a new general layer.

> 带有“create layer”子命令的“bitbake layers”脚本简化了创建新的常规层的过程。

::: note
::: title
Note
:::

- For information on BSP layers, see the \"`bsp-guide/bsp:bsp layers`{.interpreted-text role="ref"}\" section in the Yocto Project Board Specific (BSP) Developer\'s Guide.

> -有关 BSP 层的信息，请参阅 Yocto Project Board Specific（BSP）Developer\’s guide 中的“`nbsp guide/BSP：nbsp layers`｛.explored text role=”ref“｝”一节。

- In order to use a layer with the OpenEmbedded build system, you need to add the layer to your `bblayers.conf` configuration file. See the \"``dev-manual/layers:adding a layer using the \`\`bitbake-layers\`\` script``{.interpreted-text role="ref"}\" section for more information.

> -为了在 OpenEmbedded 构建系统中使用一个层，您需要将该层添加到“bblayers.conf”配置文件中。有关详细信息，请参阅“``dev manual/layers:adding a layers using the \`\`bitbake layers\`\`script``｛.depreted text role=“ref”｝\”一节。
> :::

The default mode of the script\'s operation with this subcommand is to create a layer with the following:

> 脚本使用此子命令操作的默认模式是创建一个具有以下内容的层：

- A layer priority of 6.
- A `conf` subdirectory that contains a `layer.conf` file.
- A `recipes-example` subdirectory that contains a further subdirectory named `example`, which contains an `example.bb` recipe file.

> -一个“recipes example”子目录，包含另一个名为“example”的子目录，其中包含一个“example.bb”配方文件。

- A `COPYING.MIT`, which is the license statement for the layer. The script assumes you want to use the MIT license, which is typical for most layers, for the contents of the layer itself.

> -一个“COPYING.MIT”，这是该层的许可证声明。该脚本假设您想要使用 MIT 许可证，这对于大多数层来说是典型的，用于层本身的内容。

- A `README` file, which is a file describing the contents of your new layer.

In its simplest form, you can use the following command form to create a layer. The command creates a layer whose name corresponds to \"your_layer_name\" in the current directory:

> 在最简单的形式中，可以使用以下命令形式创建图层。该命令在当前目录中创建一个层，其名称对应于“your_layer_name\”：

```
$ bitbake-layers create-layer your_layer_name
```

As an example, the following command creates a layer named `meta-scottrif` in your home directory:

> 例如，以下命令在主目录中创建一个名为“meta-scottrif”的层：

```
$ cd /usr/home
$ bitbake-layers create-layer meta-scottrif
NOTE: Starting bitbake server...
Add your new layer with 'bitbake-layers add-layer meta-scottrif'
```

If you want to set the priority of the layer to other than the default value of \"6\", you can either use the `--priority` option or you can edit the `BBFILE_PRIORITY`{.interpreted-text role="term"} value in the `conf/layer.conf` after the script creates it. Furthermore, if you want to give the example recipe file some name other than the default, you can use the `--example-recipe-name` option.

> 如果您想将层的优先级设置为默认值“6\”以外的值，您可以使用“--priority”选项，也可以在脚本创建后编辑“conf/layer.conf”中的“BBFILE_priority`｛.explored text role=“term”｝值。此外，如果您想为示例配方文件指定一些默认值以外的名称，您也可以使用“--示例配方名称”选项。

The easiest way to see how the `bitbake-layers create-layer` command works is to experiment with the script. You can also read the usage information by entering the following:

> 了解“bitbake layers create layer”命令如何工作的最简单方法是对脚本进行实验。您还可以通过输入以下内容来读取使用信息：

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

> 一旦创建了常规层，就必须将其添加到“bblayers.conf”文件中。将层添加到此配置文件可以使 OpenEmbedded 构建系统了解您的层，以便它可以在其中搜索元数据。

Add your layer by using the `bitbake-layers add-layer` command:

> 使用“bitbake layers Add layer”命令添加层：

```
$ bitbake-layers add-layer your_layer_name
```

Here is an example that adds a layer named `meta-scottrif` to the configuration file. Following the command that adds the layer is another `bitbake-layers` command that shows the layers that are in your `bblayers.conf` file:

> 下面是一个将名为“meta scottrif”的层添加到配置文件中的示例。添加层的命令后面是另一个“bitbake layers”命令，该命令显示“bblayers.conf”文件中的层：

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

> 将图层添加到此文件中可使生成系统在生成过程中定位图层。

::: note
::: title
Note
:::

During a build, the OpenEmbedded build system looks in the layers from the top of the list down to the bottom in that order.

> 在构建过程中，OpenEmbedded 构建系统按顺序从列表的顶部向下查看层。
> :::

# Saving and restoring the layers setup

Once you have a working build with the correct set of layers, it is beneficial to capture the layer setup \-\-- what they are, which repositories they come from and which SCM revisions they\'re at \-\-- into a configuration file, so that this setup can be easily replicated later, perhaps on a different machine. Here\'s how to do this:

> 一旦您有了一个具有正确层集的工作构建，就可以将层设置捕获到配置文件中，这样以后可以很容易地复制此设置，也许可以在不同的机器上复制。以下是如何做到这一点：

```
$ bitbake-layers create-layers-setup /srv/work/alex/meta-alex/
NOTE: Starting bitbake server...
NOTE: Created /srv/work/alex/meta-alex/setup-layers.json
NOTE: Created /srv/work/alex/meta-alex/setup-layers
```

The tool needs a single argument which tells where to place the output, consisting of a json formatted layer configuration, and a `setup-layers` script that can use that configuration to restore the layers in a different location, or on a different host machine. The argument can point to a custom layer (which is then deemed a \"bootstrap\" layer that needs to be checked out first), or into a completely independent location.

> 该工具需要一个告诉将输出放在何处的参数，该参数由 json 格式的层配置和“setup-layers”脚本组成，该脚本可以使用该配置恢复不同位置或不同主机上的层。该参数可以指向自定义层（然后将其视为需要首先检出的“引导”层），也可以指向完全独立的位置。

The replication of the layers is performed by running the `setup-layers` script provided above:

> 层的复制是通过运行上面提供的“设置层”脚本来执行的：

1. Clone the bootstrap layer or some other repository to obtain the json config and the setup script that can use it.

> 1.克隆引导层或其他存储库，以获得 json-config 和可以使用它的设置脚本。

2. Run the script directly with no options:

> 2.直接运行脚本，不带任何选项：

```

> ```

alex@Zen2:/srv/work/alex/my-build$ meta-alex/setup-layers

> alex@Zen2：/srv/work/alex/my-build$meta-alex/setup-layers

Note: not checking out source meta-alex, use --force-bootstraplayer-checkout to override.

> 注意：不签出源meta亚历克斯，请使用--force引导层签出来覆盖。


Setting up source meta-intel, revision 15.0-hardknott-3.3-310-g0a96edae, branch master

> 设置源meta intel，修订版15.0-hardknot-3.3-310-g0a96edae，分支主机

Running 'git init -q /srv/work/alex/my-build/meta-intel'

> 正在运行“git-int-q/srv/work/alex/my-build/met-intel”

Running 'git remote remove origin > /dev/null 2>&1; git remote add origin git://git.yoctoproject.org/meta-intel' in /srv/work/alex/my-build/meta-intel

> 正在运行“git remote remove origin”>/dev/null 2>&1；git远程添加原点git://git.yoctoproject.org/meta-intel在/srv/work/alex/my-build/met-intel中

Running 'git fetch -q origin || true' in /srv/work/alex/my-build/meta-intel

> 在/srv/work/alex/my-build/meta-intel中运行“git fetch-q origin ||true”

Running 'git checkout -q 0a96edae609a3f48befac36af82cf1eed6786b4a' in /srv/work/alex/my-build/meta-intel

> 在/srv/work/alex/my-build/met-intel中运行“git checkout-q 0a96edae609a3f48befac36af82cf1eed6786b4a”


Setting up source poky, revision 4.1_M1-372-g55483d28f2, branch akanavin/setup-layers

> 设置源poky，修订版4.1_M1-372-g55483d28f2，分支akanavin/setup层

Running 'git init -q /srv/work/alex/my-build/poky'

> 正在运行“git-int-q/srv/work/alex/my-build/poky”

Running 'git remote remove origin > /dev/null 2>&1; git remote add origin git://git.yoctoproject.org/poky' in /srv/work/alex/my-build/poky

> 正在运行“git remote remove origin”>/dev/null 2>&1；git远程添加原点git://git.yoctoproject.org/poky在/srv/work/alex/my-build/poky中

Running 'git fetch -q origin || true' in /srv/work/alex/my-build/poky

> 在/srv/work/alex/my-build/poky中运行“git fetch-q origin |true”

Running 'git remote remove poky-contrib > /dev/null 2>&1; git remote add poky-contrib ssh://git@push.yoctoproject.org/poky-contrib' in /srv/work/alex/my-build/poky

> 运行“git remote remove poky contrib>/dev/null 2>&1；git远程添加poky contribssh://git@在/srv/work/alex/my-build/poky中推送/yoctoproject.org/poky contrib'

Running 'git fetch -q poky-contrib || true' in /srv/work/alex/my-build/poky

> 在/srv/work/alex/my-build/poky中运行“git fetch-q poky contrib||true”

Running 'git checkout -q 11db0390b02acac1324e0f827beb0e2e3d0d1d63' in /srv/work/alex/my-build/poky

> 在/srv/work/alex/my-build/poky中运行“git checkout-q 11db0390b02acac1324e0f827beb0e2e3d0d1d63”

```

> ```
> ```

::: note
::: title
Note
:::

This will work to update an existing checkout as well.

> 这也将用于更新现有签出。
> :::

::: note
::: title
Note
:::

The script is self-sufficient and requires only python3 and git on the build machine.

> 该脚本是自给自足的，并且在构建机器上只需要 python3 和 git。
> :::

::: note
::: title
Note
:::

Both the `create-layers-setup` and the `setup-layers` provided several additional options that customize their behavior - you are welcome to study them via `--help` command line parameter.

> “create layers setup”和“setup layers”都提供了几个额外的选项来自定义它们的行为——欢迎您通过“--help”命令行参数来研究它们。
> :::
