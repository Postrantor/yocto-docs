---
tip: translate by openai@2023-06-10 11:07:37
...
---
title: Working With Licenses
----------------------------

As mentioned in the \"`overview-manual/development-environment:licensing` tracks changes to licensing text and covers how to maintain open source license compliance during your project\'s lifecycle. The section also describes how to enable commercially licensed recipes, which by default are disabled.

> 按照 Yocto 项目概览和概念手册中“概述-手册/开发环境：许可”部分提到的，开源项目对公众开放，因此它们有不同的许可结构。本节描述了 OpenEmbedded 构建系统如何跟踪许可文本的变化，以及如何在项目的生命周期中维护开源许可合规性。该节还描述了如何启用商业许可的 recipes，默认情况下这些 recipes 是禁用的。

# Tracking License Changes

The license of an upstream project might change in the future. In order to prevent these changes going unnoticed, the `LIC_FILES_CHKSUM` variable tracks changes to the license text. The checksums are validated at the end of the configure step, and if the checksums do not match, the build will fail.

> 未来，上游项目的许可证可能会发生变化。为了防止这些变化不被注意到，`LIC_FILES_CHKSUM` 变量跟踪许可证文本的变化。校验和在配置步骤结束时进行验证，如果校验和不匹配，构建将失败。

## Specifying the `LIC_FILES_CHKSUM` Variable

The `LIC_FILES_CHKSUM`:

> `LIC_FILES_CHKSUM` 的示例：

```
LIC_FILES_CHKSUM = "file://COPYING;md5=xxxx \
                    file://licfile1.txt;beginline=5;endline=29;md5=yyyy \
                    file://licfile2.txt;endline=50;md5=zzzz \
                    ..."
```

::: note
::: title
Note
:::

- When using \"beginline\" and \"endline\", realize that line numbering begins with one and not zero. Also, the included lines are inclusive (i.e. lines five through and including 29 in the previous example for `licfile1.txt`).

> 当使用“开始行”和“结束行”时，要记住行号从 1 开始，而不是从 0 开始。此外，包含的行是包含的(即上一个示例中的 licfile1.txt 中的第 5 行到第 29 行)。

- When a license check fails, the selected license text is included as part of the QA message. Using this output, you can determine the exact start and finish for the needed license text.

> 当许可证检查失败时，所选择的许可证文本将包含在 QA 消息中。使用此输出，您可以确定所需许可证文本的准确起始和结束位置。
> :::

The build system uses the `S`. The previous example employs the default directory.

> 系统在搜索 `LIC_FILES_CHKSUM` 变量。前面的例子使用了默认目录。

Consider this next example:

```
LIC_FILES_CHKSUM = "file://src/ls.c;beginline=5;endline=16;\
                                    md5=bb14ed3c4cda583abc85401304b5cd4e"
LIC_FILES_CHKSUM = "file://$/license.html;md5=5c94767cedb5d6987c902ac850ded2c6"
```

The first line locates a file in `$.

> 第一行将文件定位在 `$ 中的文件。

Note that `LIC_FILES_CHKSUM` variable is set to \"CLOSED\".

## Explanation of Syntax

