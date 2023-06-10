---
tip: translate by openai@2023-06-07 21:05:52
...

# Standards for contributing to Yocto Project documentation

This document attemps to standardize the way the Yocto Project documentation is created.

> 本文档试图标准化 Yocto 项目文档的创建方式。

It is currently a work in progress.

> 目前正在进行中。

## Text standards

### Bulleted lists

Though Sphinx supports both the `*` and `-` characters for introducing bulleted lists, we have chosen to use only `-` for this purpose.

> 尽管斯芬克斯支持用`*`和`-`来创建项目列表，我们只选择使用`-`来实现这一目的。

Though not strictly required by Sphinx, we have also chosen to use two space characters after `-` to introduce each list item:

> 尽管 Sphinx 并不严格要求，我们也选择在每个列表项后使用两个空格字符来介绍：

    -  Paragraph 1
    -  Paragraph 2

As shown in the above example, there should also be an empty line between each list item.

> 如上例所示，每个列表项之间应该有一个空行。

An exception to this rule is when the list items are just made of a few words, instead of entire paragraphs:

> 例外情况是，如果列表项只由几个词组成，而不是整个段落：

    -  Item 1
    -  Item 2

This is again a matter of style, not syntax.

> 这又是一个关于风格的问题，而不是语法问题。

### Line wrapping

Source code for the documentation shouldn't have lines wider than 80 characters. This makes patch lines more readable and code easier to quote in e-mail clients.

> 源代码的文档不应该有超过 80 个字符的行。这使得补丁行更加可读，代码更容易在电子邮件客户端中引用。

