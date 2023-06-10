---
tip: translate by openai@2023-06-07 20:48:34
title: Customizing the Extensible SDK standalone installer
---
This appendix describes customizations you can apply to the extensible SDK when using in the standalone installer version.

> 此附录描述了您可以在使用独立安装程序版本时应用于可扩展 SDK 的自定义设置。

::: note
::: title
Note
:::

It is also possible to use the Extensible SDK functionality directly in a Yocto build, avoiding separate installer artefacts. Please refer to \"`sdk-manual/extensible:Installing the Extensible SDK`\"

> 也可以直接在 Yocto 构建中使用可扩展 SDK 功能，避免单独的安装程序工件。请参阅“sdk-manual / extensible：安装可扩展 SDK”。
> :::

# Configuring the Extensible SDK

The extensible SDK primarily consists of a pre-configured copy of the OpenEmbedded build system from which it was produced. Thus, the SDK\'s configuration is derived using that build system and the filters shown in the following list. When these filters are present, the OpenEmbedded build system applies them against `local.conf` and `auto.conf`:

> SDK 可扩展性主要包括一个预先配置的 OpenEmbedded 构建系统的副本，它就是由此产生的。因此，SDK 的配置是使用该构建系统和以下列表中显示的过滤器派生的。当这些过滤器存在时，OpenEmbedded 构建系统将它们应用于 `local.conf` 和 `auto.conf`：

- Variables whose values start with \"/\" are excluded since the assumption is that those values are paths that are likely to be specific to the `Build Host`.

> 变量的值以"/"开头的被排除，因为假设这些值可能特定于构建主机。

- Variables listed in `ESDK_LOCALCONF_REMOVE` are excluded. These variables are not allowed through from the OpenEmbedded build system configuration into the extensible SDK configuration. Typically, these variables are specific to the machine on which the build system is running and could be problematic as part of the extensible SDK configuration.

> 变量列在 `ESDK_LOCALCONF_REMOVE` 中被排除在外。这些变量不允许从 OpenEmbedded 构建系统配置传递到可扩展 SDK 配置中。通常，这些变量是特定于构建系统运行的机器，可能会成为可扩展 SDK 配置的问题。

For a list of the variables excluded by default, see the `ESDK_LOCALCONF_REMOVE` in the glossary of the Yocto Project Reference Manual.

> 查看默认排除的变量列表，请参阅 Yocto Project 参考手册中的 `ESDK_LOCALCONF_REMOVE` 词汇表。

- Variables listed in `ESDK_LOCALCONF_ALLOW` overrides either of the previous two filters. The default value is blank.

> 变量列出在 `ESDK_LOCALCONF_ALLOW` 中将被包含。将变量包含在 `ESDK_LOCALCONF_ALLOW` 的值中将会覆盖前面两个过滤器中的任意一个。默认值为空。

- Classes inherited globally with `INHERIT` classes.

> 所有使用 `INHERIT` 类。

Additionally, the contents of `conf/sdk-extra.conf`, when present, are appended to the end of `conf/local.conf` within the produced SDK, without any filtering. The `sdk-extra.conf` file is particularly useful if you want to set a variable value just for the SDK and not the OpenEmbedded build system used to create the SDK.

> 此外，当存在时，`conf/sdk-extra.conf` 的内容会被附加到生成的 SDK 中 `conf/local.conf` 的末尾，而不会进行任何过滤。如果您想为 SDK 而不是用于创建 SDK 的 OpenEmbedded 构建系统设置变量值，则 `sdk-extra.conf` 文件特别有用。

# Adjusting the Extensible SDK to Suit Your Build Host\'s Setup

In most cases, the extensible SDK defaults should work with your `Build Host`\'s setup. However, there are cases when you might consider making adjustments:

> 在大多数情况下，可扩展的 SDK 默认设置应该与您的 `构建主机` 的设置兼容。但是，有时您可能考虑做出调整：

- If your SDK configuration inherits additional classes using the `INHERIT` variable as described in the previous section.

> 如果您的 SDK 配置使用 `INHERIT` 变量继承额外的类，而您不需要或不想在 SDK 中启用这些类，您可以通过按照前一节中的说明将它们添加到 `ESDK_CLASS_INHERIT_DISABLE` 变量中来禁用它们。

::: note
::: title

Note

> 注意
> :::

The default value of `ESDK_CLASS_INHERIT_DISABLE`\" section of the BitBake User Manual.

