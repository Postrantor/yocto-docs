---
tip: translate by openai@2023-06-07 21:06:10
...

# documentation

This is the directory that contains the Yocto Project documentation. The Yocto Project source repositories at https://git.yoctoproject.org/cgit.cgi have two instances of the "documentation" directory. You should understand each of these instances.

> 这是包含 Yocto 项目文档的目录。在https://git.yoctoproject.org/cgit.cgi上的Yocto项目源代码库有两个“documentation”目录的实例。你应该理解这些实例。

poky/documentation - The directory within the poky Git repository containing the set of Yocto Project manuals. When you clone the poky Git repository, the documentation directory contains the manuals. The state of the manuals in this directory is guaranteed to reflect the latest Yocto Project release. The manuals at the tip of this directory will also likely contain most manual development changes.

> poky/文档 - poky Git 存储库中包含 Yocto 项目手册集的目录。当您克隆 poky Git 存储库时，文档目录将包含手册。此目录中的手册状态保证反映最新的 Yocto 项目发行版。此目录的末端手册也可能包含大多数手册开发更改。

yocto-docs/documentation - The Git repository for the Yocto Project manuals. This repository is where manual development occurs. If you plan on contributing back to the Yocto Project documentation, you should set up a local Git repository based on this upstream repository as follows:

> Yocto-docs/文档 - Yocto 项目手册的 Git 存储库。此存储库是手册开发发生的地方。如果您计划回馈 Yocto 项目文档，您应该按照以下步骤建立基于此上游存储库的本地 Git 存储库：

                               git clone git://git.yoctoproject.org/yocto-docs

                             Changes and patches are first pushed to the
                             yocto-docs Git repository.  Later, they make it
                             into the poky Git repository found at
                             git://git.yoctoproject.org/poky.

# Manual Organization

Here the folders corresponding to individual manuals:

> 这里是对应各个手册的文件夹：

- overview-manual - Yocto Project Overview and Concepts Manual
- sdk-manual - Yocto Project Software Development Kit (SDK) Developer's Guide.
- bsp-guide - Yocto Project Board Support Package (BSP) Developer's Guide
- dev-manual - Yocto Project Development Tasks Manual
- kernel-dev - Yocto Project Linux Kernel Development Manual
- ref-manual - Yocto Project Reference Manual
- brief-yoctoprojectqs - Yocto Project Quick Start
- profile-manual - Yocto Project Profiling and Tracing Manual
- toaster-manual - Toaster User Manual
- test-manual - Yocto Project Test Environment Manual

Each folder is self-contained regarding content and figures.

> 每个文件夹都是关于内容和数据独立的。

If you want to find HTML versions of the Yocto Project manuals on the web, the current versions reside at https://docs.yoctoproject.org.

> 如果你想在网上找到 Yocto 项目手册的 HTML 版本，当前版本可以在https://docs.yoctoproject.org找到。

# poky.yaml

This file defines variables used for documentation production. The variables are used to define release pathnames, URLs for the published manuals, etc.

> 这个文件定义了用于文档生产的变量。这些变量用于定义发布路径名、发布手册的 URL 等。

# standards.md

This file specifies some rules to follow when contributing to the documentation.

> 这个文件指定了一些贡献文档时遵循的规则。

# template/

Contains a template.svg, to make it easier to create consistent SVG diagrams.

> 包含模板.svg，以便更轻松地创建一致的 SVG 图表。

# Sphinx

The Yocto Project documentation was migrated from the original DocBook format to Sphinx based documentation for the Yocto Project 3.2 release. This section will provide additional information related to the Sphinx migration, and guidelines for developers willing to contribute to the Yocto Project documentation.

> 针对 Yocto Project 3.2 发布，Yocto Project 文档已从原始 DocBook 格式迁移到基于 Sphinx 的文档。本节将提供有关 Sphinx 迁移的附加信息，以及为愿意为 Yocto Project 文档做贡献的开发人员提供指南。

Sphinx is a tool that makes it easy to create intelligent and beautiful documentation, written by Georg Brandl and licensed under the BSD license. It was originally created for the Python documentation.

> 斯芬克斯是一个由 Georg Brandl 开发，根据 BSD 许可证授权的工具，它可以轻松创建智能且漂亮的文档。它最初是为 Python 文档创建的。