If you have to include long commands or lines in configuration files, provided the syntax makes this possible, split them into multiple lines, using the `\` character.

> 如果语法允许，在配置文件中包含长命令或行，请使用`\`字符将它们拆分为多行。

Here is an example:

> 这是一个例子：

    $ scripts/install-buildtools \
      --without-extended-buildtools \
      --base-url https://downloads.yoctoproject.org/releases/yocto \
      --release yocto-4.0.1 \
      --installer-version 4.0.1

Exceptions are granted for file contents whose lines cannot be split without infringing syntactic rules or reducing readability, as well as for command output which should be kept unmodified.

> 例外是针对文件内容的，如果按照语法规则分割行会违反语法规则或者降低可读性，以及应保持未修改的命令输出而授予的。

### Project names

Project names should be capitalized in the same way they are on Wikipedia, in particular:

> 项目名称应与维基百科上的一致进行大写处理，特别是：

- BitBake
- OpenEmbedded

There are exceptions in which such names can be used in lower case:

> 在某些情况下，可以使用小写的这类名称：

- When referring to a package name
- When referring to the corresponding command name
- When used in a cross-reference title. Such titles are usually in lower case.

### File, tool and command names

File, tool and command names should be double tick-quoted. For example, ` ``conf/local.conf`` ` is preferred over `"conf/local.conf"`.

> 文件、工具和命令名称应该用双引号引起来。例如，更喜欢用 ``conf/local.conf` `而不是`"conf/local.conf"`。

### Variables

Every variable should be mentioned with:

> 每个变量都应该被提及。

    :term:`VARIABLE`

This assumes that `VARIABLE` is described either in the Yocto Project documentation variable index (`ref-manual/variables.rst`) or in the BitBake User Manual (`doc/bitbake-user-manual/bitbake-user-manual-ref-variables.rst`)

> 这假定`VARIABLE`在 Yocto Project 文档变量索引（`ref-manual/variables.rst`）或 BitBake 用户手册（`doc/bitbake-user-manual/bitbake-user-manual-ref-variables.rst`）中有描述。

If it is not described yet, the variable should be added to the glossary before or in the same patch it is used, so that `:term:` can be used.

> 如果尚未描述，则应在使用该变量之前或在同一补丁中将其添加到词汇表中，以便可以使用:term:。

## ReStructured Text Syntax standards

This section has not been filled yet

> 这一部分尚未填写。

## Adding screenshots

The preferred format for adding screenshots is [Portable Network Graphics (PNG)](https://en.wikipedia.org/wiki/Portable_Network_Graphics). Compared to the JPEG format, PNG is lossless and compresses screenshots better.

> 最佳添加屏幕截图的格式是[可移植网络图形（PNG）](https://en.wikipedia.org/wiki/Portable_Network_Graphics)。与 JPEG 格式相比，PNG 是无损的，可以更好地压缩屏幕截图。

Screenshots are stored in a `figures/` subdirectory.

> 截图储存在 `figures/` 子目录中。

To include a screenshot in PNG format:

> 在 PNG 格式中包含截图：

    .. image:: figures/user-configuration.png
       :align: center

A diagram with many details usually needs to use the whole page width to be readable on all media. In this case, the `:align:` directive is unnecessary:

> 一个具有许多细节的图表通常需要使用整个页面的宽度才能在所有媒体上可读。在这种情况下，`align`指令是不必要的。

       :scale: 100%

Conversely, you may also shrink some images to to prevent them from filling the whole page width:

> 反之，您也可以缩小一些图像，以防止它们填满整个页面宽度：

       :scale: 50%

For some types of screenshots, for example showing programs displaying photographs or playing video, the JPEG format may be more appropriate, because it is better at compressing real-life images:

> 对于一些类型的屏幕截图，例如显示照片或播放视频的程序，JPEG 格式可能更合适，因为它更擅长压缩真实图像。

    .. image:: figures/vlc.jpg
       :align: center
       :scale: 75%

## Adding new diagrams

New diagrams should be added in [Scalable Vector Graphics (SVG) format](https://en.wikipedia.org/wiki/Scalable_Vector_Graphics). Thanks to this, diagrams render nicely at any zoom level on generated documentation, instead of being pixelated.

> 新的图表应该以可伸缩矢量图形 (SVG) 格式添加。由于这一点，图表在生成的文档上以任何缩放级别呈现得很清晰，而不是像素化。

The recommended tool for creating SVG diagrams for the Yocto Project documentation is [Inkscape](https://inkscape.org/).

> 推荐使用[Inkscape](https://inkscape.org/)来为 Yocto Project 文档创建 SVG 图表。

### Colors

The "GNOME HIG Colors" palette is used in our diagrams (get it from <https://gitlab.gnome.org/Teams/Design/HIG-app-icons/-/blob/master/GNOME%20HIG.gpl> if you don't get it from Inkscape).

> "GNOME HIG 颜色" 调色板被用于我们的图表（如果您没有从 Inkscape 中获取，可以从 <https://gitlab.gnome.org/Teams/Design/HIG-app-icons/-/blob/master/GNOME%20HIG.gpl> 获取）。

It's easier to use than the default one in Inkscape, as it has fewer but sufficient colors. This is a way to guarantee that we use consistent colors across the diagrams used in our documentation.

> 它比 Inkscape 的默认颜色更容易使用，因为它有更少但足够的颜色。这是一种确保我们在文档中使用的图表中使用一致颜色的方法。

See <https://inkscape-manuals.readthedocs.io/en/latest/palette.html> for details about working with color palettes in Inkscape.

> 见<https://inkscape-manuals.readthedocs.io/en/latest/palette.html>了解关于 Inkscape 中使用颜色调色板的详细信息。

### Template diagram

The `template/` directory contains a `template.svg` file to make it easier to create diagrams. In particular, you can use it to copy standard text, shapes, arrows and clipart to the new SVG document.

> `template/` 目录中包含一个 `template.svg` 文件，可以帮助您更轻松地创建图表。特别是，您可以使用它将标准文本、形状、箭头和剪贴画复制到新的 SVG 文档中。

### Fonts

The recommended font for description text and labels is `Nimbus Roman`, size 10. Labels are in bold.

> 推荐用于描述文本和标签的字体是`Nimbus Roman`，大小为 10。标签为粗体。

`template.svg` contains ready-to-copy labels.

> `template.svg`包含可复制的标签。

### Text boxes

Text boxes are rectangle boxes, with rounded corners, and contain centered text or labels. Their stroke color is slightly darker than their fill color.

See `template.svg` for example boxes with different colors, which are ready to be reused.

> 请参考`template.svg`，里面有不同颜色的示例框，可以重复使用。

### Clipart

Only [Public Domain](https://en.wikipedia.org/wiki/Public_domain) files are accepted for clipart. [Openclipart](https://openclipart.org) is the best known and recommended source of such clipart.

> 只接受公共领域文件作为剪贴画。Openclipart 是最著名且推荐的公共领域剪贴画来源。

It is also required to state the source of each new clipart in the commit log, to make it possible to check its origin.

> 需要在提交日志中声明每个新剪贴画的来源，以便可以检查其来源。

For the sake of consistency across diagrams, we recommend to use existing cliparts, whenever possible.

> 为了保持图表的一致性，我们建议尽可能使用现有的剪贴画。

If cliparts in `template.svg` do not satisfy your requirements, you can add to said file new cliparts, from e.g. Openclipart. Newly added cliparts shall stay consistent in style and color with existing ones.

> 如果`template.svg`中的剪贴画不满足您的要求，您可以从 Openclipart 等网站添加新的剪贴画到该文件中。新添加的剪贴画应与现有的保持一致的风格和颜色。
---