> 默认值 `ESDK_CLASS_INHERIT_DISABLE` 使用“?=”操作符设置。因此，您需要使用“=”操作符来定义整个列表，或者使用“:append”或“+=”操作符来附加值。您可以在 BitBake 用户手册的“基本语法”部分中了解有关这些操作符的更多信息。
> :::

- If you have classes or recipes that add additional tasks to the standard build flow (i.e. the tasks execute as the recipe builds as opposed to being called explicitly), then you need to do one of the following:

> 如果您有添加额外任务到标准构建流程的类或配方(即任务在配方构建时执行，而不是显式调用)，则需要执行以下操作之一：

- After ensuring the tasks are `shared state <overview-manual/concepts:shared state cache>`.

> 确保任务是共享状态(参见共享状态缓存)任务(即任务的输出可以保存到并从共享状态缓存中恢复)或者确保任务可以快速从一个共享状态任务产生，将任务名称添加到 SDK_RECRDEP_TASKS 的值中。

- Disable the tasks if they are added by a class and you do not need the functionality the class provides in the extensible SDK. To disable the tasks, add the class to the `ESDK_CLASS_INHERIT_DISABLE` variable as described in the previous section.

> 如果任务是由类添加的，而您不需要可扩展 SDK 中提供的功能，请禁用该任务。要禁用任务，请按照上一节中的说明将类添加到 ESDK_CLASS_INHERIT_DISABLE 变量中。

- Generally, you want to have a shared state mirror set up so users of the SDK can add additional items to the SDK after installation without needing to build the items from source. See the \"`sdk-manual/appendix-customizing:providing additional installable extensible sdk content`\" section for information.

> 一般来说，您希望设置共享状态镜像，以便 SDK 的用户在安装后可以添加额外的项目，而无需从源代码构建项目。有关信息，请参阅“ sdk-manual / appendix-customizing：提供额外可安装的可扩展 SDK 内容”部分。

- If you want users of the SDK to be able to easily update the SDK, you need to set the `SDK_UPDATE_URL`\" section.

> 如果你希望 SDK 的用户能够轻松地更新 SDK，你需要设置 `SDK_UPDATE_URL` 变量。有关更多信息，请参见“sdk-manual/appendix-customizing：在安装后为可扩展 SDK 提供更新”部分。

- If you have adjusted the list of files and directories that appear in `COREBASE` so that the files are copied into the SDK.

> 如果您已经调整了在 COREBASE(除通过 bblayers.conf 启用的层外)中显示的文件和目录列表，那么您必须将这些文件列入 COREBASE_FILES 中，以便将这些文件复制到 SDK 中。

- If your OpenEmbedded build system setup uses a different environment setup script other than `structure-core-script` to point to the environment setup script you use.

> 如果您的 OpenEmbedded 构建系统设置使用的不是 `structure-core-script` 设置为指向您使用的环境设置脚本。

::: note
::: title

Note

> 注意
> :::

You must also reflect this change in the value used for the `COREBASE_FILES` variable as previously described.

> 你必须根据先前描述的方式，也要把这个变化反映到 `COREBASE_FILES` 变量的值中。
> :::

# Changing the Extensible SDK Installer Title

You can change the displayed title for the SDK installer by setting the `SDK_TITLE`\" section.

> 你可以通过设置 `SDK_TITLE` 变量来更改 SDK 安装程序显示的标题，然后重新构建 SDK 安装程序。有关如何构建 SDK 安装程序的信息，请参阅“sdk-manual/appendix-obtain：构建 SDK 安装程序”部分。

By default, this title is derived from `DISTRO_NAME` variable.

> 默认情况下，如果设置了 `DISTRO_NAME` 变量，此标题就会从其中派生出来。如果未设置 `DISTRO_NAME` 变量，则标题将从 `DISTRO` 变量中派生出来。

The `populate_sdk_base <ref-classes-populate-sdk-*>` variable as follows:

> 类 populate_sdk_base<ref-classes-populate-sdk-\*> 的默认值如下：

```
SDK_TITLE ??= "$ SDK"
```

While there are several ways of changing this variable, an efficient method is to set the variable in your distribution\'s configuration file. Doing so creates an SDK installer title that applies across your distribution. As an example, assume you have your own layer for your distribution named \"meta-mydistro\" and you are using the same type of file hierarchy as does the default \"poky\" distribution. If so, you could update the `SDK_TITLE` variable in the `~/meta-mydistro/conf/distro/mydistro.conf` file using the following form:

> 有几种方法可以更改此变量，一种有效的方法是在您的发行版的配置文件中设置变量。这样可以创建一个应用于整个发行版的 SDK 安装程序标题。例如，假设您为自己的发行版创建了名为“meta-mydistro”的自定义层，并且使用与默认的“poky”发行版相同的文件层次结构。如果是这样，您可以使用以下形式更新 `~/meta-mydistro/conf/distro/mydistro.conf` 文件中的 `SDK_TITLE` 变量：

```
SDK_TITLE = "your_title"
```

# Providing Updates to the Extensible SDK After Installation

When you make changes to your configuration or to the metadata and if you want those changes to be reflected in installed SDKs, you need to perform additional steps. These steps make it possible for anyone using the installed SDKs to update the installed SDKs by using the `devtool sdk-update` command:

> 如果您对配置或元数据做出了更改，并且希望这些更改反映在已安装的 SDK 中，则需要执行额外的步骤。这些步骤使任何使用已安装的 SDK 的人都可以通过使用 `devtool sdk-update` 命令更新已安装的 SDK：

1. Create a directory that can be shared over HTTP or HTTPS. You can do this by setting up a web server such as an `Apache HTTP Server <Apache_HTTP_Server>` server in the cloud to host the directory. This directory must contain the published SDK.

> 创建一个可以通过 HTTP 或 HTTPS 共享的目录。您可以通过在云中设置一个 Web 服务器，如 Apache HTTP 服务器或 Nginx 服务器来实现这一目的。此目录必须包含已发布的 SDK。

2. Set the `SDK_UPDATE_URL`\" section.

> 设置 `SDK_UPDATE_URL` 变量指向相应的 HTTP 或 HTTPS URL。设置此变量会使任何建立的 SDK 默认为该 URL，因此，用户不必将 URL 传递给“devtool sdk-update”命令，如“sdk-manual/extensible：将更新应用于已安装的可扩展 SDK”部分所述。

3. Build the extensible SDK normally (i.e., use the `bitbake -c populate_sdk_ext` imagename command).

> 使用 `bitbake -c populate_sdk_ext` imagename 命令正常构建可扩展的 SDK。

4. Publish the SDK using the following command:

> 使用以下命令发布 SDK：

````

> 请帮助我翻译：```

$ oe-publish-sdk some_path/sdk-installer.sh path_to_shared_http_directory

> $ oe-publish-sdk 一些路徑/sdk-installer.sh 路徑到共享的HTTP目錄

````

> 请帮助我翻译：```

You must repeat this step each time you rebuild the SDK with changes that you want to make available through the update mechanism.

> 你必须每次重新构建 SDK 时都要重复这一步，以便通过更新机制将所做的更改提供给用户。

Completing the above steps allows users of the existing installed SDKs to simply run `devtool sdk-update` to retrieve and apply the latest updates. See the \"`sdk-manual/extensible:applying updates to an installed extensible sdk`\" section for further information.

> 完成以上步骤，现有安装的 SDK 用户可以简单地运行“devtool sdk-update”以获取并应用最新更新。有关更多信息，请参见“sdk-manual/extensible：将更新应用于已安装的可扩展 SDK”部分。

# Changing the Default SDK Installation Directory

When you build the installer for the Extensible SDK, the default installation directory for the SDK is based on the `DISTRO` class as follows:

> 当您为可扩展 SDK 构建安装程序时，SDK 的默认安装目录基于来自 `populate_sdk_base <ref-classes-populate-sdk-*>` 变量，如下所示：

```
SDKEXTPATH ??= "~/$_sdk"
```

You can change this default installation directory by specifically setting the `SDKEXTPATH` variable.

> 你可以通过特定设置 `SDKEXTPATH` 变量来更改这个默认的安装目录。

While there are several ways of setting this variable, the method that makes the most sense is to set the variable in your distribution\'s configuration file. Doing so creates an SDK installer default directory that applies across your distribution. As an example, assume you have your own layer for your distribution named \"meta-mydistro\" and you are using the same type of file hierarchy as does the default \"poky\" distribution. If so, you could update the `SDKEXTPATH` variable in the `~/meta-mydistro/conf/distro/mydistro.conf` file using the following form:

> 有几种方法可以设置此变量，最合理的方法是在您的发行版配置文件中设置变量。这样可以创建一个适用于您的发行版的 SDK 安装程序默认目录。例如，假设您为您的发行版创建了名为“meta-mydistro”的自定义层，并且使用与默认“poky”发行版相同的文件层次结构。如果是这样，您可以使用以下格式更新 `~/meta-mydistro/conf/distro/mydistro.conf` 文件中的 `SDKEXTPATH` 变量：

