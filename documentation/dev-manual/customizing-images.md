---
tip: translate by openai@2023-06-10 10:24:20
...
---
title: Customizing Images
-------------------------

You can customize images to satisfy particular requirements. This section describes several methods and provides guidelines for each.

# Customizing Images Using `local.conf`

Probably the easiest way to customize an image is to add a package by way of the `local.conf` configuration file. Because it is limited to local use, this method generally only allows you to add packages and is not as flexible as creating your own customized image. When you add packages using local variables this way, you need to realize that these variable changes are in effect for every build and consequently affect all images, which might not be what you require.

> 可能最简单的自定义图像的方法是通过 `local.conf` 配置文件添加一个包。由于它仅限于本地使用，因此此方法通常仅允许添加软件包，而不如创建自己的自定义图像那么灵活。当您使用此方法添加软件包时，您需要意识到这些变量更改对每次构建都有效，并因此影响所有图像，这可能不是您所需要的。

To add a package to your image using the local configuration file, use the `IMAGE_INSTALL`{.interpreted-text role="term"} variable with the `:append` operator:

> 使用本地配置文件向图像添加包，请使用带有 `:append` 操作符的 `IMAGE_INSTALL`{.interpreted-text role="term"}变量：

```
IMAGE_INSTALL:append = " strace"
```

Use of the syntax is important; specifically, the leading space after the opening quote and before the package name, which is `strace` in this example. This space is required since the `:append` operator does not add the space.

> 使用语法很重要；具体而言，开头引号后面的空格以及软件包名（在这个例子中是 `strace`）之前的空格是必需的，因为 `:append` 操作符不会添加空格。

Furthermore, you must use `:append` instead of the `+=` operator if you want to avoid ordering issues. The reason for this is because doing so unconditionally appends to the variable and avoids ordering problems due to the variable being set in image recipes and `.bbclass` files with operators like `?=`. Using `:append` ensures the operation takes effect.

> 此外，如果要避免排序问题，必须使用 `:append` 而不是 `+=` 操作符。这是因为使用它可以无条件地附加到变量中，从而避免由于使用 `?=` 等操作符在图像配方和 `.bbclass` 文件中设置变量而导致的排序问题。使用 `:append` 可以确保操作生效。

As shown in its simplest use, `IMAGE_INSTALL:append` affects all images. It is possible to extend the syntax so that the variable applies to a specific image only. Here is an example:

> IMAGE_INSTALL:append 影响所有图像的最简单用法如上所示。可以扩展语法，使变量仅适用于特定图像。这里有一个例子：

```
IMAGE_INSTALL:append:pn-core-image-minimal = " strace"
```

This example adds `strace` to the `core-image-minimal` image only.

You can add packages using a similar approach through the `CORE_IMAGE_EXTRA_INSTALL`{.interpreted-text role="term"} variable. If you use this variable, only `core-image-*` images are affected.

> 你可以通过类似的方法使用 `CORE_IMAGE_EXTRA_INSTALL` 变量添加软件包。如果你使用这个变量，只有 `core-image-*` 图像会受到影响。

# Customizing Images Using Custom `IMAGE_FEATURES` and `EXTRA_IMAGE_FEATURES`

Another method for customizing your image is to enable or disable high-level image features by using the `IMAGE_FEATURES`{.interpreted-text role="term"} and `EXTRA_IMAGE_FEATURES`{.interpreted-text role="term"} variables. Although the functions for both variables are nearly equivalent, best practices dictate using `IMAGE_FEATURES`{.interpreted-text role="term"} from within a recipe and using `EXTRA_IMAGE_FEATURES`{.interpreted-text role="term"} from within your `local.conf` file, which is found in the `Build Directory`{.interpreted-text role="term"}.

> 另一种自定义图像的方法是通过使用 `IMAGE_FEATURES` 和 `EXTRA_IMAGE_FEATURES` 变量来启用或禁用高级图像功能。虽然这两个变量的功能几乎相同，但最佳实践是从配方中使用 `IMAGE_FEATURES`，从 `Build Directory` 中的 `local.conf` 文件中使用 `EXTRA_IMAGE_FEATURES`。

To understand how these features work, the best reference is `meta/classes-recipe/image.bbclass <ref-classes-image>`{.interpreted-text role="ref"}. This class lists out the available `IMAGE_FEATURES`{.interpreted-text role="term"} of which most map to package groups while some, such as `debug-tweaks` and `read-only-rootfs`, resolve as general configuration settings.

> 要了解这些功能如何工作，最佳参考是 `meta/classes-recipe/image.bbclass <ref-classes-image>`{.interpreted-text role="ref"}。该类列出了可用的 `IMAGE_FEATURES`{.interpreted-text role="term"}，其中大多数映射到软件包组，而一些，例如 `debug-tweaks` 和 `read-only-rootfs`，解析为一般配置设置。

In summary, the file looks at the contents of the `IMAGE_FEATURES`{.interpreted-text role="term"} variable and then maps or configures the feature accordingly. Based on this information, the build system automatically adds the appropriate packages or configurations to the `IMAGE_INSTALL`{.interpreted-text role="term"} variable. Effectively, you are enabling extra features by extending the class or creating a custom class for use with specialized image `.bb` files.

