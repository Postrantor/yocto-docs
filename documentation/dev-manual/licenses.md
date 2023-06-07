---
tip: translate by baidu@2023-06-07 17:13:12
...
---
title: Working With Licenses
----------------------------

As mentioned in the \"`overview-manual/development-environment:licensing`{.interpreted-text role="ref"}\" section in the Yocto Project Overview and Concepts Manual, open source projects are open to the public and they consequently have different licensing structures in place. This section describes the mechanism by which the `OpenEmbedded Build System`{.interpreted-text role="term"} tracks changes to licensing text and covers how to maintain open source license compliance during your project\'s lifecycle. The section also describes how to enable commercially licensed recipes, which by default are disabled.

> 正如 Yocto 项目概述和概念手册中的“概述手册/开发环境：许可”一节所述，开源项目是向公众开放的，因此它们有不同的许可结构。本节介绍了 `OpenEmbedded Build System`｛.depredicted text role=“term”｝跟踪许可文本更改的机制，并介绍了如何在项目生命周期内保持开源许可的合规性。本节还介绍了如何启用商业许可的配方，这些配方在默认情况下是禁用的。

# Tracking License Changes

The license of an upstream project might change in the future. In order to prevent these changes going unnoticed, the `LIC_FILES_CHKSUM`{.interpreted-text role="term"} variable tracks changes to the license text. The checksums are validated at the end of the configure step, and if the checksums do not match, the build will fail.

> 上游项目的许可证将来可能会更改。为了防止这些更改被忽视，`LIC_FILES_CHKSUM`｛.depreted text role=“term”｝变量跟踪对许可证文本的更改。校验和将在配置步骤结束时进行验证，如果校验和不匹配，则构建将失败。

## Specifying the `LIC_FILES_CHKSUM` Variable

The `LIC_FILES_CHKSUM`{.interpreted-text role="term"} variable contains checksums of the license text in the source code for the recipe. Following is an example of how to specify `LIC_FILES_CHKSUM`{.interpreted-text role="term"}:

> `LIC_FILES_CHKSUM`｛.explored text role=“term”｝变量包含配方源代码中许可证文本的校验和。以下是如何指定 `LIC_FILES_CHKSUM`｛.explored text role=“term”｝的示例：

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

> -当使用“beginline\”和“endline\”时，请注意行号以 1 开头，而不是以零开头。此外，所包括的行是包含的（即，在前面的“licfile1.txt”示例中的第 5 行到第 29 行，包括第 29 行）。

- When a license check fails, the selected license text is included as part of the QA message. Using this output, you can determine the exact start and finish for the needed license text.

> -当许可证检查失败时，所选的许可证文本将作为 QA 消息的一部分包含在内。使用此输出，您可以确定所需许可证文本的确切开始和结束。
> :::

The build system uses the `S`{.interpreted-text role="term"} variable as the default directory when searching files listed in `LIC_FILES_CHKSUM`{.interpreted-text role="term"}. The previous example employs the default directory.

> 在搜索 `LIC_files_CHKSUM` 中列出的文件时，生成系统使用 `S`｛.expected text role=“term”｝变量作为默认目录。前面的示例使用了默认目录。

Consider this next example:

> 考虑下一个例子：

```
LIC_FILES_CHKSUM = "file://src/ls.c;beginline=5;endline=16;\
                                    md5=bb14ed3c4cda583abc85401304b5cd4e"
LIC_FILES_CHKSUM = "file://${WORKDIR}/license.html;md5=5c94767cedb5d6987c902ac850ded2c6"
```

The first line locates a file in `${S}/src/ls.c` and isolates lines five through 16 as license text. The second line refers to a file in `WORKDIR`{.interpreted-text role="term"}.

> 第一行在“${S}/src/ls.c”中定位一个文件，并将第 5 行到第 16 行隔离为许可证文本。第二行指的是 `WORKDIR`｛.explored text role=“term”｝中的一个文件。

Note that `LIC_FILES_CHKSUM`{.interpreted-text role="term"} variable is mandatory for all recipes, unless the `LICENSE`{.interpreted-text role="term"} variable is set to \"CLOSED\".

## Explanation of Syntax