As mentioned in the previous section, the `LIC_FILES_CHKSUM` variable lists all the important files that contain the license text for the source code. It is possible to specify a checksum for an entire file, or a specific section of a file (specified by beginning and ending line numbers with the \"beginline\" and \"endline\" parameters, respectively). The latter is useful for source files with a license notice header, README documents, and so forth. If you do not use the \"beginline\" parameter, then it is assumed that the text begins on the first line of the file. Similarly, if you do not use the \"endline\" parameter, it is assumed that the license text ends with the last line of the file.

> 在上一节中提到，`LIC_FILES_CHKSUM` 变量列出了所有包含源代码许可文本的重要文件。可以为整个文件指定校验和，或者指定特定文件部分(通过使用“beginline”和“endline”参数分别指定起始和结束行号)的校验和。后者对于具有许可声明标头、README 文档等的源文件非常有用。如果不使用“beginline”参数，则假定文本从文件的第一行开始。类似地，如果不使用“endline”参数，则假定许可文本以文件的最后一行结束。

The \"md5\" parameter stores the md5 checksum of the license text. If the license text changes in any way as compared to this parameter then a mismatch occurs. This mismatch triggers a build failure and notifies the developer. Notification allows the developer to review and address the license text changes. Also note that if a mismatch occurs during the build, the correct md5 checksum is placed in the build log and can be easily copied to the recipe.

> 参数"md5"存储许可文本的 MD5 校验和。如果与此参数相比，许可文本以任何方式发生变化，则会发生不匹配。此不匹配会触发构建失败，并通知开发人员。通知允许开发人员查看和处理许可文本的更改。另请注意，如果在构建过程中发生不匹配，则正确的 MD5 校验和将放置在构建日志中，可以轻松复制到配方中。

There is no limit to how many files you can specify using the `LIC_FILES_CHKSUM` variable. Generally, however, every project requires a few specifications for license tracking. Many projects have a \"COPYING\" file that stores the license information for all the source code files. This practice allows you to just track the \"COPYING\" file as long as it is kept up to date.

> 没有限制，您可以使用 `LIC_FILES_CHKSUM` 变量指定多少文件。但是，一般来说，每个项目都需要几个许可证跟踪规范。许多项目都有一个“COPYING”文件，用于存储所有源代码文件的许可证信息。只要保持“COPYING”文件的更新，这种做法就可以让您只跟踪“COPYING”文件。

::: note
::: title
Note
:::

- If you specify an empty or invalid \"md5\" parameter, `BitBake` returns an md5 mis-match error and displays the correct \"md5\" parameter value during the build. The correct parameter is also captured in the build log.

> 如果指定了一个空的或无效的“md5”参数，BitBake 会返回一个 md5 不匹配的错误，并在构建期间显示正确的“md5”参数值。正确的参数也会被记录在构建日志中。

- If the whole file contains only license text, you do not need to use the \"beginline\" and \"endline\" parameters.
  :::

# Enabling Commercially Licensed Recipes

By default, the OpenEmbedded build system disables components that have commercial or other special licensing requirements. Such requirements are defined on a recipe-by-recipe basis through the `LICENSE_FLAGS` variable definition in the affected recipe. For instance, the `poky/meta/recipes-multimedia/gstreamer/gst-plugins-ugly` recipe contains the following statement:

> 默认情况下，OpenEmbedded 构建系统禁用具有商业或其他特殊许可要求的组件。这些要求是通过受影响配方中的 `LICENSE_FLAGS` 变量定义在配方每个配方上定义的。例如，`poky/meta/recipes-multimedia/gstreamer/gst-plugins-ugly` 配方包含以下声明：

```
LICENSE_FLAGS = "commercial"
```

Here is a slightly more complicated example that contains both an explicit recipe name and version (after variable expansion):

```
LICENSE_FLAGS = "license_$"
```

In order for a component restricted by a `LICENSE_FLAGS` matching works. Here is the example:

> 要使一个受 `LICENSE_FLAGS` 定义限制的组件被包含在一个镜像中并启用，它需要在全局 `LICENSE_FLAGS_ACCEPTED` 变量中有一个匹配的条目，这是一个通常在您的 `local.conf` 文件中定义的变量。例如，要启用 `poky/meta/recipes-multimedia/gstreamer/gst-plugins-ugly` 软件包，您可以添加字符串“commercial_gst-plugins-ugly”或更通用的字符串“commercial”到 `LICENSE_FLAGS_ACCEPTED`。有关 `LICENSE_FLAGS` 匹配如何工作的完整解释，请参见“`dev-manual/licenses:license flag matching`”部分。这里有一个例子：

```
LICENSE_FLAGS_ACCEPTED = "commercial_gst-plugins-ugly"
```

Likewise, to additionally enable the package built from the recipe containing `LICENSE_FLAGS = "license_$"`, and assuming that the actual recipe name was `emgd_1.10.bb`, the following string would enable that package as well as the original `gst-plugins-ugly` package:

> 同样的，为了使用包含 `LICENSE_FLAGS = "license_$"` 的配方所构建的包，假设实际的配方名称是 `emgd_1.10.bb`，以下字符串将启用该软件包以及原始的 `gst-plugins-ugly` 软件包：

```
LICENSE_FLAGS_ACCEPTED = "commercial_gst-plugins-ugly license_emgd_1.10"
```

As a convenience, you do not need to specify the complete license string for every package. You can use an abbreviated form, which consists of just the first portion or portions of the license string before the initial underscore character or characters. A partial string will match any license that contains the given string as the first portion of its license. For example, the following value will also match both of the packages previously mentioned as well as any other packages that have licenses starting with \"commercial\" or \"license\":

> 为了方便起见，您不需要为每个软件包指定完整的许可证字符串。您可以使用缩写形式，其中仅包括许可证字符串在下划线字符或字符之前的第一部分或多个部分。部分字符串将与其许可证字符串的第一部分为给定字符串的任何许可证匹配。例如，以下值也将匹配先前提到的两个软件包以及其他任何许可证字符串以“商业”或“许可证”开头的软件包：

```
LICENSE_FLAGS_ACCEPTED = "commercial license"
```

## License Flag Matching

License flag matching allows you to control what recipes the OpenEmbedded build system includes in the build. Fundamentally, the build system attempts to match `LICENSE_FLAGS`. A match causes the build system to include a recipe in the build, while failure to find a match causes the build system to exclude a recipe.

> 许可标志匹配允许您控制 OpenEmbedded 构建系统在构建中包含哪些配方。从本质上讲，构建系统会尝试将配方中找到的 `LICENSE_FLAGS` 字符串进行匹配。如果匹配成功，构建系统会将配方包含在构建中，如果未能找到匹配项，构建系统会排除该配方。

In general, license flag matching is simple. However, understanding some concepts will help you correctly and effectively use matching.

Before a flag defined by a particular recipe is tested against the entries of `LICENSE_FLAGS_ACCEPTED`.

> 在用特定配方定义的标志被测试到 `LICENSE_FLAGS_ACCEPTED` 的条目中。

Judicious use of the `LICENSE_FLAGS`.

> 恰当地使用 `LICENSE_FLAGS` 中使用许可证标志字符串子集来扩大匹配能力。

::: note
::: title
Note
:::

When using a string subset, be sure to use the part of the expanded string that precedes the appended underscore character (e.g. `usethispart_1.3`, `usethispart_1.4`, and so forth).

> 在使用字符串子集时，一定要使用扩展字符串中在追加下划线字符之前的部分(例如 `usethispart_1.3`、`usethispart_1.4` 等)。
> :::

For example, simply specifying the string \"commercial\" in the `LICENSE_FLAGS_ACCEPTED` definition that starts with the string \"commercial\" such as \"commercial_foo\" and \"commercial_bar\", which are the strings the build system automatically generates for hypothetical recipes named \"foo\" and \"bar\" assuming those recipes simply specify the following:

> 例如，在 `LICENSE_FLAGS_ACCEPTED` 定义，比如“commercial_foo”和“commercial_bar”，这些字符串是假设这些 recipes 只指定以下内容时，构建系统自动生成的：

```
LICENSE_FLAGS = "commercial"
```

Thus, you can choose to exhaustively enumerate each license flag in the list and allow only specific recipes into the image, or you can use a string subset that causes a broader range of matches to allow a range of recipes into the image.

> 因此，您可以选择详尽地列出列表中的每个许可证标志，并仅允许特定配方进入镜像，或者您可以使用引起更广泛匹配的字符子集，以允许一系列配方进入镜像。

This scheme works even if the `LICENSE_FLAGS` variable, as expected.

> 这个方案即使 `LICENSE_FLAGS` 变量中的一般“commercial”和特定的“commercial_1.2_foo”字符串，如预期的那样。

Here are some other scenarios:

- You can specify a versioned string in the recipe such as \"commercial_foo_1.2\" in a \"foo\" recipe. The build system expands this string to \"commercial_foo_1.2_foo\". Combine this license flag with a `LICENSE_FLAGS_ACCEPTED` variable that has the string \"commercial\" and you match the flag along with any other flag that starts with the string \"commercial\".

> 你可以在 recipes 中指定一个版本字符串，比如在“foo”recipes 中指定“commercial_foo_1.2”。构建系统会将此字符串扩展为“commercial_foo_1.2_foo”。将此许可标志与具有字符串“commercial”的 `LICENSE_FLAGS_ACCEPTED` 变量结合起来，即可与任何以“commercial”开头的标志匹配。

- Under the same circumstances, you can add \"commercial_foo\" in the `LICENSE_FLAGS_ACCEPTED` variable and the build system not only matches \"commercial_foo_1.2\" but also matches any license flag with the string \"commercial_foo\", regardless of the version.

> 在相同的情况下，您可以在 `LICENSE_FLAGS_ACCEPTED` 变量中添加“commercial_foo”，构建系统不仅匹配“commercial_foo_1.2”，而且匹配任何具有字符串“commercial_foo”的许可标志，而不管版本如何。

- You can be very specific and use both the package and version parts in the `LICENSE_FLAGS_ACCEPTED` list (e.g. \"commercial_foo_1.2\") to specifically match a versioned recipe.

> 你可以非常具体地使用 `LICENSE_FLAGS_ACCEPTED` 列表中的包和版本部分(例如“commercial_foo_1.2”)来特定匹配一个版本化的 recipes。

## Other Variables Related to Commercial Licenses

There are other helpful variables related to commercial license handling, defined in the `poky/meta/conf/distro/include/default-distrovars.inc` file:

> 在 `poky/meta/conf/distro/include/default-distrovars.inc` 文件中定义了其他有关商业许可处理的有用变量：

```
COMMERCIAL_AUDIO_PLUGINS ?= ""
COMMERCIAL_VIDEO_PLUGINS ?= ""
```

If you want to enable these components, you can do so by making sure you have statements similar to the following in your `local.conf` configuration file:

> 如果您想启用这些组件，可以通过确保在您的 `local.conf` 配置文件中具有类似以下语句来实现：

```
COMMERCIAL_AUDIO_PLUGINS = "gst-plugins-ugly-mad \
    gst-plugins-ugly-mpegaudioparse"
COMMERCIAL_VIDEO_PLUGINS = "gst-plugins-ugly-mpeg2dec \
    gst-plugins-ugly-mpegstream gst-plugins-bad-mpegvideoparse"
LICENSE_FLAGS_ACCEPTED = "commercial_gst-plugins-ugly commercial_gst-plugins-bad commercial_qmmp"
```

Of course, you could also create a matching list for those components using the more general \"commercial\" string in the `LICENSE_FLAGS_ACCEPTED` containing \"commercial\", which you may or may not want:

> 当然，您也可以使用更常见的“商业”字符串在 `LICENSE_FLAGS_ACCEPTED`，您可能会或可能不会想要：

```
LICENSE_FLAGS_ACCEPTED = "commercial"
```

Specifying audio and video plugins as part of the `COMMERCIAL_AUDIO_PLUGINS`) includes the plugins or components into built images, thus adding support for media formats or components.

> 指定音频和视频插件作为 `COMMERCIAL_AUDIO_PLUGINS`)的一部分，将插件或组件包含到构建的镜像中，从而添加对媒体格式或组件的支持。

