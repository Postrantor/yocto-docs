---
tip: translate by baidu@2023-06-07 17:16:23
...
---
title: Creating a Software Bill of Materials
--------------------------------------------

Once you are able to build an image for your project, once the licenses for each software component are all identified (see \"`dev-manual/licenses:working with licenses`{.interpreted-text role="ref"}\") and once vulnerability fixes are applied (see \"`dev-manual/vulnerabilities:checking for vulnerabilities`{.interpreted-text role="ref"}\"), the OpenEmbedded build system can generate a description of all the components you used, their licenses, their dependencies, their sources, the changes that were applied to them and the known vulnerabilities that were fixed.

> 一旦您能够为您的项目构建映像，一旦每个软件组件的许可证都被识别出来（请参阅\“`dev manual/licenses:使用许可证”｛.depreted text role=“ref”｝\”），并且一旦应用了漏洞修复（请参阅“` devmanual/脆弱性：检查漏洞”｛.repreted text role=“ref”}\”），OpenEmbedded 构建系统可以生成您使用的所有组件、它们的许可证、依赖项、它们的源、应用于它们的更改以及修复的已知漏洞的描述。

This description is generated in the form of a *Software Bill of Materials* (`SBOM`{.interpreted-text role="term"}), using the `SPDX`{.interpreted-text role="term"} standard.

> 本说明以*软件材料清单*（`SBOM`{.depreted text role=“term”}）的形式生成，使用 `SPDX`{.deploted text rol=“term“}标准。

When you release software, this is the most standard way to provide information about the Software Supply Chain of your software image and SDK. The `SBOM`{.interpreted-text role="term"} tooling is often used to ensure open source license compliance by providing the license texts used in the product which legal departments and end users can read in standardized format.

> 当您发布软件时，这是提供软件镜像和 SDK 的软件供应链信息的最标准方式。“SBOM”｛.explored text role=“term”｝工具通常用于通过提供产品中使用的许可文本来确保开源许可的合规性，法律部门和最终用户可以以标准格式阅读这些文本。

`SBOM`{.interpreted-text role="term"} information is also critical to performing vulnerability exposure assessments, as all the components used in the Software Supply Chain are listed.

> `SBOM `｛.explored text role=“term”｝信息对于执行漏洞暴露评估也至关重要，因为软件供应链中使用的所有组件都已列出。

The OpenEmbedded build system doesn\'t generate such information by default. To make this happen, you must inherit the `ref-classes-create-spdx`{.interpreted-text role="ref"} class from a configuration file:

> 默认情况下，OpenEmbedded 构建系统不会生成此类信息。要做到这一点，您必须从配置文件继承 `ref classes create spdx`｛.respered text role=“ref”｝类：

```
INHERIT += "create-spdx"
```

You then get `SPDX`{.interpreted-text role="term"} output in JSON format as an `IMAGE-MACHINE.spdx.json` file in `tmp/deploy/images/MACHINE/` inside the `Build Directory`{.interpreted-text role="term"}.

> 然后，您可以在 `Build Directory` 内的 `tmp/deploy/images/MACHINE/` 中获得 JSON 格式的 `SPDX`{.expreted text role=“term”}输出，作为 `IMAGE-MACHINE.SPDX.JSON` 文件。

This is a toplevel file accompanied by an `IMAGE-MACHINE.spdx.index.json` containing an index of JSON `SPDX`{.interpreted-text role="term"} files for individual recipes, together with an `IMAGE-MACHINE.spdx.tar.zst` compressed archive containing all such files.

> 这是一个顶级文件，附带一个 `IMAGE-MACHINE.spdx.index.json`，其中包含单个配方的 json `spdx`｛.explored text role=“term”｝文件的索引，以及一个包含所有此类文件的 `IMAGE-MACHINE.spdx.tar.zst` 压缩档案。

