---
tip: translate by openai@2023-06-07 20:49:56
title: What I wish I\'d known about Yocto Project
---
::: note
::: title
Note
:::

Before reading further, make sure you\'ve taken a look at the :yocto_home:[Software Overview\</software-overview\>]{.title-ref} page which presents the definitions for many of the terms referenced here. Also, know that some of the information here won\'t make sense now, but as you start developing, it is the information you\'ll want to keep close at hand. These are best known methods for working with Yocto Project and they are updated regularly.

> 在继续阅读之前，请确保您已经查看了:yocto_home:[软件概述\</software-overview\>]{.title-ref}页面，该页面提供了此处引用的许多术语的定义。此外，请注意，此处的某些信息现在可能无法理解，但是随着您开始开发，它就是您想要保持接近的信息。这些是使用 Yocto 项目的最佳方法，它们会定期更新。

:::

Using the Yocto Project is fairly easy, _until something goes wrong_. Without an understanding of how the build process works, you\'ll find yourself trying to troubleshoot \"a black box\". Here are a few items that new users wished they had known before embarking on their first build with Yocto Project. Feel free to contact us with other suggestions.

> 使用 Yocto Project 相当容易，直到出现问题。如果您不了解构建过程，您将发现自己试图调试“黑盒子”。以下是新用户希望在第一次使用 Yocto Project 构建之前就知道的一些内容。如果您有其他建议，请随时与我们联系。

1. **Use Git, not the tarball download:** If you use git the software will be automatically updated with bug updates because of how git works. If you download the tarball instead, you will need to be responsible for your own updates.

> 使用 Git 而不是 tarball 下载：如果你使用 Git，软件将自动更新错误更新，因为 Git 的工作原理。如果你下载 tarball，你需要自己负责更新。

2. **Get to know the layer index:** All layers can be found in the :oe_layerindex:[layer index \<\>]{.title-ref}. Layers which have applied for Yocto Project Compatible status (structure continuity assurance and testing) can be found in the :yocto_home:[Yocto Project Compatible index \</software-over/layer/\>]{.title-ref}. Generally check the Compatible layer index first, and if you don\'t find the necessary layer check the general layer index. The layer index is an original artifact from the Open Embedded Project. As such, that index doesn\'t have the curating and testing that the Yocto Project provides on Yocto Project Compatible layer list, but the latter has fewer entries. Know that when you start searching in the layer index that not all layers have the same level of maturity, validation, or usability. Nor do searches prioritize displayed results. There is no easy way to help you through the process of choosing the best layer to suit your needs. Consequently, it is often trial and error, checking the mailing lists, or working with other developers through collaboration rooms that can help you make good choices.

> 2. **了解层索引：**所有层都可以在：oe_layerindex：[层索引 \<\>]{.title-ref}中找到。已经申请了 Yocto 项目兼容状态（结构连续性保证和测试）的层可以在：yocto_home：[Yocto 项目兼容索引 \</software-over/layer/\>]{.title-ref}中找到。通常首先检查兼容层索引，如果没有找到必要的层，请检查一般层索引。层索引是来自 Open Embedded 项目的原始工件。因此，该索引没有 Yocto 项目为 Yocto 项目兼容层列表提供的策划和测试，但后者的条目较少。当您开始在层索引中搜索时，请注意不是所有层的成熟度，验证或可用性都是一样的。搜索也不会优先显示结果。没有简单的方法可以帮助您选择最适合您需求的最佳层。因此，通常是试验与错误，检查邮件列表，或者通过协作室与其他开发人员合作，可以帮助您做出良好的选择。

3. **Use existing BSP layers from silicon vendors when possible:** Intel, TI, NXP and others have information on what BSP layers to use with their silicon. These layers have names such as \"meta-intel\" or \"meta-ti\". Try not to build layers from scratch. If you do have custom silicon, use one of these layers as a guide or template and familiarize yourself with the `bsp-guide/index`{.interpreted-text role="doc"}.

> 尽可能使用硅片供应商提供的现有 BSP 层：英特尔、TI、NXP 等都提供有关如何使用其硅片的 BSP 层信息。这些层的名称如“meta-intel”或“meta-ti”。尽量不要从头开始构建层。如果您有自定义硅片，请使用其中一层作为指南或模板，并熟悉 bsp-guide/index。