::: note
::: title
Note
:::

GStreamer \"ugly\" and \"bad\" plugins are actually available through open source licenses. However, the \"ugly\" ones can be subject to software patents in some countries, making it necessary to pay licensing fees to distribute them. The \"bad\" ones are just deemed unreliable by the GStreamer community and should therefore be used with care.

> GStreamer 的“丑陋”和“坏”插件实际上可以通过开源许可证获得。但是，在某些国家/地区，“丑陋”插件可能受到软件专利的影响，因此需要支付许可费才能分发它们。“坏”插件只是被 GStreamer 社区认为不可靠，因此应该谨慎使用。
> :::

# Maintaining Open Source License Compliance During Your Product\'s Lifecycle

One of the concerns for a development organization using open source software is how to maintain compliance with various open source licensing during the lifecycle of the product. While this section does not provide legal advice or comprehensively cover all scenarios, it does present methods that you can use to assist you in meeting the compliance requirements during a software release.

> 一个使用开源软件的发展组织的关注点是如何在产品的生命周期中保持各种开源许可的遵守。虽然本节不提供法律建议或全面涵盖所有情况，但它确实提供了您可以用来帮助您在软件发布期间满足合规要求的方法。

With hundreds of different open source licenses that the Yocto Project tracks, it is difficult to know the requirements of each and every license. However, the requirements of the major FLOSS licenses can begin to be covered by assuming that there are three main areas of concern:

