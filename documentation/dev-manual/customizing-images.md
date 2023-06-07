---
tip: translate by baidu@2023-06-07 17:10:24
...
---
title: Customizing Images
-------------------------

You can customize images to satisfy particular requirements. This section describes several methods and provides guidelines for each.

> 您可以自定义图像以满足特定要求。本节介绍了几种方法，并为每种方法提供了指导方针。

# Customizing Images Using `local.conf`

Probably the easiest way to customize an image is to add a package by way of the `local.conf` configuration file. Because it is limited to local use, this method generally only allows you to add packages and is not as flexible as creating your own customized image. When you add packages using local variables this way, you need to realize that these variable changes are in effect for every build and consequently affect all images, which might not be what you require.

> 自定义映像最简单的方法可能是通过“local.conf”配置文件添加一个包。因为它仅限于本地使用，所以这种方法通常只允许添加包，不像创建自己的自定义映像那样灵活。当您以这种方式使用局部变量添加包时，您需要意识到这些变量更改对每个构建都有效，因此会影响所有映像，而这可能不是您所需要的。

To add a package to your image using the local configuration file, use the `IMAGE_INSTALL`{.interpreted-text role="term"} variable with the `:append` operator:

> 要使用本地配置文件将程序包添加到映像中，请使用 `image_INSTALL`｛.respered text role=“term”｝变量和 `：append` 运算符：

```
IMAGE_INSTALL:append = " strace"
```

Use of the syntax is important; specifically, the leading space after the opening quote and before the package name, which is `strace` in this example. This space is required since the `:append` operator does not add the space.

> 语法的使用很重要；具体地说，在开头引号之后和包名称之前的前导空格，在本例中为“strace”。由于“：append”运算符不添加空格，因此需要此空格。

Furthermore, you must use `:append` instead of the `+=` operator if you want to avoid ordering issues. The reason for this is because doing so unconditionally appends to the variable and avoids ordering problems due to the variable being set in image recipes and `.bbclass` files with operators like `?=`. Using `:append` ensures the operation takes effect.

> 此外，如果要避免排序问题，必须使用“：append”而不是“+=”运算符。之所以这样做，是因为这样做会无条件地附加到变量，并避免由于变量是在图像配方和带有“？=”等运算符的“.bbclass”文件中设置的而导致的排序问题。使用“：append”可确保操作生效。

As shown in its simplest use, `IMAGE_INSTALL:append` affects all images. It is possible to extend the syntax so that the variable applies to a specific image only. Here is an example:

> 如最简单的用法所示，`IMAGE_INSTAL:append` 会影响所有图像。可以扩展语法，使变量仅适用于特定图像。以下是一个示例：

```
IMAGE_INSTALL:append:pn-core-image-minimal = " strace"
```

This example adds `strace` to the `core-image-minimal` image only.

> 此示例仅将“strace”添加到“核心图像最小”图像。

You can add packages using a similar approach through the `CORE_IMAGE_EXTRA_INSTALL`{.interpreted-text role="term"} variable. If you use this variable, only `core-image-*` images are affected.

> 您可以使用类似的方法通过 `CORE_IMAGE_EXTRA_INSTAL`｛.explored text role=“term”｝变量添加包。如果使用此变量，则仅“核心图像-*”图像会受到影响。

# Customizing Images Using Custom `IMAGE_FEATURES` and `EXTRA_IMAGE_FEATURES`

Another method for customizing your image is to enable or disable high-level image features by using the `IMAGE_FEATURES`{.interpreted-text role="term"} and `EXTRA_IMAGE_FEATURES`{.interpreted-text role="term"} variables. Although the functions for both variables are nearly equivalent, best practices dictate using `IMAGE_FEATURES`{.interpreted-text role="term"} from within a recipe and using `EXTRA_IMAGE_FEATURES`{.interpreted-text role="term"} from within your `local.conf` file, which is found in the `Build Directory`{.interpreted-text role="term"}.