4. **Do not put everything into one layer:** Use different layers to logically separate information in your build. As an example, you could have a BSP layer, a GUI layer, a distro configuration, middleware, or an application (e.g. \"meta-filesystems\", \"meta-python\", \"meta-intel\", and so forth). Putting your entire build into one layer limits and complicates future customization and reuse. Isolating information into layers, on the other hand, helps keep simplify future customizations and reuse.

> 不要把所有东西放在一个层面：使用不同的层面来逻辑上分离您的构建中的信息。例如，您可以有一个 BSP 层，一个 GUI 层，一个发行版配置，中间件或应用程序（例如“meta-filesystems”，“meta-python”，“meta-intel”等）。将您的整个构建放在一个层面会限制并且使未来的定制和重用变得复杂。另一方面，将信息隔离到层次中有助于简化未来的定制和重用。

5. **Never modify the POKY layer. Never. Ever. When you update to the next release, you\'ll lose all of your work. ALL OF IT.**

> 5. **永远不要修改 POKY 层。永远不要。当您更新到下一个版本时，您将会失去所有的工作。所有的工作。**

6. **Don\'t be fooled by documentation searching results:** Yocto Project documentation is always being updated. Unfortunately, when you use Google to search for Yocto Project concepts or terms, Google consistently searches and retrieves older versions of Yocto Project manuals. For example, searching for a particular topic using Google could result in a \"hit\" on a Yocto Project manual that is several releases old. To be sure that you are using the most current Yocto Project documentation, use the drop-down menu at the top of any of its page.

> 不要被文档搜索结果所迷惑：Yocto 项目文档总是在更新。不幸的是，当您使用 Google 搜索 Yocto 项目概念或术语时，Google 总是搜索并检索旧版本的 Yocto 项目手册。例如，使用 Google 搜索特定主题可能会导致几个发行版本的 Yocto 项目手册中出现“命中”。为了确保您正在使用最新的 Yocto 项目文档，请使用页面顶部的下拉菜单。

Many developers look through the :yocto_docs:[All-in-one \'Mega\' Manual \</singleindex.html\>]{.title-ref} for a concept or term by doing a search through the whole page. This manual is a concatenation of the core set of Yocto Project manual. Thus, a simple string search using Ctrl-F in this manual produces all the \"hits\" for a desired term or concept. Once you find the area in which you are interested, you can display the actual manual, if desired. It is also possible to use the search bar in the menu or in the left navigation pane.

> 许多开发者会通过搜索整个页面来查找 Yocto 文档中的概念或术语，这些文档被称为“全能的 Mega 手册”。因此，使用 Ctrl-F 在此手册中进行简单的字符串搜索可以获得所需术语或概念的“命中”。一旦你找到你感兴趣的区域，你可以根据需要显示实际的手册。也可以使用菜单中或左侧导航栏中的搜索栏。

7. **Understand the basic concepts of how the build system works: the workflow:** Understanding the Yocto Project workflow is important as it can help you both pinpoint where trouble is occurring and how the build is breaking. The workflow breaks down into the following steps:

> 了解 Yocto 项目的工作流程很重要，因为它可以帮助您确定问题出现的位置以及构建出现了什么问题。工作流程可以分为以下几个步骤：

1. Fetch -- get the source code

> 1. 抓取--获取源代码

2. Extract -- unpack the sources

> 提取--解压源文件

3. Patch -- apply patches for bug fixes and new capability

> 3. 修补程序 -- 用于修复错误和添加新功能的补丁

4. Configure -- set up your environment specifications

> 4.配置--设置您的环境规格

5. Build -- compile and link

> 5. 构建--编译和链接

6. Install -- copy files to target directories

> 6. 安装 -- 将文件复制到目标目录

7. Package -- bundle files for installation

> 7. 安装包 -- 用于安装的文件打包

During \"fetch\", there may be an inability to find code. During \"extract\", there is likely an invalid zip or something similar. In other words, the function of a particular part of the workflow gives you an idea of what might be going wrong.

> 在“获取”期间，可能无法找到代码。在“提取”期间，可能是无效的 zip 或类似内容。换句话说，工作流程的某个部分的功能可以让您了解可能出了什么问题。

![image](figures/yp-how-it-works-new-diagram.png){width="100.0%"}

8. **Know that you can generate a dependency graph and learn how to do it:** A dependency graph shows dependencies between recipes, tasks, and targets. You can use the \"-g\" option with BitBake to generate this graph. When you start a build and the build breaks, you could see packages you have no clue about or have any idea why the build system has included them. The dependency graph can clarify that confusion. You can learn more about dependency graphs and how to generate them in the `bitbake-user-manual/bitbake-user-manual-intro:generating dependency graphs`{.interpreted-text role="ref"} section in the BitBake User Manual.

> 知道你可以生成依赖图，并学习如何做：依赖图显示菜谱、任务和目标之间的依赖关系。您可以使用 BitBake 的“-g”选项来生成此图。当您启动构建并构建失败时，您可能会看到您不知道的软件包或不知道构建系统为什么包括它们。依赖图可以澄清这种困惑。您可以在 BitBake 用户手册中的“bitbake-user-manual / bitbake-user-manual-intro：生成依赖图”部分中了解有关依赖图及如何生成它们的更多信息。

9. **Here\'s how you decode \"magic\" folder names in tmp/work:** The build system fetches, unpacks, preprocesses, and builds. If something goes wrong, the build system reports to you directly the path to a folder where the temporary (build/tmp) files and packages reside resulting from the build. For a detailed example of this process, see the :yocto_wiki:[example \</Cookbook:Example:Adding_packages_to_your_OS_image\>]{.title-ref}. Unfortunately this example is on an earlier release of Yocto Project.

> 在 tmp/work 中如何解码“魔法”文件夹名称：构建系统会获取、解包、预处理和构建。如果出现问题，构建系统会直接向您报告临时（build/tmp）文件和包的路径，这些文件和包是构建过程中产生的。要了解详细的示例，请参见：yocto_wiki:[example \</Cookbook:Example:Adding_packages_to_your_OS_image\>]{.title-ref}。不幸的是，此示例是基于 Yocto Project 的早期版本。

When you perform a build, you can use the \"-u\" BitBake command-line option to specify a user interface viewer into the dependency graph (e.g. knotty, ncurses, or taskexp) that helps you understand the build dependencies better.

> 当您执行构建时，您可以使用“-u” BitBake 命令行选项来指定用户界面查看器到依赖关系图（例如 knotty，ncurses 或 taskexp），以帮助您更好地理解构建依赖关系。

10. **You can build more than just images:** You can build and run a specific task for a specific package (including devshell) or even a single recipe. When developers first start using the Yocto Project, the instructions found in the `brief-yoctoprojectqs/index`{.interpreted-text role="doc"} show how to create an image and then run or flash that image. However, you can actually build just a single recipe. Thus, if some dependency or recipe isn\'t working, you can just say \"bitbake foo\" where \"foo\" is the name for a specific recipe. As you become more advanced using the Yocto Project, and if builds are failing, it can be useful to make sure the fetch itself works as desired. Here are some valuable links: `dev-manual/development-shell:Using a Development Shell`{.interpreted-text role="ref"} for information on how to build and run a specific task using devshell. Also, the ``SDK manual shows how to build out a specific recipe <sdk-manual/extensible:use \`\`devtool modify\`\` to modify the source of an existing component>``{.interpreted-text role="ref"}.

> 10. **您可以建立的不只是图像：** 您可以为特定的包（包括 devshell）或单个配方构建和运行特定的任务。当开发人员第一次开始使用 Yocto 项目时，在 `brief-yoctoprojectqs/index`{.interpreted-text role="doc"}中找到的说明将指导您如何创建图像，然后运行或刷新该图像。但是，您实际上只能构建单个配方。因此，如果某些依赖关系或配方不起作用，您只需说“bitbake foo”，其中“foo”是特定配方的名称。随着您对 Yocto 项目的使用变得更加熟练，如果构建失败，确保自身可以按照预期运行是非常有用的。以下是一些有价值的链接： `dev-manual/development-shell:Using a Development Shell`{.interpreted-text role="ref"}，其中包含有关如何使用 devshell 构建和运行特定任务的信息。此外，``SDK手册显示如何构建特定的配方<sdk-manual/extensible:use \`\`devtool modify\`\` to modify the source of an existing component>``{.interpreted-text role="ref"}。

11. **An ambiguous definition: Package vs Recipe:** A recipe contains instructions the build system uses to create packages. Recipes and Packages are the difference between the front end and the result of the build process.

> 包与配方的定义：配方包含构建系统使用的指令，用于创建包。配方和包是前端和构建过程结果之间的差异。

```
As mentioned, the build system takes the recipe and creates packages from the recipe\'s instructions. The resulting packages are related to the one thing the recipe is building but are different parts (packages) of the build (i.e. the main package, the doc package, the debug symbols package, the separate utilities package, and so forth). The build system splits out the packages so that you don\'t need to install the packages you don\'t want or need, which is advantageous because you are building for small devices when developing for embedded and IoT.
```

12. **You will want to learn about and know what\'s packaged in the root filesystem.**

> 你需要了解根文件系统中打包的内容并且知道它们是什么。

13. **Create your own image recipe:** There are a number of ways to create your own image recipe. We suggest you create your own image recipe as opposed to appending an existing recipe. It is trivial and easy to write an image recipe. Again, do not try appending to an existing image recipe. Create your own and do it right from the start.

> 创建自己的图像配方有很多方法。我们建议您创建自己的图像配方，而不是附加现有的配方。编写图像配方很简单容易。再次提醒，不要尝试附加到现有的图像配方。从一开始就自己创建，并做得恰当。

14. **Finally, here is a list of the basic skills you will need as a systems developer. You must be able to:**

> 14. 最后，这里是系统开发者所需的基本技能列表。您必须能够：

```
- deal with corporate proxies
- add a package to an image
- understand the difference between a recipe and package
- build a package by itself and why that\'s useful
- find out what packages are created by a recipe
- find out what files are in a package
- find out what files are in an image
- add an ssh server to an image (enable transferring of files to target)
- know the anatomy of a recipe
- know how to create and use layers
- find recipes (with the :oe_layerindex:[OpenEmbedded Layer index \<\>]{.title-ref})
- understand difference between machine and distro settings
- find and use the right BSP (machine) for your hardware
- find examples of distro features and know where to set them
- understanding the task pipeline and executing individual tasks
- understand devtool and how it simplifies your workflow
- improve build speeds with shared downloads and shared state cache
- generate and understand a dependency graph
- generate and understand BitBake environment
- build an Extensible SDK for applications development
```

15. **Depending on what you primary interests are with the Yocto Project, you could consider any of the following reading:**

> 根据您对 Yocto 项目的主要兴趣，您可以考虑以下阅读：

```
- **Look Through the Yocto Project Development Tasks Manual**: This manual contains procedural information grouped to help you get set up, work with layers, customize images, write new recipes, work with libraries, and use QEMU. The information is task-based and spans the breadth of the Yocto Project. See the `/dev-manual/index`{.interpreted-text role="doc"}.
- **Look Through the Yocto Project Application Development and the Extensible Software Development Kit (eSDK) manual**: This manual describes how to use both the standard SDK and the extensible SDK, which are used primarily for application development. The `/sdk-manual/extensible`{.interpreted-text role="doc"} also provides example workflows that use devtool. See the section `` sdk-manual/extensible:using \`\`devtool\`\` in your sdk workflow ``{.interpreted-text role="ref"} for more information.
- **Learn About Kernel Development**: If you want to see how to work with the kernel and understand Yocto Linux kernels, see the `/kernel-dev/index`{.interpreted-text role="doc"}. This manual provides information on how to patch the kernel, modify kernel recipes, and configure the kernel.
- **Learn About Board Support Packages (BSPs)**: If you want to learn about BSPs, see the `/bsp-guide/index`{.interpreted-text role="doc"}. This manual also provides an example BSP creation workflow. See the `/bsp-guide/bsp`{.interpreted-text role="doc"} section.
- **Learn About Toaster**: Toaster is a web interface to the Yocto Project\'s OpenEmbedded build system. If you are interested in using this type of interface to create images, see the `/toaster-manual/index`{.interpreted-text role="doc"}.
- **Have Available the Yocto Project Reference Manual**: Unlike the rest of the Yocto Project manual set, this manual is comprised of material suited for reference rather than procedures. You can get build details, a closer look at how the pieces of the Yocto Project development environment work together, information on various technical details, guidance on migrating to a newer Yocto Project release, reference material on the directory structure, classes, and tasks. The `/ref-manual/index`{.interpreted-text role="doc"} also contains a fairly comprehensive glossary of variables used within the Yocto Project.
```