> 随着 Yocto 项目跟踪的数百种不同的开源许可证，要了解每一种许可证的要求是很困难的。但是，可以通过假设有三个主要关注领域来开始涵盖主要的 FLOSS 许可证的要求：

- Source code must be provided.
- License text for the software must be provided.
- Compilation scripts and modifications to the source code must be provided.

There are other requirements beyond the scope of these three and the methods described in this section (e.g. the mechanism through which source code is distributed).

> 除了这三个以及本节中描述的方法之外，还有其他要求(例如源代码分发的机制)。

As different organizations have different methods of complying with open source licensing, this section is not meant to imply that there is only one single way to meet your compliance obligations, but rather to describe one method of achieving compliance. The remainder of this section describes methods supported to meet the previously mentioned three requirements. Once you take steps to meet these requirements, and prior to releasing images, sources, and the build system, you should audit all artifacts to ensure completeness.

> 随着不同组织遵守开源许可协议的方式不同，本节旨在描述一种实现合规的方法，而不是暗示只有一种方法可以满足您的合规义务。本节的其余部分描述了支持满足前述三个要求的方法。在释放镜像、源代码和构建系统之前，您应该审核所有工件以确保完整性。

::: note
::: title
Note
:::

The Yocto Project generates a license manifest during image creation that is located in `$ to assist with any audits.

> 面向审计提供帮助，Yocto 项目在镜像创建时会生成一个许可证清单，位于 `$。
> :::