The `ref-classes-create-spdx`{.interpreted-text role="ref"} class offers options to include more information in the output `SPDX`{.interpreted-text role="term"} data, such as making the generated files more human readable (`SPDX_PRETTY`{.interpreted-text role="term"}), adding compressed archives of the files in the generated target packages (`SPDX_ARCHIVE_PACKAGED`{.interpreted-text role="term"}), adding a description of the source files used to generate host tools and target packages (`SPDX_INCLUDE_SOURCES`{.interpreted-text role="term"}) and adding archives of these source files themselves (`SPDX_ARCHIVE_SOURCES`{.interpreted-text role="term"}).

> `ref classes create spdx`｛.depredicted text role=“ref”｝类提供了在输出 `spdx`｛.epredicted textrole=”term“｝数据中包括更多信息的选项，例如使生成的文件更易于阅读（`spdx_PRETTY`｛.repredicted extrole=‘term”｝），在生成的目标包中添加文件的压缩存档（`spdx_ARCHIVE_PAGED｝｛.deverdicted text-lole=“term”}），添加用于生成主机工具和目标包的源文件的描述（` SPDX_INCLUDE_SOURCES `｛.depreted text role=“term”｝），并添加这些源文件本身的存档（` SPDX_ARCHIVE_SOURCES`{.depreted textrole=”term“｝）。

Though the toplevel `SPDX`{.interpreted-text role="term"} output is available in `tmp/deploy/images/MACHINE/` inside the `Build Directory`{.interpreted-text role="term"}, ancillary generated files are available in `tmp/deploy/spdx/MACHINE` too, such as:

> 尽管顶层“SPDX”｛.explored text role=“term”｝输出在“构建目录”内的“tmp/deploy/images/MACHINE/”中可用，但辅助生成的文件在“tmp/deloy/SPDX/MACHINE”中也可用，例如：

- The individual `SPDX`{.interpreted-text role="term"} JSON files in the `IMAGE-MACHINE.spdx.tar.zst` archive.

> -`IMAGE-MACHINE.SPDX.tar.zst` 存档中的单个 `SPDX`｛.explored text role=“term”｝JSON 文件。

- Compressed archives of the files in the generated target packages, in `packages/packagename.tar.zst` (when `SPDX_ARCHIVE_PACKAGED`{.interpreted-text role="term"} is set).

> -生成的目标包中文件的压缩存档，位于 `packages/packagename.tar.zst` 中（当设置了 `SPDX_ARCHIVE_PACKAGED`{.depreted text role=“term”}时）。

- Compressed archives of the source files used to build the host tools and the target packages in `recipes/recipe-packagename.tar.zst` (when `SPDX_ARCHIVE_SOURCES`{.interpreted-text role="term"} is set). Those are needed to fulfill \"source code access\" license requirements.

> -用于在 `recipes/recipe packagename.tar.zst` 中构建主机工具和目标包的源文件的压缩存档（当设置了 `SPDX_ARCHIVE_SOURCES`{.depreted text role=“term”}时）。这些是满足“源代码访问”许可证要求所必需的。

See also the `SPDX_CUSTOM_ANNOTATION_VARS`{.interpreted-text role="term"} variable which allows to associate custom notes to a recipe.

> 另请参阅 `SPDX_CUSTOM_ANNOTATION_ARS`｛.explored text role=“term”｝变量，该变量允许将自定义注释与配方关联起来。

See the [tools page](https://spdx.dev/resources/tools/) on the `SPDX`{.interpreted-text role="term"} project website for a list of tools to consume and transform the `SPDX`{.interpreted-text role="term"} data generated by the OpenEmbedded build system.

> 请参阅[工具页](https://spdx.dev/resources/tools/)关于消费和转换 OpenEmbedded 构建系统生成的 `SPDX`｛.depredicted text role=“term”｝数据的工具列表，请访问 `SPDX'｛.epredicted text role=”term“｝项目网站。

See also Joshua Watt\'s [Automated SBoM generation with OpenEmbedded and the Yocto Project](https://youtu.be/Q5UQUM6zxVU) presentation at FOSDEM 2023.

> 另请参阅 Joshua Watt [利用 OpenEmbedded 和 Yocto 项目自动生成 SBoM](https://youtu.be/Q5UQUM6zxVU) 在 FOSDEM 2023 上的演讲。