Extensive documentation is available on the Sphinx website: https://www.sphinx-doc.org/en/master/. Sphinx is designed to be extensible thanks to the ability to write our own custom extensions, as Python modules, which will be executed during the generation of the documentation.

> 可以在 Sphinx 网站上找到大量文档：https://www.sphinx-doc.org/en/master/。Sphinx设计用于可扩展，因为我们可以编写自己的自定义扩展，作为Python模块，在生成文档期间将执行这些模块。

# Yocto Project documentation website

The website hosting the Yocto Project documentation, can be found at: https://docs.yoctoproject.org/.

> 网站托管 Yocto 项目文档可以在https://docs.yoctoproject.org/上找到。

The entire Yocto Project documentation, as well as the BitBake manual, is published on this website, including all previously released versions. A version switcher was added, as a drop-down menu on the top of the page to switch back and forth between the various versions of the current active Yocto Project releases.

> 整个 Yocto 项目文档以及 BitBake 手册都发布在本网站上，包括所有以前发布的版本。在页面顶部添加了一个版本切换器，可以在当前活动的 Yocto 项目发行版之间来回切换。

Transition pages have been added (as rst files) to show links to old versions of the Yocto Project documentation with links to each manual generated with DocBook.

> 已添加过渡页面（作为 rst 文件），以显示到 Yocto Project 文档旧版本的链接，并使用 DocBook 生成每个手册的链接。

# How to build the Yocto Project documentation

Sphinx is written in Python. While it might work with Python2, for obvious reasons, we will only support building the Yocto Project documentation with Python3.

> 斯芬克斯是用 Python 编写的。虽然它可能在 Python2 上工作，但是出于显而易见的原因，我们将只支持使用 Python3 来构建 Yocto 项目文档。

Sphinx might be available in your Linux distro packages repositories, however it is not recommended to use distro packages, as they might be old versions, especially if you are using an LTS version of your distro. The recommended method to install the latest versions of Sphinx and of its required dependencies is to use the Python Package Index (pip).

> 可能在你的 Linux 发行版软件仓库中可以找到 Sphinx，但不推荐使用发行版软件包，因为它们可能是旧版本，特别是如果你使用的是发行版的 LTS 版本。推荐的安装 Sphinx 及其所需依赖项的最新版本的方法是使用 Python 软件包索引（pip）。

To install all required packages run:

> 要安装所有必需的软件包，请运行：

\$ pip3 install sphinx sphinx_rtd_theme pyyaml

> $ pip3 安装 sphinx sphinx_rtd_theme pyyaml

To make sure you always have the latest versions of such packages, you should regularly run the same command with an added "--upgrade" option:

> 确保您总是拥有最新版本的这些软件包，您应该定期使用带有“--upgrade”选项的相同命令运行：

\$ pip3 install --upgrade sphinx sphinx_rtd_theme pyyaml

> \$ pip3 安装并升级 sphinx sphinx_rtd_theme pyyaml

Also install the "inkscape" package from your distribution. Inkscape is need to convert SVG graphics to PNG (for EPUB export) and to PDF (for PDF export).

> 也安裝你的發行版本中的“inkscape”包。 Inkscape 需要將 SVG 圖形轉換為 PNG（用於 EPUB 導出）和 PDF（用於 PDF 導出）。

Additionally install "fncychap.sty" TeX font if you want to build PDFs. Debian and Ubuntu have it in "texlive-latex-extra" package while RedHat distributions and OpenSUSE have it in "texlive-fncychap" package for example.

> 如果你想要构建 PDF，另外安装"fncychap.sty" TeX 字体。Debian 和 Ubuntu 在"texlive-latex-extra"包中有它，而 RedHat 发行版和 OpenSUSE 在"texlive-fncychap"包中有它。

To build the documentation locally, run:

> 本地构建文档，运行：

\$ cd documentation \$ make html

> $ cd 文档 $ 生成 html

The resulting HTML index page will be \_build/html/index.html, and you can browse your own copy of the locally generated documentation with your browser.

> 结果的 HTML 索引页面将会是\_build/html/index.html，你可以在浏览器中浏览本地生成的文档的自己的副本。