## Providing the Source Code

Compliance activities should begin before you generate the final image. The first thing you should look at is the requirement that tops the list for most compliance groups \-\-- providing the source. The Yocto Project has a few ways of meeting this requirement.

> 您在生成最终镜像之前应该开始执行合规活动。首先要考虑的是大多数合规组织最重要的要求——提供源代码。Yocto Project 有几种满足这一要求的方法。

One of the easiest ways to meet this requirement is to provide the entire `DL_DIR` class to help avoid some of these concerns.

> 一个最简单的满足这个要求的方法是提供整个由构建使用的 `DL_DIR`。然而，这种方法有一些问题。最明显的是目录的大小，因为它包括构建中使用的所有源，而不仅仅是发布镜像中使用的源。它将包括工具链源和其他工件，这些工件通常不会发布。然而，对于大多数公司而言，更严重的问题是意外发布专有软件。Yocto 项目提供了一个 `ref-classes-archiver` 类来帮助避免一些这些问题。

Before you employ `DL_DIR` class can generate tarballs and SRPMs and can create them with various levels of compliance in mind.

> 在使用 `DL_DIR` 类可以生成 tarballs 和 SRPMs，并且可以根据不同级别的合规性创建它们。

One way of doing this (but certainly not the only way) is to release just the source as a tarball. You can do this by adding the following to the `local.conf` file found in the `Build Directory`:

> 一种做法(但不是唯一的方法)是将源代码以 tarball 的形式发布。您可以在 `Build Directory` 中的 `local.conf` 文件中添加以下内容来实现：

```
INHERIT += "archiver"
ARCHIVER_MODE[src] = "original"
```

During the creation of your image, the source from all recipes that deploy packages to the image is placed within subdirectories of `DEPLOY_DIR/sources` based on the `LICENSE` for each recipe. Releasing the entire directory enables you to comply with requirements concerning providing the unmodified source. It is important to note that the size of the directory can get large.

> 在创建镜像的过程中，根据每个配方的许可证，将部署到镜像的所有配方的源代码放置在 `DEPLOY_DIR/sources` 的子目录中。发布整个目录可以帮助您遵守提供未修改源代码的要求。重要的是要注意，该目录的大小可能会变得很大。

A way to help mitigate the size issue is to only release tarballs for licenses that require the release of source. Let us assume you are only concerned with GPL code as identified by running the following script:

> 解决大小问题的一种方法是只发布那些需要发布源代码的许可证的压缩文件。假设您只关心 GPL 代码，可以运行以下脚本来识别：