> 另一种自定义图像的方法是通过使用 `image_FATURE`｛.explored text role=“term”｝和 `EXTRA_image_FEATURE`{.explered text rol=“term“｝变量来启用或禁用高级图像功能。尽管这两个变量的函数几乎相等，但最佳实践要求在配方中使用 `IMAGE_FATURE`｛.depredicted text role=“term”｝，并在 `Build Directory`｛.epredicted textrole=”term“｝中的 `local.conf` 文件中使用 `EXTRA_IMAGE_FEATURE`{.depredictedtext role=“term“}。

To understand how these features work, the best reference is `meta/classes-recipe/image.bbclass <ref-classes-image>`{.interpreted-text role="ref"}. This class lists out the available `IMAGE_FEATURES`{.interpreted-text role="term"} of which most map to package groups while some, such as `debug-tweaks` and `read-only-rootfs`, resolve as general configuration settings.

> 要了解这些功能是如何工作的，最好的参考是 `meta/classes recipe/image.bbclass<ref classes image>`{.depreted text role=“ref”}。此类列出了可用的 `IMAGE_FATURE`｛.explored text role=“term”｝，其中大多数映射到包组，而一些，如 `调试调整'和` 只读 rootfs'，则解析为常规配置设置。

In summary, the file looks at the contents of the `IMAGE_FEATURES`{.interpreted-text role="term"} variable and then maps or configures the feature accordingly. Based on this information, the build system automatically adds the appropriate packages or configurations to the `IMAGE_INSTALL`{.interpreted-text role="term"} variable. Effectively, you are enabling extra features by extending the class or creating a custom class for use with specialized image `.bb` files.

> 总之，该文件查看 `IMAGE_FATURE`｛.explored text role=“term”｝变量的内容，然后相应地映射或配置该功能。基于此信息，生成系统会自动将适当的包或配置添加到 `IMAGE_INSTALL`｛.depredicted text role=“term”｝变量中。实际上，您可以通过扩展类或创建一个自定义类来启用额外的功能，以便与专用的映像 `.bb` 文件一起使用。

Use the `EXTRA_IMAGE_FEATURES`{.interpreted-text role="term"} variable from within your local configuration file. Using a separate area from which to enable features with this variable helps you avoid overwriting the features in the image recipe that are enabled with `IMAGE_FEATURES`{.interpreted-text role="term"}. The value of `EXTRA_IMAGE_FEATURES`{.interpreted-text role="term"} is added to `IMAGE_FEATURES`{.interpreted-text role="term"} within `meta/conf/bitbake.conf`.

> 使用本地配置文件中的 `EXTRA_IMAGE_FEATURE`｛.explored text role=“term”｝变量。使用一个单独的区域来启用此变量的功能，有助于避免覆盖图像配方中使用 `image_FATURE`｛.depredicted text role=“term”｝启用的功能。`EXTRA_IMAGE_FEATURE`｛.explored text role=“term”｝的值被添加到 `meta/conf/bitbake.conf` 内的 `IMAGE_FATURE`{.explered text rol=“term“｝中。

To illustrate how you can use these variables to modify your image, consider an example that selects the SSH server. The Yocto Project ships with two SSH servers you can use with your images: Dropbear and OpenSSH. Dropbear is a minimal SSH server appropriate for resource-constrained environments, while OpenSSH is a well-known standard SSH server implementation. By default, the `core-image-sato` image is configured to use Dropbear. The `core-image-full-cmdline` and `core-image-lsb` images both include OpenSSH. The `core-image-minimal` image does not contain an SSH server.

> 为了说明如何使用这些变量来修改映像，请考虑一个选择 SSH 服务器的示例。Yocto 项目附带了两个 SSH 服务器，您可以将它们用于映像：Dropbear 和 OpenSSH。Dropbear 是适用于资源受限环境的最小 SSH 服务器，而 OpenSSH 是众所周知的标准 SSH 服务器实现。默认情况下，“核心图像 sato”图像配置为使用 Dropbear。“核心图像完整 cmdline”和“核心图像 lsb”图像都包括 OpenSSH。“核心映像最小”映像不包含 SSH 服务器。

You can customize your image and change these defaults. Edit the `IMAGE_FEATURES`{.interpreted-text role="term"} variable in your recipe or use the `EXTRA_IMAGE_FEATURES`{.interpreted-text role="term"} in your `local.conf` file so that it configures the image you are working with to include `ssh-server-dropbear` or `ssh-server-openssh`.

> 您可以自定义您的图像并更改这些默认值。编辑配方中的 `IMAGE_FATURE`｛.depreted text role=“term”｝变量，或使用 `local.conf` 文件中的 `EXTRA_IMAGE_FEATURE'｛.deverted text rol=“term“｝，以便它将您正在使用的映像配置为包括` ssh server dropbear `或` ssh server openssh`。

::: note
::: title
Note
:::

See the \"`ref-manual/features:image features`{.interpreted-text role="ref"}\" section in the Yocto Project Reference Manual for a complete list of image features that ship with the Yocto Project.

> 请参阅 Yocto 项目参考手册中的“`ref manual/features:image features`｛.explored text role=“ref”｝\”一节，了解 Yocto 工程附带的图像功能的完整列表。
> :::

# Customizing Images Using Custom .bb Files

You can also customize an image by creating a custom recipe that defines additional software as part of the image. The following example shows the form for the two lines you need:

> 您还可以通过创建自定义配方来自定义图像，该配方将其他软件定义为图像的一部分。以下示例显示了您需要的两行的表单：

```
IMAGE_INSTALL = "packagegroup-core-x11-base package1 package2"
inherit core-image
```

Defining the software using a custom recipe gives you total control over the contents of the image. It is important to use the correct names of packages in the `IMAGE_INSTALL`{.interpreted-text role="term"} variable. You must use the OpenEmbedded notation and not the Debian notation for the names (e.g. `glibc-dev` instead of `libc6-dev`).

> 使用自定义配方定义软件可以完全控制图像的内容。在 `IMAGE_INSTALL`｛.explored text role=“term”｝变量中使用正确的包名称非常重要。名称必须使用 OpenEmbedded 表示法，而不是 Debian 表示法（例如“glibc dev”而不是“libc6 dev”）。

The other method for creating a custom image is to base it on an existing image. For example, if you want to create an image based on `core-image-sato` but add the additional package `strace` to the image, copy the `meta/recipes-sato/images/core-image-sato.bb` to a new `.bb` and add the following line to the end of the copy:

> 创建自定义图像的另一种方法是以现有图像为基础。例如，如果您想创建一个基于“核心图像 sato”的图像，但将附加包“strace”添加到图像中，请将“meta/precipes sato/images/core image sato.bb”复制到新的“.bb”中，并在副本末尾添加以下行：

```
IMAGE_INSTALL += "strace"
```

# Customizing Images Using Custom Package Groups

For complex custom images, the best approach for customizing an image is to create a custom package group recipe that is used to build the image or images. A good example of a package group recipe is `meta/recipes-core/packagegroups/packagegroup-base.bb`.

> 对于复杂的自定义图像，自定义图像的最佳方法是创建用于构建图像的自定义包组配方。包组配方的一个很好的例子是“meta/precipes core/packagegroups/packagegroup base.bb”。

If you examine that recipe, you see that the `PACKAGES`{.interpreted-text role="term"} variable lists the package group packages to produce. The `inherit packagegroup` statement sets appropriate default values and automatically adds `-dev`, `-dbg`, and `-ptest` complementary packages for each package specified in the `PACKAGES`{.interpreted-text role="term"} statement.

> 如果您检查该配方，您会看到 `PACKAGES`｛.explored text role=“term”｝变量列出了要生产的包组包。“inherit packagegroup”语句设置适当的默认值，并自动为“packages”｛.explored text role=“term”｝语句中指定的每个包添加“-dev”、“-dbg”和“-ptest”补充包。

::: note
::: title
Note
:::

The `inherit packagegroup` line should be located near the top of the recipe, certainly before the `PACKAGES`{.interpreted-text role="term"} statement.

> “inherit packagegroup”行应该位于配方的顶部附近，当然是在“PACKAGES”{.depredicted text role=“term”}语句之前。
> :::

For each package you specify in `PACKAGES`{.interpreted-text role="term"}, you can use `RDEPENDS`{.interpreted-text role="term"} and `RRECOMMENDS`{.interpreted-text role="term"} entries to provide a list of packages the parent task package should contain. You can see examples of these further down in the `packagegroup-base.bb` recipe.

> 对于您在 `PACKAGES`｛.explored text role=“term”｝中指定的每个包，您可以使用 `RDEPENDS`{.explered text rol=“term）”｝和 `RRECOMMENDS`｛。您可以在“packagegroupbase.bb”配方中看到这些示例。

Here is a short, fabricated example showing the same basic pieces for a hypothetical packagegroup defined in `packagegroup-custom.bb`, where the variable `PN`{.interpreted-text role="term"} is the standard way to abbreviate the reference to the full packagegroup name `packagegroup-custom`:

> 这里是一个简短的、捏造的例子，显示了在 `packagegroup-custom.bb` 中定义的假设 packagegroup 的相同基本部分，其中变量 `PN`｛.explored text role=“term”｝是缩写对完整 packagegroup 名称 `packagegroup custom` 的引用的标准方式：

```
DESCRIPTION = "My Custom Package Groups"

inherit packagegroup

PACKAGES = "\
    ${PN}-apps \
    ${PN}-tools \
    "

RDEPENDS:${PN}-apps = "\
    dropbear \
    portmap \
    psplash"

RDEPENDS:${PN}-tools = "\
    oprofile \
    oprofileui-server \
    lttng-tools"

RRECOMMENDS:${PN}-tools = "\
    kernel-module-oprofile"
```

In the previous example, two package group packages are created with their dependencies and their recommended package dependencies listed: `packagegroup-custom-apps`, and `packagegroup-custom-tools`. To build an image using these package group packages, you need to add `packagegroup-custom-apps` and/or `packagegroup-custom-tools` to `IMAGE_INSTALL`{.interpreted-text role="term"}. For other forms of image dependencies see the other areas of this section.

> 在前面的示例中，创建了两个包组包，并列出了它们的依赖项和推荐的包依赖项：“packagegroup 自定义应用程序”和“packagegroup 自定义工具”。若要使用这些软件包组包构建映像，您需要将 `packagegroup自定义应用程序` 和/或 `packagegroup自定义工具` 添加到 `image_INSTALL`｛.explored text role=“term”｝。有关其他形式的图像相关性，请参阅本节的其他部分。

# Customizing an Image Hostname

By default, the configured hostname (i.e. `/etc/hostname`) in an image is the same as the machine name. For example, if `MACHINE`{.interpreted-text role="term"} equals \"qemux86\", the configured hostname written to `/etc/hostname` is \"qemux86\".

> 默认情况下，映像中配置的主机名（即“/etc/hostname”）与机器名称相同。例如，如果 `MACHINE`｛.explored text role=“term”｝等于\“qemux86\”，则写入 `/etc/hostname` 的配置主机名为\“qemux86@”。

You can customize this name by altering the value of the \"hostname\" variable in the `base-files` recipe using either an append file or a configuration file. Use the following in an append file:

> 您可以通过使用附加文件或配置文件更改“基本文件”配方中的“主机名”变量的值来自定义此名称。在附加文件中使用以下内容：

```
hostname = "myhostname"
```

Use the following in a configuration file:

> 在配置文件中使用以下内容：

```
hostname:pn-base-files = "myhostname"
```

Changing the default value of the variable \"hostname\" can be useful in certain situations. For example, suppose you need to do extensive testing on an image and you would like to easily identify the image under test from existing images with typical default hostnames. In this situation, you could change the default hostname to \"testme\", which results in all the images using the name \"testme\". Once testing is complete and you do not need to rebuild the image for test any longer, you can easily reset the default hostname.

> 在某些情况下，更改变量“hostname”的默认值可能很有用。例如，假设您需要对一个映像进行广泛的测试，并且您希望从具有典型默认主机名的现有映像中轻松识别测试中的映像。在这种情况下，您可以将默认主机名更改为\“testme\”，这将导致所有图像都使用名称\“testme \”。一旦测试完成，并且不再需要重新构建用于测试的映像，就可以轻松地重置默认主机名。

Another point of interest is that if you unset the variable, the image will have no default hostname in the filesystem. Here is an example that unsets the variable in a configuration file:

> 另一个感兴趣的点是，如果取消设置变量，则映像在文件系统中将没有默认主机名。下面是一个在配置文件中取消设置变量的示例：

```
hostname:pn-base-files = ""
```

Having no default hostname in the filesystem is suitable for environments that use dynamic hostnames such as virtual machines.

> 文件系统中没有默认主机名适用于使用动态主机名（如虚拟机）的环境。