Alternatively, you can use Pipenv to automatically install all required dependencies in a virtual environment:

> 你也可以使用 Pipenv 自动在虚拟环境中安装所有必需的依赖项：

\$ cd documentation \$ pipenv install \$ pipenv run make html

> $ cd 文档 $ pipenv 安装 $ pipenv 运行制作 html

# Sphinx theme and CSS customization

The Yocto Project documentation is currently based on the "Read the Docs" Sphinx theme, with a few changes to make sure the look and feel of the project documentation is preserved.

> 项目文档目前基于“阅读文档”Sphinx 主题，经过一些改动以确保项目文档的外观和感觉得以保存。

Most of the theme changes can be done using the file 'sphinx-static/theme_overrides.css'. Most CSS changes in this file were inherited from the DocBook CSS stylesheets.

> 大多数主题更改可以使用文件'sphinx-static/theme_overrides.css'来完成。此文件中的大多数 CSS 更改都是从 DocBook CSS 样式表继承而来的。

# Sphinx design guidelines and principles

The initial Docbook to Sphinx migration was done with an automated tool called Pandoc (https://pandoc.org/). The tool produced some clean output markdown text files. After the initial automated conversion additional changes were done to fix up headings, images and links. In addition Sphinx has built in mechanisms (directives) which were used to replace similar functions implemented in Docbook such as glossary, variables substitutions, notes and references.

> 初步的 Docbook 到 Sphinx 迁移是使用一个叫做 Pandoc（https://pandoc.org/）的自动化工具完成的。该工具产生了一些干净的输出Markdown文本文件。在初步的自动转换之后，还做了一些额外的更改来修复标题，图像和链接。此外，Sphinx还具有内置的机制（指令），用于替换Docbook中实现的类似功能，如词汇表，变量替换，注释和引用。

# Headings

The layout of the Yocto Project manuals is organized as follows

> Yocto Project 手册的布局按照以下方式组织。

    Book
      Chapter
        Section
          Subsection
            Subsubsection
              Subsubsubsection

Here are the heading styles that we use in the manuals:

> 这是我们在手册中使用的标题样式：

    Book                       => overline ===
      Chapter                  => overline ***
        Section                => ====
          Subsection           => ----
            Subsubsection      => ~~~~
              Subsubsubsection => ^^^^

With this proposal, we preserve the same TOCs between Sphinx and Docbook.

> 随着这个提议，我们在 Sphinx 和 Docbook 之间保持相同的目录结构。

# Built-in glossary

Sphinx has a glossary directive. From https://www.sphinx-doc.org/en/master/usage/restructuredtext/directives.html#glossary:

> 斯芬克斯有一个词汇表指令。来自https://www.sphinx-doc.org/en/master/usage/restructuredtext/directives.html#glossary:

    This directive must contain a reST definition list with terms and
    definitions. It's then possible to refer to each definition through the
    [https://www.sphinx-doc.org/en/master/usage/restructuredtext/roles.html#role-term
    'term' role].

So anywhere in any of the Yocto Project manuals, :term:`VAR` can be used to refer to an item from the glossary, and a link is created automatically. A general index of terms is also generated by Sphinx automatically.

> 在 Yocto 项目手册中的任何地方，都可以使用:term:`VAR`来引用词汇表中的项目，并且会自动生成链接。Sphinx 也会自动生成一个术语的总索引。

# Global substitutions

The Yocto Project documentation makes heavy use of global variables. In Docbook these variables are stored in the file poky.ent. This Docbook feature is not handled automatically with Pandoc. Sphinx has builtin support for substitutions (https://www.sphinx-doc.org/en/master/usage/restructuredtext/basics.html#substitutions), however there are important shortcomings. For example they cannot be used/nested inside code-block sections.

> 项目 Yocto 的文档大量使用全局变量。在 Docbook 中，这些变量存储在文件 poky.ent 中。Pandoc 没有自动处理这个 Docbook 特性。Sphinx 有内置的替换支持（https://www.sphinx-doc.org/en/master/usage/restructuredtext/basics.html#substitutions），但是也有重要的缺点。例如，它们不能在代码块部分内部使用/嵌套。

A Sphinx extension was implemented to support variable substitutions to mimic the DocBook based documentation behavior. Variable substitutions are done while reading/parsing the .rst files. The pattern for variables substitutions is the same as with DocBook, e.g. `&VAR;`.

> 一个 Sphinx 扩展被实现来支持变量替换以模仿基于 DocBook 的文档行为。读取/解析.rst 文件时会进行变量替换。变量替换的模式与 DocBook 相同，例如'&VAR;'。

The implementation of the extension can be found here in the file documentation/sphinx/yocto-vars.py, this extension is enabled by default when building the Yocto Project documentation. All variables are set in a file call poky.yaml, which was initially generated from poky.ent. The file was converted into YAML so that it is easier to process by the custom Sphinx extension (which is a Python module).

> 实现该扩展的文件可以在 documentation/sphinx/yocto-vars.py 中找到，构建 Yocto Project 文档时默认启用此扩展。所有变量都设置在一个名为 poky.yaml 的文件中，该文件最初是从 poky.ent 生成的。该文件被转换为 YAML，以便自定义 Sphinx 扩展（即 Python 模块）更容易处理。

For example, the following .rst content will produce the 'expected' content:

> 例如，以下.rst 内容将产生预期的内容：

.. code-block:: \$ mkdir poky-&DISTRO; or \$ git clone &YOCTO_GIT_URL;/git/poky -b &DISTRO_NAME_NO_CAP;

> 代码块：\$ mkdir poky-&DISTRO; 或 \$ git clone &YOCTO_GIT_URL;/git/poky -b &DISTRO_NAME_NO_CAP;

Variables can be nested, like it was the case for DocBook:

> 变量可以嵌套，就像 DocBook 的情况一样。

YOCTO_HOME_URL : "https://www.yoctoproject.org" YOCTO_DOCS_URL : "&YOCTO_HOME_URL;/docs"

> YOCTO_HOME_URL："https://www.yoctoproject.org" YOCTO_DOCS_URL："&YOCTO_HOME_URL;/docs"

# Note directive

Sphinx has a builtin 'note' directive that produces clean Note section in the output file. There are various types of directives such as "attention", "caution", "danger", "error", "hint", "important", "tip", "warning", "admonition" that are supported, and additional directives can be added as Sphinx extension if needed.

> 斯芬克斯有一个内置的“注意”指令，可以在输出文件中生成干净的注释部分。支持各种类型的指令，如“注意”、“小心”、“危险”、“错误”、“提示”、“重要”、“提示”、“警告”和“劝告”，如果需要，还可以添加额外的指令作为斯芬克斯的扩展。

# Figures

The Yocto Project documentation has many figures/images. Sphinx has a 'figure' directive which is straightforward to use. To include a figure in the body of the documentation:

> Yocto 项目文档有很多图片。Sphinx 有一个“图形”指令，使用起来很简单。要在文档正文中包含图片：

.. image:: figures/YP-flow-diagram.png

> .. 图像:: figures/YP-flow-diagram.png

# Links and References

The following types of links can be used: links to other locations in the same document, to locations in other documents and to external websites.

> 以下类型的链接可以使用：链接到同一文档中的其他位置，到其他文档中的位置以及外部网站。

More information can be found here: https://sublime-and-sphinx-guide.readthedocs.io/en/latest/references.html.

> 更多信息可以在这里找到：https://sublime-and-sphinx-guide.readthedocs.io/en/latest/references.html.

For external links, we use this syntax: `link text <link URL>`\_\_

> 对于外部链接，我们使用此语法：`链接文本 <链接URL>`

instead of: `link text <link URL>`\_

> 代替：链接文本<链接 URL>

Both syntaxes work, but the latter also creates a "link text" reference target which could conflict with other references with the same name. So, only use this variant when you wish to make multiple references to this link, reusing only the target name.

> 两种语法都可以工作，但后者还会创建一个“链接文本”引用目标，可能与具有相同名称的其他引用发生冲突。因此，只有当您希望对此链接进行多次引用，仅重用目标名称时，才使用此变体。

See https://stackoverflow.com/questions/27420317/restructured-text-rst-http-links-underscore-vs-use

> 请参阅https://stackoverflow.com/questions/27420317/restructured-text-rst-http-links-underscore-vs-use

Anchor (\<#link\>) links are forbidden as they are not checked by Sphinx during the build and may be broken without knowing about it.

> 锚点（\<#link\>）链接是被禁止的，因为在构建过程中它们不被 Sphinx 检查，可能会在不知情的情况下变得无效。

# References

The following extension is enabled by default: sphinx.ext.autosectionlabel (https://www.sphinx-doc.org/en/master/usage/extensions/autosectionlabel.html).

> 以下扩展默认启用：sphinx.ext.autosectionlabel（https://www.sphinx-doc.org/en/master/usage/extensions/autosectionlabel.html）。

This extension allows you to refer sections by their titles. Note that autosectionlabel_prefix_document is enabled by default, so that we can insert references from any document.

> 这个扩展允许您通过它们的标题引用部分。请注意，autosectionlabel_prefix_document 默认启用，因此我们可以从任何文档中插入引用。

For example, to insert an HTML link to a section from documentation/manual/intro.rst, use:

> 例如，要插入一个指向文档/手册/intro.rst 中某个部分的 HTML 链接，请使用：

Please check this :ref:`manual/intro:Cross-References to Locations in the Same Document`

> 请检查这个：ref：“同一文档中的位置的交叉引用”手册/介绍。

Alternatively a custom text can be used instead of using the section text:

> 另外，可以使用自定义文本来代替使用节文本：

Please check this :ref:`section <manual/intro:Cross-References to Locations in the Same Document>`

> 请检查这个:ref:`部分<manual/intro:同一文档中的位置的交叉引用>`

TIP: The following command can be used to dump all the references that are defined in the project documentation:

> TIP：可以使用以下命令来转储项目文档中定义的所有引用：

       python -msphinx.ext.intersphinx <path to build folder>/html/objects.inv

This dump contains all links and for each link it shows the default "Link Text" that Sphinx would use. If the default link text is not appropriate, a custom link text can be used in the ':ref:' directive.

> 这个转储包含所有链接，对于每个链接，它都显示 Sphinx 默认的“链接文本”。如果默认链接文本不合适，可以在':ref:'指令中使用自定义链接文本。

# Extlinks

The sphinx.ext.extlinks extension is enabled by default (https://sublime-and-sphinx-guide.readthedocs.io/en/latest/references.html#use-the-external-links-extension), and it is configured with the 'extlinks' definitions in the 'documentation/conf.py' file:

> 默认启用了 sphinx.ext.extlinks 扩展（https://sublime-and-sphinx-guide.readthedocs.io/en/latest/references.html#use-the-external-links-extension），并且它在'documentation/conf.py'文件中使用'extlinks'定义进行配置：

'yocto_home': ('https://yoctoproject.org%s', None), 'yocto_wiki': ('https://wiki.yoctoproject.org%s', None), 'yocto_dl': ('https://downloads.yoctoproject.org%s', None), 'yocto_lists': ('https://lists.yoctoproject.org%s', None), 'yocto_bugs': ('https://bugzilla.yoctoproject.org%s', None), 'yocto_ab': ('https://autobuilder.yoctoproject.org%s', None), 'yocto_docs': ('https://docs.yoctoproject.org%s', None), 'yocto_git': ('https://git.yoctoproject.org%s', None), 'oe_home': ('https://www.openembedded.org%s', None), 'oe_lists': ('https://lists.openembedded.org%s', None), 'oe_git': ('https://git.openembedded.org%s', None), 'oe_wiki': ('https://www.openembedded.org/wiki%s', None), 'oe_layerindex': ('https://layers.openembedded.org%s', None), 'oe_layer': ('https://layers.openembedded.org/layerindex/branch/master/layer%s', None),

> 'yocto_home': ('https://yoctoproject.org%s', None), 'yocto_wiki': ('https://wiki.yoctoproject.org%s', None), 'yocto_dl': ('https://downloads.yoctoproject.org%s', None), 'yocto_lists': ('https://lists.yoctoproject.org%s', None), 'yocto_bugs': ('https://bugzilla.yoctoproject.org%s', None), 'yocto_ab': ('https://autobuilder.yoctoproject.org%s', None), 'yocto_docs': ('https://docs.yoctoproject.org%s', None), 'yocto_git': ('https://git.yoctoproject.org%s', None), 'oe_home': ('https://www.openembedded.org%s', None), 'oe_lists': ('https://lists.openembedded.org%s', None), 'oe_git': ('https://git.openembedded.org%s', None), 'oe_wiki': ('https://www.openembedded.org/wiki%s', None), 'oe_layerindex': ('https://layers.openembedded.org%s', None), 'oe_layer': ('https://layers.openembedded.org/layerindex/branch/master/layer%s', None),

yocto_home：（'https：//yoctoproject.org％s'，无），yocto_wiki：（'https：//wiki.yoctoproject.org％s'，无），yocto_dl：（'https：//downloads.yoctoproject.org％s'，无），yocto_lists：（'https：//lists.yoctoproject.org％s'，无），yocto_bugs：（'https：//bugzilla.yoctoproject.org％s'，无），yocto_ab：（'https：//autobuilder.yoctoproject.org％s'，无），yocto_docs：（'https：//docs.yoctoproject.org％s'，无），yocto_git：（'https：//git.yoctoproject.org％s'，无），oe_home：（'https：//www.openembedded.org％s'，无），oe_lists：（'https：//lists.openembedded.org％s'，无），oe_git：（'https：//git.openembedded.org％s'，无），oe_wiki：（'https：//www.openembedded.org/wiki％s'，无），oe_layerindex：（'https：//layers.openembedded.org％s'，无），oe_layer：（'https：//layers.openembedded.org/layerindex/branch/master/layer％s'，无）

It creates convenient shortcuts which can be used throughout the documentation rst files, as:

> 它创建了方便的快捷方式，可以在文档 rst 文件中使用，如：

Please check this :yocto_wiki:`wiki page </Weekly_Status>`

> 请检查这个 yocto wiki 页面：每周状态

# Intersphinx links

The sphinx.ext.intersphinx extension is enabled by default (https://www.sphinx-doc.org/en/master/usage/extensions/intersphinx.html), so that we can cross reference content from other Sphinx based documentation projects, such as the BitBake manual.

> Sphinx.ext.intersphinx 扩展默认情况下已启用（https://www.sphinx-doc.org/en/master/usage/extensions/intersphinx.html），因此我们可以跨引用其他基于Sphinx的文档项目，如BitBake手册。

References to the BitBake manual can directly be done: - With a specific description instead of the section name: :ref:`Azure Storage fetcher (az://) <bitbake-user-manual/bitbake-user-manual-fetching:fetchers>` - With the section name: :ref:`bitbake-user-manual/bitbake-user-manual-intro:usage and syntax` option

> 参考 BitBake 手册可以直接这样做：- 使用特定描述而不是章节名：:ref:`Azure Storage fetcher (az://) <bitbake-user-manual/bitbake-user-manual-fetching:fetchers>` - 使用章节名：:ref:`bitbake-user-manual/bitbake-user-manual-intro:usage and syntax` 选项

If you want to refer to an entire document (or chapter) in the BitBake manual, you have to use the ":doc:" macro with the "bitbake:" prefix: - :doc:`BitBake User Manual <bitbake:index>` - :doc:`bitbake:bitbake-user-manual/bitbake-user-manual-metadata`" chapter

> 如果您想引用 BitBake 手册中的整个文档（或章节），您必须使用带有“bitbake：”前缀的“:doc：”宏：-：doc：`BitBake用户手册<bitbake：index>`-：doc：`bitbake：bitbake-user-manual / bitbake-user-manual-metadata`“章节

Note that a reference to a variable (:term:`VARIABLE`) automatically points to the BitBake manual if the variable is not described in the Reference Manual's Variable Glossary. However, if you need to bypass this, you can explicitely refer to a description in the BitBake manual as follows:

:term:`bitbake:BB_NUMBER_PARSE_THREADS`

This would be the same if we had identical document filenames in both the Yocto Project and BitBake manuals:

> 如果我们在 Yocto Project 和 BitBake 手册中都有相同的文档文件名，情况也会一样。

:ref:`bitbake:directory/file:section title`

# Submitting documentation changes

Please see the top level README file in this repository for details of where to send patches.

> 请参阅本存储库中的顶级 README 文件，了解如何发送补丁的详细信息。

---