```shell
# Script to archive a subset of packages matching specific license(s)
# Source and license files are copied into sub folders of package folder
# Must be run from build folder
#!/bin/bash
src_release_dir="source-release"
mkdir -p $src_release_dir
for a in tmp/deploy/sources/*; do
   for d in $a/*; do
      # Get package name from path
      p=`basename $d`
      p=$
      p=$
      # Only archive GPL packages (update *GPL* regex for your license check)
      numfiles=`ls tmp/deploy/licenses/$p/*GPL* 2> /dev/null | wc -l`
      if [ $numfiles -ge 1 ]; then
         echo Archiving $p
         mkdir -p $src_release_dir/$p/source
         cp $d/* $src_release_dir/$p/source 2> /dev/null
         mkdir -p $src_release_dir/$p/license
         cp tmp/deploy/licenses/$p/* $src_release_dir/$p/license 2> /dev/null
      fi
   done
done
```

At this point, you could create a tarball from the `gpl_source_release` directory and provide that to the end user. This method would be a step toward achieving compliance with section 3a of GPLv2 and with section 6 of GPLv3.

> 在这一点上，您可以从 `gpl_source_release` 目录中创建一个 tarball，并将其提供给最终用户。这种方法有助于实现 GPLv2 第 3a 条和 GPLv3 第 6 条的合规性。

## Providing License Text

One requirement that is often overlooked is inclusion of license text. This requirement also needs to be dealt with prior to generating the final image. Some licenses require the license text to accompany the binary. You can achieve this by adding the following to your `local.conf` file:

> 经常被忽略的一个要求是包括许可文本。在生成最终映像之前，这个要求也需要处理。有些许可证要求许可文本附带二进制文件。您可以通过将以下内容添加到您的 `local.conf` 文件来实现：

```
COPY_LIC_MANIFEST = "1"
COPY_LIC_DIRS = "1"
LICENSE_CREATE_PACKAGE = "1"
```

Adding these statements to the configuration file ensures that the licenses collected during package generation are included on your image.

::: note
::: title
Note
:::

Setting all three variables to \"1\" results in the image having two copies of the same license file. One copy resides in `/usr/share/common-licenses` and the other resides in `/usr/share/license`.

> 将三个变量设置为“1”的结果是，镜像具有两个相同的许可证文件副本。一个副本位于/usr/share/common-licenses，另一个副本位于/usr/share/license。

The reason for this behavior is because `COPY_LIC_DIRS` adds a separate package and an upgrade path for adding licenses to an image.

> 这种行为的原因是因为 `COPY_LIC_DIRS` 和 `COPY_LIC_MANIFEST` 在构建镜像时会添加一份许可证的副本，但不提供添加新安装软件包许可证的路径。`LICENSE_CREATE_PACKAGE` 添加一个单独的软件包和一个升级路径，用于向镜像添加许可证。
> :::

As the source `ref-classes-archiver` class has already archived the original unmodified source that contains the license files, you would have already met the requirements for inclusion of the license information with source as defined by the GPL and other open source licenses.

> 由于源代码 ref-classes-archiver 类已经存档了原始未修改的源代码，其中包含许可证文件，因此您已经满足了 GPL 和其他开源许可证规定的源代码中包含许可信息的要求。

## Providing Compilation Scripts and Source Code Modifications

At this point, we have addressed all we need to prior to generating the image. The next two requirements are addressed during the final packaging of the release.

> 到目前为止，我们已经处理了生成镜像所需的一切。接下来的两个要求是在最终发布时处理的。

By releasing the version of the OpenEmbedded build system and the layers used during the build, you will be providing both compilation scripts and the source code modifications in one step.

> 通过发布 OpenEmbedded 构建系统的版本以及构建过程中使用的层，您将在一步中提供编译脚本和源代码修改。

If the deployment team has a `overview-manual/concepts:bsp layer` and a distro layer, and those those layers are used to patch, compile, package, or modify (in any way) any open source software included in your released images, you might be required to release those layers under section 3 of GPLv2 or section 1 of GPLv3. One way of doing that is with a clean checkout of the version of the Yocto Project and layers used during your build. Here is an example:

> 如果部署团队有一个 `overview-manual/concepts:bsp layer` 层和一个发行版层，并且这些层被用来修补、编译、打包或修改(以任何方式)您发布的镜像中包含的任何开源软件，您可能需要根据 GPLv2 第 3 节或 GPLv3 第 1 节发布这些层。一种做法是使用您构建时使用的 Yocto 项目和层的干净检出版本。这里有一个例子：

```shell
# We built using the dunfell branch of the poky repo
$ git clone -b dunfell git://git.yoctoproject.org/poky
$ cd poky
# We built using the release_branch for our layers
$ git clone -b release_branch git://git.mycompany.com/meta-my-bsp-layer
$ git clone -b release_branch git://git.mycompany.com/meta-my-software-layer
# clean up the .git repos
$ find . -name ".git" -type d -exec rm -rf  \;
```

One thing a development organization might want to consider for end-user convenience is to modify `meta-poky/conf/templates/default/bblayers.conf.sample` to ensure that when the end user utilizes the released build system to build an image, the development organization\'s layers are included in the `bblayers.conf` file automatically:

> 一个开发组织可能想要考虑用户便利性的一件事是修改 `meta-poky/conf/templates/default/bblayers.conf.sample`，以确保当最终用户使用发布的构建系统构建镜像时，开发组织的层会自动包含在 `bblayers.conf` 文件中。

```
# POKY_BBLAYERS_CONF_VERSION is increased each time build/conf/bblayers.conf
# changes incompatibly
POKY_BBLAYERS_CONF_VERSION = "2"

BBPATH = "$"
BBFILES ?= ""

BBLAYERS ?= " \
  ##OEROOT##/meta \
  ##OEROOT##/meta-poky \
  ##OEROOT##/meta-yocto-bsp \
  ##OEROOT##/meta-mylayer \
  "
```

Creating and providing an archive of the `Metadata` layers (recipes, configuration files, and so forth) enables you to meet your requirements to include the scripts to control compilation as well as any modifications to the original source.

> 创建和提供元数据层的存档(recipes、配置文件等)可以帮助您满足要求，包括用于控制编译的脚本以及对原始源代码的任何修改。

## Compliance Limitations with Executables Built from Static Libraries

When package A is added to an image via the `RDEPENDS` end up in the image.

> 当通过 `RDEPENDS` 或 `RRECOMMENDS` 机制将包 A 添加到镜像中，以及使用 `IMAGE_INSTALL` 明确地将其包含在镜像配方中，并且依赖于静态链接库配方 B(`DEPENDS += "B"`)时，包 B 将不会出现在生成的许可证清单中，也不会出现在生成的源 tarballs 中。这是因为 `ref-classes-license` 和 `ref-classes-archiver` 类假定只有通过 `RDEPENDS` 或 `RRECOMMENDS` 添加到镜像中的包才会出现。

As a result, potential obligations regarding license compliance for package B may not be met.

The Yocto Project doesn\'t enable static libraries by default, in part because of this issue. Before a solution to this limitation is found, you need to keep in mind that if your root filesystem is built from static libraries, you will need to manually ensure that your deliveries are compliant with the licenses of these libraries.

> 默认情况下，Yocto Project 不支持静态库，部分原因是由于这个问题。在找到解决这个限制的解决方案之前，你需要记住，如果你的根文件系统是由静态库构建的，你需要手动确保你的交付物符合这些库的许可证。

# Copying Non Standard Licenses

Some packages, such as the linux-firmware package, have many licenses that are not in any way common. You can avoid adding a lot of these types of common license files, which are only applicable to a specific package, by using the `NO_GENERIC_LICENSE` variable. Using this variable also avoids QA errors when you use a non-common, non-CLOSED license in a recipe.

> 一些软件包，例如 linux-firmware 软件包，有许多不常见的许可证。您可以通过使用 `NO_GENERIC_LICENSE` 变量来避免添加大量这些仅适用于特定软件包的常见许可证文件。使用此变量还可以避免在使用非常见、非 CLOSED 许可证时出现 QA 错误。

Here is an example that uses the `LICENSE.Abilis.txt` file as the license from the fetched source:

```
NO_GENERIC_LICENSE[Firmware-Abilis] = "LICENSE.Abilis.txt"
```