> 总之，该文件研究 `IMAGE_FEATURES` 变量的内容，然后根据该信息映射或配置特征。根据此信息，构建系统会自动将适当的软件包或配置添加到 `IMAGE_INSTALL` 变量中。有效地，您可以通过为专用图像 `.bb` 文件扩展类或创建自定义类来启用额外的功能。

Use the `EXTRA_IMAGE_FEATURES`{.interpreted-text role="term"} variable from within your local configuration file. Using a separate area from which to enable features with this variable helps you avoid overwriting the features in the image recipe that are enabled with `IMAGE_FEATURES`{.interpreted-text role="term"}. The value of `EXTRA_IMAGE_FEATURES`{.interpreted-text role="term"} is added to `IMAGE_FEATURES`{.interpreted-text role="term"} within `meta/conf/bitbake.conf`.

> 使用本地配置文件中的 `EXTRA_IMAGE_FEATURES` 变量。使用一个单独的区域来启用此变量的功能可以帮助您避免覆盖图像配方中使用 `IMAGE_FEATURES` 启用的功能。 `EXTRA_IMAGE_FEATURES` 的值将添加到 `meta / conf / bitbake.conf` 中的 `IMAGE_FEATURES` 中。

To illustrate how you can use these variables to modify your image, consider an example that selects the SSH server. The Yocto Project ships with two SSH servers you can use with your images: Dropbear and OpenSSH. Dropbear is a minimal SSH server appropriate for resource-constrained environments, while OpenSSH is a well-known standard SSH server implementation. By default, the `core-image-sato` image is configured to use Dropbear. The `core-image-full-cmdline` and `core-image-lsb` images both include OpenSSH. The `core-image-minimal` image does not contain an SSH server.

> 为了说明你如何使用这些变量来修改你的图像，考虑一个选择 SSH 服务器的例子。Yocto 项目附带了两个可以用于图像的 SSH 服务器：Dropbear 和 OpenSSH。Dropbear 是一个适合资源受限环境的最小 SSH 服务器，而 OpenSSH 是一个众所周知的标准 SSH 服务器实现。默认情况下，`core-image-sato` 图像配置为使用 Dropbear。`core-image-full-cmdline` 和 `core-image-lsb` 图像都包含 OpenSSH。`core-image-minimal` 图像不包含 SSH 服务器。

You can customize your image and change these defaults. Edit the `IMAGE_FEATURES`{.interpreted-text role="term"} variable in your recipe or use the `EXTRA_IMAGE_FEATURES`{.interpreted-text role="term"} in your `local.conf` file so that it configures the image you are working with to include `ssh-server-dropbear` or `ssh-server-openssh`.

> 您可以自定义您的镜像，并更改这些默认值。在配方中编辑 `IMAGE_FEATURES` 变量，或者在 `local.conf` 文件中使用 `EXTRA_IMAGE_FEATURES`，以便将您正在使用的镜像配置为包括 `ssh-server-dropbear` 或 `ssh-server-openssh`。

::: note
::: title
Note
:::

See the \"`ref-manual/features:image features`{.interpreted-text role="ref"}\" section in the Yocto Project Reference Manual for a complete list of image features that ship with the Yocto Project.

> 请参阅 Yocto Project 参考手册中的“ref-manual/features:image features”部分，了解 Yocto Project 所提供的完整图像功能列表。
> :::

# Customizing Images Using Custom .bb Files

You can also customize an image by creating a custom recipe that defines additional software as part of the image. The following example shows the form for the two lines you need:

> 您也可以通过创建自定义配方来自定义图像，该配方定义额外的软件作为图像的一部分。 以下示例显示了您需要的两行的表单：

```
IMAGE_INSTALL = "packagegroup-core-x11-base package1 package2"
inherit core-image
```

Defining the software using a custom recipe gives you total control over the contents of the image. It is important to use the correct names of packages in the `IMAGE_INSTALL`{.interpreted-text role="term"} variable. You must use the OpenEmbedded notation and not the Debian notation for the names (e.g. `glibc-dev` instead of `libc6-dev`).

> 定义软件使用自定义配方可以完全控制映像的内容。在 `IMAGE_INSTALL` 变量中使用正确的软件包名称很重要。您必须使用 OpenEmbedded 的符号而不是 Debian 的符号来表示名称（例如 `glibc-dev` 而不是 `libc6-dev`）。

The other method for creating a custom image is to base it on an existing image. For example, if you want to create an image based on `core-image-sato` but add the additional package `strace` to the image, copy the `meta/recipes-sato/images/core-image-sato.bb` to a new `.bb` and add the following line to the end of the copy:

> 另一种创建自定义图像的方法是基于现有图像。例如，如果要创建基于 `core-image-sato` 的图像，但要将额外的软件包 `strace` 添加到图像中，请将 `meta/recipes-sato/images/core-image-sato.bb` 复制到新的 `.bb` 文件，并在复制的末尾添加以下行：

```
IMAGE_INSTALL += "strace"
```