```
SDKEXTPATH = "some_path_for_your_installed_sdk"
```

After building your installer, running it prompts the user for acceptance of the some_path_for_your_installed_sdk directory as the default location to install the Extensible SDK.

> 在构建安装程序后，运行它会提示用户接受 some_path_for_your_installed_sdk 目录作为安装可扩展 SDK 的默认位置。

# Providing Additional Installable Extensible SDK Content

If you want the users of an extensible SDK you build to be able to add items to the SDK without requiring the users to build the items from source, you need to do a number of things:

> 如果你想让你构建的可扩展 SDK 的用户能够添加项目而无需用户从源码构建，你需要做几件事：

1. Ensure the additional items you want the user to be able to install are already built:

> 确保已经构建了用户需要安装的额外项目。

- Build the items explicitly. You could use one or more \"meta\" recipes that depend on lists of other recipes.

> 明确构建物品。您可以使用一个或多个“元”配方，这些配方取决于其他配方的列表。

- Build the \"world\" target and set `EXCLUDE_FROM_WORLD:pn-` recipename for the recipes you do not want built. See the `EXCLUDE_FROM_WORLD` variable for additional information.

> 构建“world”目标，并为不想构建的 recipes 设置 `EXCLUDE_FROM_WORLD:pn-` recipes 名称。有关其他信息，请参阅 `EXCLUDE_FROM_WORLD` 变量。

2. Expose the `sstate-cache` directory produced by the build. Typically, you expose this directory by making it available through an `Apache HTTP Server <Apache_HTTP_Server>` server.

> 将构建产生的 `sstate-cache` 目录暴露出来。通常，您可以通过 `Apache HTTP Server <Apache_HTTP_Server>` 服务器来实现这一目的。

3. Set the appropriate configuration so that the produced SDK knows how to find the configuration. The variable you need to set is `SSTATE_MIRRORS`:

> 设置适当的配置，以便生成的 SDK 知道如何找到配置。您需要设置的变量是 SSTATE_MIRRORS。

````

> 请帮助我翻译：```

SSTATE_MIRRORS = "file://.* https://example.com/some_path/sstate-cache/PATH"

> SSTATE_MIRRORS = "file://.* https://example.com/some_path/sstate-cache/PATH"
SSTATE_MIRRORS = "文件://.* https://example.com/some_path/sstate-cache/PATH"

````

> 请帮助我翻译，```

You can set the `SSTATE_MIRRORS` variable in two different places:

> 你可以在两个不同的地方设置 `SSTATE_MIRRORS` 变量：

- If the mirror value you are setting is appropriate to be set for both the OpenEmbedded build system that is actually building the SDK and the SDK itself (i.e. the mirror is accessible in both places or it will fail quickly on the OpenEmbedded build system side, and its contents will not interfere with the build), then you can set the variable in your `local.conf` or custom distro configuration file. You can then pass the variable to the SDK by adding the following:

> 如果您设置的镜像值适用于正在构建 SDK 的 OpenEmbedded 构建系统和 SDK 本身(即镜像在这两个地方都可以访问，或者在 OpenEmbedded 构建系统端会很快失败，其内容不会影响构建)，那么您可以在 `local.conf` 或自定义发行版配置文件中设置该变量。然后，您可以通过添加以下内容将变量传递给 SDK：

```

```

ESDK_LOCALCONF_ALLOW = "SSTATE_MIRRORS"

```

```

- Alternatively, if you just want to set the `SSTATE_MIRRORS` setting within that file.

> 如果您只想为 SDK 设置 `SSTATE_MIRRORS` 变量的值，请在构建目录或任何图层中创建一个 `conf/sdk-extra.conf` 文件，并将您的 `SSTATE_MIRRORS` 设置放入该文件中。

```
 ::: note
 ::: title
 Note
 :::

 This second option is the safest option should you have any doubts as to which method to use when setting `SSTATE_MIRRORS`
 :::
```

# Minimizing the Size of the Extensible SDK Installer Download

By default, the extensible SDK bundles the shared state artifacts for everything needed to reconstruct the image for which the SDK was built. This bundling can lead to an SDK installer file that is a Gigabyte or more in size. If the size of this file causes a problem, you can build an SDK that has just enough in it to install and provide access to the `devtool command` by setting the following in your configuration:

> 默认情况下，可扩展 SDK 将共享状态工件打包在一起，以便重建 SDK 构建的映像。此打包可导致 SDK 安装程序文件的大小达到 1 GB 或更大。如果这个文件的大小引起问题，您可以构建一个仅包含安装和提供访问 `devtool命令` 所需的足够内容的 SDK，方法是在配置中设置以下内容：

```
SDK_EXT_TYPE = "minimal"
```

Setting `SDK_EXT_TYPE` to \"minimal\" produces an SDK installer that is around 35 Mbytes in size, which downloads and installs quickly. You need to realize, though, that the minimal installer does not install any libraries or tools out of the box. These libraries and tools must be installed either \"on the fly\" or through actions you perform using `devtool` or explicitly with the `devtool sdk-install` command.

> 设置 SDK_EXT_TYPE 为“最小”可以生成大约 35M 字节的 SDK 安装程序，下载和安装速度很快。但是，你需要意识到，最小安装程序不会默认安装任何库或工具。这些库和工具必须通过 devtool 或显式使用 devtool sdk-install 命令进行“即时”安装或操作来安装。

In most cases, when building a minimal SDK you need to also enable bringing in the information on a wider range of packages produced by the system. Requiring this wider range of information is particularly true so that `devtool add` is able to effectively map dependencies it discovers in a source tree to the appropriate recipes. Additionally, the information enables the `devtool search` command to return useful results.

> 在大多数情况下，当构建最小 SDK 时，您还需要启用获取系统生成的更广泛的软件包信息。特别要求更广泛的信息是因为 `devtool add` 能够有效地将源树中发现的依赖关系映射到适当的配方。另外，这些信息使 `devtool search` 命令返回有用的结果。

To facilitate this wider range of information, you would need to set the following:

> 为了方便这种更广泛的信息，你需要设置以下内容：

```
SDK_INCLUDE_PKGDATA = "1"
```

See the `SDK_INCLUDE_PKGDATA` variable for additional information.

> 查看 `SDK_INCLUDE_PKGDATA` 变量以获取更多信息。

Setting the `SDK_INCLUDE_PKGDATA` variable as shown causes the \"world\" target to be built so that information for all of the recipes included within it are available. Having these recipes available increases build time significantly and increases the size of the SDK installer by 30-80 Mbytes depending on how many recipes are included in your configuration.

> 设置 `SDK_INCLUDE_PKGDATA` 变量如所示会导致“world”目标被构建，以便其中包含的所有配方信息可用。拥有这些配方可显著增加构建时间，并根据您的配置中包含的配方数量，将 SDK 安装程序的大小增加 30-80 MB。

You can use `EXCLUDE_FROM_WORLD:pn-` recipename for recipes you want to exclude. However, it is assumed that you would need to be building the \"world\" target if you want to provide additional items to the SDK. Consequently, building for \"world\" should not represent undue overhead in most cases.

> 你可以使用 `EXCLUDE_FROM_WORLD：pn-` recipename 来排除你想要排除的 recipes。但是，假设如果你想为 SDK 提供额外的项目，你需要构建“世界”目标。因此，在大多数情况下，为“世界”构建不应该代表不适当的开销。

::: note
::: title
Note
:::

If you set SDK_EXT_TYPE to \"minimal\", then providing a shared state mirror is mandatory so that items can be installed as needed. See the `sdk-manual/appendix-customizing:providing additional installable extensible sdk content` section for more information.

> 如果您将 SDK_EXT_TYPE 设置为“最小”，则必须提供共享状态镜像，以便可以根据需要安装项目。有关更多信息，请参见 sdk-manual / appendix-customizing：提供可安装的可扩展 SDK 内容部分。
> :::

You can explicitly control whether or not to include the toolchain when you build an SDK by setting the `SDK_INCLUDE_TOOLCHAIN` to \"minimal\", which by default, excludes the toolchain. Also, it is helpful if you are building a small SDK for use with an IDE or some other tool where you do not want to take extra steps to install a toolchain.

> 您可以通过将 `SDK_INCLUDE_TOOLCHAIN` 变量设置为“1”来显式控制是否在构建 SDK 时包括工具链。特别是，当您将 `SDK_EXT_TYPE` 设置为“最小”时，包括工具链是有用的，默认情况下，会排除工具链。此外，如果您正在构建一个小型 SDK 用于 IDE 或其他工具，您不想采取额外步骤来安装工具链，则很有帮助。