As mentioned in the previous section, the `LIC_FILES_CHKSUM`{.interpreted-text role="term"} variable lists all the important files that contain the license text for the source code. It is possible to specify a checksum for an entire file, or a specific section of a file (specified by beginning and ending line numbers with the \"beginline\" and \"endline\" parameters, respectively). The latter is useful for source files with a license notice header, README documents, and so forth. If you do not use the \"beginline\" parameter, then it is assumed that the text begins on the first line of the file. Similarly, if you do not use the \"endline\" parameter, it is assumed that the license text ends with the last line of the file.

> 如前一节所述，`LIC_FILES_CHKSUM`｛.depreted text role=“term”｝变量列出了包含源代码许可证文本的所有重要文件。可以为整个文件或文件的特定部分指定校验和（分别用\“beginline\”和\“endline\”参数的开头和结尾行号指定）。后者对于具有许可证通知头、README 文档等的源文件非常有用。如果不使用“beginline\”参数，则假定文本从文件的第一行开始。同样，如果不使用\“endline\”参数，则假定许可证文本以文件的最后一行结束。

The \"md5\" parameter stores the md5 checksum of the license text. If the license text changes in any way as compared to this parameter then a mismatch occurs. This mismatch triggers a build failure and notifies the developer. Notification allows the developer to review and address the license text changes. Also note that if a mismatch occurs during the build, the correct md5 checksum is placed in the build log and can be easily copied to the recipe.

> “md5\”参数存储许可证文本的 md5 校验和。如果许可证文本与此参数相比有任何变化，则会出现不匹配。这种不匹配会触发生成失败并通知开发人员。通知允许开发人员查看和处理许可证文本更改。还要注意的是，如果在构建过程中发生不匹配，正确的 md5 校验和将被放置在构建日志中，并且可以很容易地复制到配方中。

There is no limit to how many files you can specify using the `LIC_FILES_CHKSUM`{.interpreted-text role="term"} variable. Generally, however, every project requires a few specifications for license tracking. Many projects have a \"COPYING\" file that stores the license information for all the source code files. This practice allows you to just track the \"COPYING\" file as long as it is kept up to date.

> 使用 `LIC_files_CHKSUM`｛.depredicted text role=“term”｝变量可以指定的文件数量没有限制。然而，一般来说，每个项目都需要一些许可证跟踪规范。许多项目都有一个“COPYING”文件，用于存储所有源代码文件的许可证信息。这种做法允许您只跟踪“复制”文件，只要它是最新的。

::: note
::: title
Note
:::

- If you specify an empty or invalid \"md5\" parameter, `BitBake`{.interpreted-text role="term"} returns an md5 mis-match error and displays the correct \"md5\" parameter value during the build. The correct parameter is also captured in the build log.

> -如果指定了一个空的或无效的“md5”参数，则 `BitBake`｛.respered text role=“term”｝将返回一个 md5 不匹配错误，并在生成过程中显示正确的“md5\”参数值。生成日志中还捕获了正确的参数。

- If the whole file contains only license text, you do not need to use the \"beginline\" and \"endline\" parameters.

> -如果整个文件仅包含许可证文本，则不需要使用\“beginline\”和\“endline\”参数。
> :::

# Enabling Commercially Licensed Recipes

By default, the OpenEmbedded build system disables components that have commercial or other special licensing requirements. Such requirements are defined on a recipe-by-recipe basis through the `LICENSE_FLAGS`{.interpreted-text role="term"} variable definition in the affected recipe. For instance, the `poky/meta/recipes-multimedia/gstreamer/gst-plugins-ugly` recipe contains the following statement:

> 默认情况下，OpenEmbedded 构建系统会禁用具有商业或其他特殊许可要求的组件。这些要求是通过受影响配方中的 `LICENSE_FLAGS`｛.explored text role=“term”｝变量定义在配方基础上定义的。例如，“poky/meta/precipes multimedia/gstreamer/gst-plugins-ugly”配方包含以下语句：

```
LICENSE_FLAGS = "commercial"
```

Here is a slightly more complicated example that contains both an explicit recipe name and version (after variable expansion):

> 下面是一个稍微复杂一点的例子，它包含明确的配方名称和版本（在变量展开后）：

```
LICENSE_FLAGS = "license_${PN}_${PV}"
```

In order for a component restricted by a `LICENSE_FLAGS`{.interpreted-text role="term"} definition to be enabled and included in an image, it needs to have a matching entry in the global `LICENSE_FLAGS_ACCEPTED`{.interpreted-text role="term"} variable, which is a variable typically defined in your `local.conf` file. For example, to enable the `poky/meta/recipes-multimedia/gstreamer/gst-plugins-ugly` package, you could add either the string \"commercial_gst-plugins-ugly\" or the more general string \"commercial\" to `LICENSE_FLAGS_ACCEPTED`{.interpreted-text role="term"}. See the \"`dev-manual/licenses:license flag matching`{.interpreted-text role="ref"}\" section for a full explanation of how `LICENSE_FLAGS`{.interpreted-text role="term"} matching works. Here is the example:

> 为了启用受 `LICENSE_FLAGS`｛.depreted text role=“term”｝定义限制的组件并将其包含在图像中，它需要在全局 `LICENSE_FLAGS_ACCEPTED`｛.repreted text role=“term“｝变量中具有匹配条目，该变量通常在 `local.conf` 文件中定义。例如，要启用 `poky/meta/precipes multimedia/gsteamer/gst-plugins-ugly` 包，您可以将字符串\“commercial_gst-plugin-ugly\”或更通用的字符串\“commercial\”添加到 `LICENSE_FLAG_ACCEPTED`｛.depredicted text role=“term”｝。请参阅“`dev manual/licenses:license flag matching`｛.explored text role=“ref”｝”一节，以完整解释 `license_FLAGS`｛.expered text rol=“term”｝匹配的工作原理。以下是示例：

```
LICENSE_FLAGS_ACCEPTED = "commercial_gst-plugins-ugly"
```

Likewise, to additionally enable the package built from the recipe containing `LICENSE_FLAGS = "license_${PN}_${PV}"`, and assuming that the actual recipe name was `emgd_1.10.bb`, the following string would enable that package as well as the original `gst-plugins-ugly` package:

> 同样，为了额外启用根据包含“LICENSE_FLAGS=“LICENSE_${PN}_${PV}”的配方构建的包，并假设实际配方名称为“emgd_1.10.bb”，以下字符串将启用该包以及原始的“gst-plugins-丑陋”包：

```
LICENSE_FLAGS_ACCEPTED = "commercial_gst-plugins-ugly license_emgd_1.10"
```

As a convenience, you do not need to specify the complete license string for every package. You can use an abbreviated form, which consists of just the first portion or portions of the license string before the initial underscore character or characters. A partial string will match any license that contains the given string as the first portion of its license. For example, the following value will also match both of the packages previously mentioned as well as any other packages that have licenses starting with \"commercial\" or \"license\":

> 为了方便起见，您不需要为每个包指定完整的许可证字符串。您可以使用缩写形式，它仅由初始下划线字符之前的许可证字符串的第一部分或多个部分组成。部分字符串将匹配任何包含给定字符串作为其许可证第一部分的许可证。例如，以下值还将与前面提到的两个软件包以及许可证以“commercial\”或“license\”开头的任何其他软件包相匹配：

```
LICENSE_FLAGS_ACCEPTED = "commercial license"
```

## License Flag Matching

License flag matching allows you to control what recipes the OpenEmbedded build system includes in the build. Fundamentally, the build system attempts to match `LICENSE_FLAGS`{.interpreted-text role="term"} strings found in recipes against strings found in `LICENSE_FLAGS_ACCEPTED`{.interpreted-text role="term"}. A match causes the build system to include a recipe in the build, while failure to find a match causes the build system to exclude a recipe.

> 许可证标志匹配允许您控制 OpenEmbedded 构建系统在构建中包含的配方。从根本上讲，生成系统试图将配方中找到的 `LICENSE_FLAGS`｛.depreted text role=“term”｝字符串与 `LICENSE_FLAGS_ACCEPTED`｛.repreted text role=“term“｝中找到的字符串相匹配。匹配会导致生成系统在生成中包含配方，而找不到匹配会导致构建系统排除配方。

In general, license flag matching is simple. However, understanding some concepts will help you correctly and effectively use matching.

> 一般来说，许可证标志匹配很简单。然而，理解一些概念将有助于正确有效地使用匹配。

Before a flag defined by a particular recipe is tested against the entries of `LICENSE_FLAGS_ACCEPTED`{.interpreted-text role="term"}, the expanded string `_${PN}` is appended to the flag. This expansion makes each `LICENSE_FLAGS`{.interpreted-text role="term"} value recipe-specific. After expansion, the string is then matched against the entries. Thus, specifying `LICENSE_FLAGS = "commercial"` in recipe \"foo\", for example, results in the string `"commercial_foo"`. And, to create a match, that string must appear among the entries of `LICENSE_FLAGS_ACCEPTED`{.interpreted-text role="term"}.

> 在根据 `LICENSE_FLAGS_ACCEPTED`｛.explored text role=“term”｝的条目测试由特定配方定义的标志之前，将扩展字符串 `_$｛PN｝` 附加到标志上。此扩展使每个 `LICENSE_FLAGS`｛.explored text role=“term”｝值配方特定。展开后，字符串将与条目相匹配。因此，例如，在配方\“foo\”中指定 `LICENSE_FLAGS=“commercial”` 会产生字符串 `“commercial_foo”`。而且，要创建匹配，该字符串必须出现在 `LICENSE_FLAGS_ACCEPTED` 的条目中{.depreted text role=“term”}。

Judicious use of the `LICENSE_FLAGS`{.interpreted-text role="term"} strings and the contents of the `LICENSE_FLAGS_ACCEPTED`{.interpreted-text role="term"} variable allows you a lot of flexibility for including or excluding recipes based on licensing. For example, you can broaden the matching capabilities by using license flags string subsets in `LICENSE_FLAGS_ACCEPTED`{.interpreted-text role="term"}.

> 明智地使用 `LICENSE_FLAGS`｛.depredicted text role=“term”｝字符串和 `LICENSE_FLAGS_ACCEPTED`｛.epredicted textrole=”term“｝变量的内容，使您可以在基于许可的情况下灵活地包括或排除配方。例如，您可以通过使用 `license_flags_ACCEPTED`｛.depreted text role=“term”｝中的许可标志字符串子集来扩展匹配功能。

::: note
::: title
Note
:::

When using a string subset, be sure to use the part of the expanded string that precedes the appended underscore character (e.g. `usethispart_1.3`, `usethispart_1.4`, and so forth).

> 使用字符串子集时，请确保使用扩展字符串中位于附加下划线字符之前的部分（例如“usethispart_1.3”、“usethiapart_1.4”等）。
> :::

For example, simply specifying the string \"commercial\" in the `LICENSE_FLAGS_ACCEPTED`{.interpreted-text role="term"} variable matches any expanded `LICENSE_FLAGS`{.interpreted-text role="term"} definition that starts with the string \"commercial\" such as \"commercial_foo\" and \"commercial_bar\", which are the strings the build system automatically generates for hypothetical recipes named \"foo\" and \"bar\" assuming those recipes simply specify the following:

> 例如，只需在 `LICENSE_FLAGS_ACCEPTE`｛.depredicted text role=“term”｝变量中指定字符串\“commercial\”，就可以匹配任何以字符串\“commercial\”开头的扩展 `LICENSE_FLAG`｛.epredicted textrole=”term“｝定义，例如\“commercel_foo\”和\“commerical_bar\”，它们是构建系统为名为“foo”和“bar”的假设配方自动生成的字符串，假设这些配方只指定以下内容：

```
LICENSE_FLAGS = "commercial"
```

Thus, you can choose to exhaustively enumerate each license flag in the list and allow only specific recipes into the image, or you can use a string subset that causes a broader range of matches to allow a range of recipes into the image.

> 因此，您可以选择详尽地枚举列表中的每个许可证标志，并只允许特定的配方进入图像，也可以使用导致更广泛匹配的字符串子集，以允许一系列配方进入图像。

This scheme works even if the `LICENSE_FLAGS`{.interpreted-text role="term"} string already has `_${PN}` appended. For example, the build system turns the license flag \"commercial_1.2_foo\" into \"commercial_1.2_foo_foo\" and would match both the general \"commercial\" and the specific \"commercial_1.2_foo\" strings found in the `LICENSE_FLAGS_ACCEPTED`{.interpreted-text role="term"} variable, as expected.

> 即使 `LICENSE_FLAGS`｛.explored text role=“term”｝字符串已经附加了 `_$｛PN｝`，该方案也能工作。例如，生成系统将许可标志“commercial_1.2_foo\”转换为“commercial _1.2_foo_foo\”，并将与 `license_FLAGS_ACCEPTE`｛.explored text role=“term”｝变量中的常规“commercular \”和特定“commercual_1.2_foo \”字符串相匹配。

Here are some other scenarios:

> 以下是其他一些场景：

- You can specify a versioned string in the recipe such as \"commercial_foo_1.2\" in a \"foo\" recipe. The build system expands this string to \"commercial_foo_1.2_foo\". Combine this license flag with a `LICENSE_FLAGS_ACCEPTED`{.interpreted-text role="term"} variable that has the string \"commercial\" and you match the flag along with any other flag that starts with the string \"commercial\".

> -您可以在配方中指定一个版本化的字符串，例如“foo”配方中的“commercial_foo_1.2\”。生成系统将此字符串扩展为“commercial_foo_1.2_foo\”。将此许可证标志与带有字符串“commercial”的 `license_FLAGS_ACCEPTED`｛.depreted text role=“term”｝变量组合，即可将该标志与以字符串“commerceal”开头的任何其他标志进行匹配。

- Under the same circumstances, you can add \"commercial_foo\" in the `LICENSE_FLAGS_ACCEPTED`{.interpreted-text role="term"} variable and the build system not only matches \"commercial_foo_1.2\" but also matches any license flag with the string \"commercial_foo\", regardless of the version.

> -在同样的情况下，您可以在 `LICENSE_FLAGS_ACCEPTE`｛.depreted text role=“term”｝变量中添加\“commercial_foo\”，生成系统不仅匹配\“commercel_fo_1.2\”，而且还匹配任何带有字符串\“commeercial_foo\\”的许可标志，无论版本如何。

- You can be very specific and use both the package and version parts in the `LICENSE_FLAGS_ACCEPTED`{.interpreted-text role="term"} list (e.g. \"commercial_foo_1.2\") to specifically match a versioned recipe.

> -您可以非常具体，并使用 `LICENSE_FLAGS_ACCEPTED`｛.depreted text role=“term”｝列表中的包和版本部分（例如\“commercial_foo_1.2\”）来专门匹配版本化配方。

## Other Variables Related to Commercial Licenses

There are other helpful variables related to commercial license handling, defined in the `poky/meta/conf/distro/include/default-distrovars.inc` file:

> “poky/meta/conf/distro/include/default-distrovars.inc”文件中定义了其他与商业许可证处理相关的有用变量：

```
COMMERCIAL_AUDIO_PLUGINS ?= ""
COMMERCIAL_VIDEO_PLUGINS ?= ""
```

If you want to enable these components, you can do so by making sure you have statements similar to the following in your `local.conf` configuration file:

> 如果您想启用这些组件，您可以确保在“local.conf”配置文件中有类似于以下的语句：

```
COMMERCIAL_AUDIO_PLUGINS = "gst-plugins-ugly-mad \
    gst-plugins-ugly-mpegaudioparse"
COMMERCIAL_VIDEO_PLUGINS = "gst-plugins-ugly-mpeg2dec \
    gst-plugins-ugly-mpegstream gst-plugins-bad-mpegvideoparse"
LICENSE_FLAGS_ACCEPTED = "commercial_gst-plugins-ugly commercial_gst-plugins-bad commercial_qmmp"
```

Of course, you could also create a matching list for those components using the more general \"commercial\" string in the `LICENSE_FLAGS_ACCEPTED`{.interpreted-text role="term"} variable, but that would also enable all the other packages with `LICENSE_FLAGS`{.interpreted-text role="term"} containing \"commercial\", which you may or may not want:

> 当然，您也可以使用 `LICENSE_FLAGS_ACCEPTED`｛.depredicted text role=“term”｝变量中更通用的\“commercial\”字符串为这些组件创建一个匹配列表，但这也将启用所有其他带有 `LICENSE_FLAGS`｛.epredicted textrole=”term“｝的包，其中包含\“commeercial\”，您可能想要也可能不想要：

```
LICENSE_FLAGS_ACCEPTED = "commercial"
```

Specifying audio and video plugins as part of the `COMMERCIAL_AUDIO_PLUGINS`{.interpreted-text role="term"} and `COMMERCIAL_VIDEO_PLUGINS`{.interpreted-text role="term"} statements (along with `LICENSE_FLAGS_ACCEPTED`{.interpreted-text role="term"}) includes the plugins or components into built images, thus adding support for media formats or components.

> 将音频和视频插件指定为 `COMMERCIAL_audio_plugins`｛.depreted text role=“term”｝和 `COMMERCIAL_video_plugins`｛.epreted text role=“term“｝语句的一部分（以及 `LICENSE_FLAG_ACCEPTED`｛.repreted text 角色=“term”｝）将插件或组件包含在内置图像中，从而增加对媒体格式或组件的支持。

::: note
::: title
Note
:::

GStreamer \"ugly\" and \"bad\" plugins are actually available through open source licenses. However, the \"ugly\" ones can be subject to software patents in some countries, making it necessary to pay licensing fees to distribute them. The \"bad\" ones are just deemed unreliable by the GStreamer community and should therefore be used with care.

> GStreamer“丑陋”和“糟糕”插件实际上可以通过开源许可证获得。然而，在一些国家，“丑陋”的软件可能会获得软件专利，因此需要支付许可费才能分发。“坏的”那些只是被 GStreamer 社区认为是不可靠的，因此应该小心使用。
> :::

# Maintaining Open Source License Compliance During Your Product\'s Lifecycle

One of the concerns for a development organization using open source software is how to maintain compliance with various open source licensing during the lifecycle of the product. While this section does not provide legal advice or comprehensively cover all scenarios, it does present methods that you can use to assist you in meeting the compliance requirements during a software release.

> 使用开源软件的开发组织关注的问题之一是如何在产品的生命周期中保持对各种开源许可的遵守。虽然本节没有提供法律建议或全面涵盖所有情况，但它提供了一些方法，您可以使用这些方法来帮助您在软件发布期间满足法规遵从性要求。

With hundreds of different open source licenses that the Yocto Project tracks, it is difficult to know the requirements of each and every license. However, the requirements of the major FLOSS licenses can begin to be covered by assuming that there are three main areas of concern:

> Yocto 项目跟踪了数百个不同的开源许可证，很难知道每个许可证的要求。然而，主要 FLOSS 许可证的要求可以通过假设有三个主要关注领域来开始涵盖：

- Source code must be provided.
- License text for the software must be provided.
- Compilation scripts and modifications to the source code must be provided.

There are other requirements beyond the scope of these three and the methods described in this section (e.g. the mechanism through which source code is distributed).

> 除了这三个要求和本节中描述的方法之外，还有其他要求（例如，分发源代码的机制）。

As different organizations have different methods of complying with open source licensing, this section is not meant to imply that there is only one single way to meet your compliance obligations, but rather to describe one method of achieving compliance. The remainder of this section describes methods supported to meet the previously mentioned three requirements. Once you take steps to meet these requirements, and prior to releasing images, sources, and the build system, you should audit all artifacts to ensure completeness.

> 由于不同的组织有不同的方法来遵守开源许可，本节并不意味着只有一种方法可以满足您的法规遵从性义务，而是描述实现法规遵从性的一种方法。本节的其余部分描述了为满足前面提到的三个要求而支持的方法。一旦您采取措施满足这些要求，并且在发布图像、源代码和构建系统之前，您应该审核所有工件以确保完整性。

::: note
::: title
Note
:::

The Yocto Project generates a license manifest during image creation that is located in `${DEPLOY_DIR}/licenses/`[image_name]{.title-ref}`-`[datestamp]{.title-ref} to assist with any audits.

> Yocto 项目在图像创建过程中生成一个许可证清单，该清单位于 `${DEPLOY_DIR}/licents/`[image_name]{.title-ref}`-`[datestamp]{.ttitle-ref}中，以协助进行任何审计。
> :::

## Providing the Source Code

Compliance activities should begin before you generate the final image. The first thing you should look at is the requirement that tops the list for most compliance groups \-\-- providing the source. The Yocto Project has a few ways of meeting this requirement.

> 合规活动应在生成最终图像之前开始。首先应该考虑的是，对于大多数合规性小组来说，最重要的要求是提供来源。Yocto 项目有几种方法可以满足这一要求。

One of the easiest ways to meet this requirement is to provide the entire `DL_DIR`{.interpreted-text role="term"} used by the build. This method, however, has a few issues. The most obvious is the size of the directory since it includes all sources used in the build and not just the source used in the released image. It will include toolchain source, and other artifacts, which you would not generally release. However, the more serious issue for most companies is accidental release of proprietary software. The Yocto Project provides an `ref-classes-archiver`{.interpreted-text role="ref"} class to help avoid some of these concerns.

> 满足这一要求的最简单方法之一是提供构建所使用的整个 `DL_DIR`{.depredicted text role=“term”}。然而，这种方法也有一些问题。最明显的是目录的大小，因为它包括构建中使用的所有源，而不仅仅是发布的映像中使用的源。它将包括工具链源代码和其他工件，您通常不会发布这些工件。然而，对大多数公司来说，更严重的问题是专有软件的意外发布。Yocto 项目提供了一个 `ref classes archiver`｛.explored text role=“ref”｝类，以帮助避免其中的一些问题。

Before you employ `DL_DIR`{.interpreted-text role="term"} or the `ref-classes-archiver`{.interpreted-text role="ref"} class, you need to decide how you choose to provide source. The source `ref-classes-archiver`{.interpreted-text role="ref"} class can generate tarballs and SRPMs and can create them with various levels of compliance in mind.

> 在使用 `DL_DIR`｛.depreted text role=“term”｝或 `ref classes archiver`｛.epreted text role=“ref”｝类之前，您需要决定如何选择提供源。源 `ref classes archiver`{.depredicted text role=“ref”}类可以生成 tarball 和 SRPM，并可以在创建它们时考虑到各种级别的合规性。

One way of doing this (but certainly not the only way) is to release just the source as a tarball. You can do this by adding the following to the `local.conf` file found in the `Build Directory`{.interpreted-text role="term"}:

> 这样做的一种方法（但肯定不是唯一的方法）是将源代码作为 tarball 发布。您可以通过将以下内容添加到“构建目录”中的“local.conf”文件中来做到这一点｛.depreted text role=“term”｝：

```
INHERIT += "archiver"
ARCHIVER_MODE[src] = "original"
```

During the creation of your image, the source from all recipes that deploy packages to the image is placed within subdirectories of `DEPLOY_DIR/sources` based on the `LICENSE`{.interpreted-text role="term"} for each recipe. Releasing the entire directory enables you to comply with requirements concerning providing the unmodified source. It is important to note that the size of the directory can get large.

> 在创建映像的过程中，将包部署到映像的所有配方中的源都会根据每个配方的“LICENSE”｛.explored text role=“term”｝放置在“deploy_DIR/sources”的子目录中。释放整个目录使您能够遵守有关提供未修改源的要求。需要注意的是，目录的大小可能会变大。

A way to help mitigate the size issue is to only release tarballs for licenses that require the release of source. Let us assume you are only concerned with GPL code as identified by running the following script:

> 帮助缓解大小问题的一种方法是只为需要发布源代码的许可证发布 tarball。让我们假设您只关心通过运行以下脚本识别的 GPL 代码：

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
      p=${p%-*}
      p=${p%-*}
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

> 此时，您可以从“gpl_source_release”目录创建一个 tarball，并将其提供给最终用户。该方法将是实现符合 GPLv2 的第 3a 节和 GPLv3 的第 6 节的步骤。

## Providing License Text

One requirement that is often overlooked is inclusion of license text. This requirement also needs to be dealt with prior to generating the final image. Some licenses require the license text to accompany the binary. You can achieve this by adding the following to your `local.conf` file:

> 一个经常被忽视的要求是包含许可证文本。在生成最终图像之前，还需要处理这一要求。有些许可证要求二进制文件附带许可证文本。您可以通过在“local.conf”文件中添加以下内容来实现这一点：

```
COPY_LIC_MANIFEST = "1"
COPY_LIC_DIRS = "1"
LICENSE_CREATE_PACKAGE = "1"
```

Adding these statements to the configuration file ensures that the licenses collected during package generation are included on your image.

> 将这些语句添加到配置文件可以确保在包生成过程中收集的许可证包含在映像中。

::: note
::: title
Note
:::

Setting all three variables to \"1\" results in the image having two copies of the same license file. One copy resides in `/usr/share/common-licenses` and the other resides in `/usr/share/license`.

> 将所有三个变量都设置为“1”会导致图像具有同一许可证文件的两个副本。一个副本位于“/usr/share/common licenses”中，另一个位于“/usr/share/license”中。

The reason for this behavior is because `COPY_LIC_DIRS`{.interpreted-text role="term"} and `COPY_LIC_MANIFEST`{.interpreted-text role="term"} add a copy of the license when the image is built but do not offer a path for adding licenses for newly installed packages to an image. `LICENSE_CREATE_PACKAGE`{.interpreted-text role="term"} adds a separate package and an upgrade path for adding licenses to an image.

> 出现这种行为的原因是，`COPY_LIC_DIRS`｛.depreted text role=“term”｝和 `COPY_LIC_MANIFEST`｛.epreted text role=“term“｝在生成映像时添加许可证的副本，但不提供将新安装的程序包的许可证添加到映像的路径 `LICENSE_CREATE _PACKAGE`｛.explored text role=“term”｝添加了一个单独的程序包和一个用于向映像添加许可证的升级路径。
> :::

As the source `ref-classes-archiver`{.interpreted-text role="ref"} class has already archived the original unmodified source that contains the license files, you would have already met the requirements for inclusion of the license information with source as defined by the GPL and other open source licenses.

> 由于源 `ref classes archiver`｛.explored text role=“ref”｝class 已经存档了包含许可证文件的原始未修改源，因此您已经满足了包含 GPL 和其他开源许可证定义的源的许可证信息的要求。

## Providing Compilation Scripts and Source Code Modifications

At this point, we have addressed all we need to prior to generating the image. The next two requirements are addressed during the final packaging of the release.

> 在这一点上，我们已经解决了在生成图像之前所需要的所有问题。接下来的两个需求将在发布的最终包装过程中解决。

By releasing the version of the OpenEmbedded build system and the layers used during the build, you will be providing both compilation scripts and the source code modifications in one step.

> 通过发布 OpenEmbedded 构建系统的版本和构建过程中使用的层，您将在一个步骤中提供编译脚本和源代码修改。

If the deployment team has a `overview-manual/concepts:bsp layer`{.interpreted-text role="ref"} and a distro layer, and those those layers are used to patch, compile, package, or modify (in any way) any open source software included in your released images, you might be required to release those layers under section 3 of GPLv2 or section 1 of GPLv3. One way of doing that is with a clean checkout of the version of the Yocto Project and layers used during your build. Here is an example:

> 如果部署团队有一个“概述手册/概念：bsp 层”｛.explored text role=“ref”｝和一个发行版层，并且这些层用于修补、编译、打包或修改（以任何方式）已发布映像中包含的任何开源软件，则可能需要根据 GPLv2 第 3 节或 GPLv3 第 1 节发布这些层。一种方法是对 Yocto 项目的版本和构建过程中使用的层进行干净的检查。以下是一个示例：

```shell
# We built using the dunfell branch of the poky repo
$ git clone -b dunfell git://git.yoctoproject.org/poky
$ cd poky
# We built using the release_branch for our layers
$ git clone -b release_branch git://git.mycompany.com/meta-my-bsp-layer
$ git clone -b release_branch git://git.mycompany.com/meta-my-software-layer
# clean up the .git repos
$ find . -name ".git" -type d -exec rm -rf {} \;
```

One thing a development organization might want to consider for end-user convenience is to modify `meta-poky/conf/templates/default/bblayers.conf.sample` to ensure that when the end user utilizes the released build system to build an image, the development organization\'s layers are included in the `bblayers.conf` file automatically:

> 为了方便最终用户，开发组织可能需要考虑的一件事是修改“meta poky/conf/templates/default/bbblayers.conf.sample”，以确保当最终用户使用发布的构建系统构建映像时，开发组织的层自动包含在“bblayers.conf”文件中：

```
# POKY_BBLAYERS_CONF_VERSION is increased each time build/conf/bblayers.conf
# changes incompatibly
POKY_BBLAYERS_CONF_VERSION = "2"

BBPATH = "${TOPDIR}"
BBFILES ?= ""

BBLAYERS ?= " \
  ##OEROOT##/meta \
  ##OEROOT##/meta-poky \
  ##OEROOT##/meta-yocto-bsp \
  ##OEROOT##/meta-mylayer \
  "
```

Creating and providing an archive of the `Metadata`{.interpreted-text role="term"} layers (recipes, configuration files, and so forth) enables you to meet your requirements to include the scripts to control compilation as well as any modifications to the original source.

> 创建并提供 `Metadata`｛.explored text role=“term”｝层（配方、配置文件等）的存档，使您能够满足包含脚本以控制编译以及对原始源的任何修改的要求。

## Compliance Limitations with Executables Built from Static Libraries

When package A is added to an image via the `RDEPENDS`{.interpreted-text role="term"} or `RRECOMMENDS`{.interpreted-text role="term"} mechanisms as well as explicitly included in the image recipe with `IMAGE_INSTALL`{.interpreted-text role="term"}, and depends on a static linked library recipe B (`DEPENDS += "B"`), package B will neither appear in the generated license manifest nor in the generated source tarballs. This occurs as the `ref-classes-license`{.interpreted-text role="ref"} and `ref-classes-archiver`{.interpreted-text role="ref"} classes assume that only packages included via `RDEPENDS`{.interpreted-text role="term"} or `RRECOMMENDS`{.interpreted-text role="term"} end up in the image.

> 当包 A 通过 `RDEPENDS`｛.explored text role=“term”｝或 `RRECOMMENDS`{.explered text rol=“term“｝机制添加到图像中，并明确包含在带有 `image_INSTAL` 的图像配方中时｛，包 B 既不会出现在所生成的许可证清单中，也不会出现在生成的源 tarball 中。这是因为 `ref classes license`｛.depredicted text role=“ref”｝和 `ref classes-archiver`｛.epredicted text-role=“ref”}类假设只有通过 `RDEPENDS`｛.repredicted ext-role=”term“｝或 `RRECOMMENDS`{.depredictedtext-role=”term”｝包含的包才会出现在图像中。

As a result, potential obligations regarding license compliance for package B may not be met.

> 因此，可能无法满足与包 B 的许可证合规性有关的潜在义务。

The Yocto Project doesn\'t enable static libraries by default, in part because of this issue. Before a solution to this limitation is found, you need to keep in mind that if your root filesystem is built from static libraries, you will need to manually ensure that your deliveries are compliant with the licenses of these libraries.

> Yocto 项目默认情况下不启用静态库，部分原因是这个问题。在找到此限制的解决方案之前，您需要记住，如果根文件系统是从静态库构建的，则需要手动确保您的交付符合这些库的许可证。

# Copying Non Standard Licenses

Some packages, such as the linux-firmware package, have many licenses that are not in any way common. You can avoid adding a lot of these types of common license files, which are only applicable to a specific package, by using the `NO_GENERIC_LICENSE`{.interpreted-text role="term"} variable. Using this variable also avoids QA errors when you use a non-common, non-CLOSED license in a recipe.

> 有些软件包，例如 linux 固件软件包，有许多许可证，但这些许可证并不常见。通过使用 `NO_GENERIC_license`{.depreced text role=“term”}变量，可以避免添加许多此类仅适用于特定软件包的常见许可证文件。当您在配方中使用非通用、非 CLOSED 许可证时，使用此变量也可以避免 QA 错误。

Here is an example that uses the `LICENSE.Abilis.txt` file as the license from the fetched source:

> 下面是一个使用 `LICENSE.Abilis.txt ` 文件作为获取源的许可证的示例：

```
NO_GENERIC_LICENSE[Firmware-Abilis] = "LICENSE.Abilis.txt"
```