# Customizing Images Using Custom Package Groups

For complex custom images, the best approach for customizing an image is to create a custom package group recipe that is used to build the image or images. A good example of a package group recipe is `meta/recipes-core/packagegroups/packagegroup-base.bb`.

> 对于复杂的自定义图像，最佳的定制图像方法是创建一个用于构建图像或图像的自定义软件包组配方。一个好的软件包组配方示例是 `meta/recipes-core/packagegroups/packagegroup-base.bb`。

If you examine that recipe, you see that the `PACKAGES`{.interpreted-text role="term"} variable lists the package group packages to produce. The `inherit packagegroup` statement sets appropriate default values and automatically adds `-dev`, `-dbg`, and `-ptest` complementary packages for each package specified in the `PACKAGES`{.interpreted-text role="term"} statement.

> 如果你检查该食谱，你会发现 `PACKAGES` 变量列出了要生成的包组包。`inherit packagegroup` 语句设置合适的默认值，并且自动为 `PACKAGES` 语句中指定的每个包添加 `-dev`、`-dbg` 和 `-ptest` 补充包。

::: note
::: title
Note
:::

The `inherit packagegroup` line should be located near the top of the recipe, certainly before the `PACKAGES`{.interpreted-text role="term"} statement.

> `inherit packagegroup` 行应该位于食谱的顶部，肯定在 `PACKAGES` 语句之前。
> :::

For each package you specify in `PACKAGES`{.interpreted-text role="term"}, you can use `RDEPENDS`{.interpreted-text role="term"} and `RRECOMMENDS`{.interpreted-text role="term"} entries to provide a list of packages the parent task package should contain. You can see examples of these further down in the `packagegroup-base.bb` recipe.

> 对于您在 `PACKAGES`{.interpreted-text role="term"}中指定的每个包，您可以使用 `RDEPENDS`{.interpreted-text role="term"}和 `RRECOMMENDS`{.interpreted-text role="term"}条目提供父任务包应包含的包列表。您可以在 `packagegroup-base.bb` 配方中看到这些示例。

Here is a short, fabricated example showing the same basic pieces for a hypothetical packagegroup defined in `packagegroup-custom.bb`, where the variable `PN`{.interpreted-text role="term"} is the standard way to abbreviate the reference to the full packagegroup name `packagegroup-custom`:

> 这是一个简短的虚构示例，用于显示在 `packagegroup-custom.bb` 中定义的假设的 packagegroup 的基本部分，其中变量 `PN`{.interpreted-text role="term"}是引用完整的 packagegroup 名称 `packagegroup-custom` 的标准方式：

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

> 在前面的例子中，使用它们的依赖关系及其推荐的软件包依赖关系创建了两个软件包组：`packagegroup-custom-apps` 和 `packagegroup-custom-tools`。要使用这些软件包组构建映像，您需要将 `packagegroup-custom-apps` 和/或 `packagegroup-custom-tools` 添加到 `IMAGE_INSTALL`{.interpreted-text role="term"}。有关其他形式的映像依赖关系，请参阅本节的其他部分。

# Customizing an Image Hostname

By default, the configured hostname (i.e. `/etc/hostname`) in an image is the same as the machine name. For example, if `MACHINE`{.interpreted-text role="term"} equals \"qemux86\", the configured hostname written to `/etc/hostname` is \"qemux86\".

> 默认情况下，在镜像中配置的主机名（即 `/etc/hostname`）与机器名相同。例如，如果 `MACHINE` 等于“qemux86”，则写入 `/etc/hostname` 的配置主机名为“qemux86”。

You can customize this name by altering the value of the \"hostname\" variable in the `base-files` recipe using either an append file or a configuration file. Use the following in an append file:

> 你可以通过修改"hostname"变量在 base-files 食谱中的值来自定义这个名称。使用以下内容在附加文件中：

```
hostname = "myhostname"
```

Use the following in a configuration file:

```
hostname:pn-base-files = "myhostname"
```

Changing the default value of the variable \"hostname\" can be useful in certain situations. For example, suppose you need to do extensive testing on an image and you would like to easily identify the image under test from existing images with typical default hostnames. In this situation, you could change the default hostname to \"testme\", which results in all the images using the name \"testme\". Once testing is complete and you do not need to rebuild the image for test any longer, you can easily reset the default hostname.

> 更改变量“主机名”的默认值在某些情况下可能很有用。例如，假设您需要对图像进行大量测试，并希望能够从具有典型默认主机名的现有图像中轻松识别正在测试的图像。在这种情况下，您可以将默认主机名更改为“testme”，这样所有图像都将使用名称“testme”。一旦测试完成，您不再需要重新构建图像进行测试，您可以轻松重置默认主机名。

Another point of interest is that if you unset the variable, the image will have no default hostname in the filesystem. Here is an example that unsets the variable in a configuration file:

> 另一个有趣的点是，如果您取消设置变量，文件系统中将没有默认主机名。以下是在配置文件中取消设置变量的示例：

```
hostname:pn-base-files = ""
```

Having no default hostname in the filesystem is suitable for environments that use dynamic hostnames such as virtual machines.
