---
tip: translate by openai@2023-06-07 20:44:08
title: Transitioning to a custom environment for systems development
---
::: note
::: title
Note
:::

So you\'ve finished the `brief-yoctoprojectqs/index`, the latter contains important information learned from other users. You\'re well prepared. But now, as you are starting your own project, it isn\'t exactly straightforward what to do. And, the documentation is daunting. We\'ve put together a few hints to get you started.

> 你已经完成了 `brief-yoctoprojectqs/index` 文档，后者包含了从其他用户获得的重要信息。你已经准备好了。但是，现在你开始自己的项目时，不太清楚该做什么。而且，文档令人生畏。我们已经汇总了一些提示，让你入门。
> :::

1. **Make a list of the processor, target board, technologies, and capabilities that will be part of your project**. You will be finding layers with recipes and other metadata that support these things, and adding them to your configuration. (See #3)

> 1. 列出将成为您项目一部分的处理器、目标板、技术和功能。您将找到支持这些内容的具有配方和其他元数据的层，并将它们添加到您的配置中。(参见＃3)

2. **Set up your board support**. Even if you\'re using custom hardware, it might be easier to start with an existing target board that uses the same processor or at least the same architecture as your custom hardware. Knowing the board already has a functioning Board Support Package (BSP) within the project makes it easier for you to get comfortable with project concepts.

> 设置你的板支持。即使你使用的是定制硬件，也可能更容易从使用相同处理器或至少相同架构的现有目标板开始。知道板子已经有一个在项目中正常运行的板支持包(BSP)可以让你更容易掌握项目概念。

3. **Find and acquire the best BSP for your target**. Use the :yocto_home:[Yocto Project Compatible Layers \</software-overview/layers/\>] to find and acquire the best BSP for your target board. The Yocto Project layer index BSPs are regularly validated. The best place to get your first BSP is from your silicon manufacturer or board vendor -- they can point you to their most qualified efforts. In general, for Intel silicon use meta-intel, for Texas Instruments use meta-ti, and so forth. Choose a BSP that has been tested with the same Yocto Project release that you\'ve downloaded. Be aware that some BSPs may not be immediately supported on the very latest release, but they will be eventually.

> 找到并获取最佳 BSP(基本系统平台)。使用:yocto_home:[Yocto Project 兼容层\</software-overview/layers/\>]来找到并获取最佳的目标板 BSP。Yocto Project 层索引 BSP 经常会被验证。获取第一个 BSP 的最佳位置是从你的硅片制造商或者板子供应商那里——他们可以指导你获取他们最有资质的 BSP。一般来说，对于 Intel 的硅片使用 meta-intel，对于 Texas Instruments 使用 meta-ti，等等。选择一个与你下载的 Yocto Project 发行版本一起测试过的 BSP。注意有些 BSP 可能暂时不支持最新的发行版本，但是最终会支持。

You might want to start with the build specification that Poky provides (which is reference embedded distribution) and then add your newly chosen layers to that. Here is the information `about adding layers <dev-manual/layers:Understanding and Creating Layers>`.

> 如果您想使用 ROS2 项目，您可以先使用 Poky 提供的构建规范(参考嵌入式发行版)，然后将您选择的新图层添加到此规范中。关于添加图层的信息，请参阅 <dev-manual/layers:Understanding and Creating Layers>。

4. **Based on the layers you\'ve chosen, make needed changes in your configuration**. For instance, you\'ve chosen a machine type and added in the corresponding BSP layer. You\'ll then need to change the value of the `MACHINE` variable in your configuration file (build/local.conf) to point to that same machine type. There could be other layer-specific settings you need to change as well. Each layer has a `README` document that you can look at for this type of usage information.

> 根据您选择的层，请在您的配置中做出必要的更改。例如，您已经选择了机器类型并添加了相应的 BSP 层。然后，您需要将配置文件(build/local.conf)中的 `MACHINE` 变量的值更改为指向同一类型的机器。您可能还需要更改其他与层相关的设置。每个层都有一个 `README` 文档，您可以查看此类用法信息。

5. **Add a new layer for any custom recipes and metadata you create**. Use the `bitbake-layers create-layer` tool for Yocto Project 2.4+ releases. If you are using a Yocto Project release earlier than 2.4, use the `yocto-layer create` tool. The `bitbake-layers` tool also provides a number of other useful layer-related commands. See ``dev-manual/layers:creating a general layer using the \`\`bitbake-layers\`\` script`` section.

> 添加一个新的层来存储你创建的任何自定义 recipes 和元数据。使用 `bitbake-layers create-layer` 工具，适用于 Yocto Project 2.4+ 版本。如果你正在使用的 Yocto Project 版本低于 2.4，请使用 `yocto-layer create` 工具。`bitbake-layers` 工具还提供了许多其他有用的图层相关命令。请参阅 ``dev-manual/layers:creating a general layer using the \`\`bitbake-layers\`\` script`` 部分。

6. **Create your own layer for the BSP you\'re going to use**. It is not common that you would need to create an entire BSP from scratch unless you have a _really_ special device. Even if you are using an existing BSP, ``create your own layer for the BSP <bsp-guide/bsp:creating a new bsp layer using the \`\`bitbake-layers\`\` script>``. For example, given a 64-bit x86-based machine, copy the conf/intel-corei7-64 definition and give the machine a relevant name (think board name, not product name). Make sure the layer configuration is dependent on the meta-intel layer (or at least, meta-intel remains in your bblayers.conf). Now you can put your custom BSP settings into your layer and you can re-use it for different applications.

> 创建你自己的层，用于你要使用的 BSP。除非你有一个*真正*特殊的设备，否则不太可能需要从头开始创建一个完整的 BSP。即使你正在使用现有的 BSP，也可以使用 `bitbake-layers` 脚本 `<bsp-guide/bsp:creating a new bsp layer using the \`\`bitbake-layers\`\` script>` 创建你自己的层。例如，给定一台基于 64 位 x86 的机器，复制 conf/intel-corei7-64 定义，并给机器起一个相关的名字(考虑板名，而不是产品名称)。确保层配置依赖于 meta-intel 层(或者至少保留 meta-intel 在你的 bblayers.conf 中)。现在你可以将你的自定义 BSP 设置放入你的层中，并且可以用于不同的应用。

7. **Write your own recipe to build additional software support that isn\'t already available in the form of a recipe**. Creating your own recipe is especially important for custom application software that you want to run on your device. Writing new recipes is a process of refinement. Start by getting each step of the build process working beginning with fetching all the way through packaging. Next, run the software on your target and refine further as needed. See `Writing a New Recipe <dev-manual/new-recipe:writing a new recipe>` in the Yocto Project Development Tasks Manual for more information.

> 写自己的配方来构建尚未以配方形式提供的附加软件支持是非常重要的。创建自己的配方是一个改进的过程。首先，从获取开始，运行构建过程的每一步，最后打包。接下来，在目标上运行软件，并根据需要进一步改进。有关更多信息，请参阅 Yocto 项目开发任务手册中的“编写新配方”。

8. **Now you\'re ready to create an image recipe**. There are a number of ways to do this. However, it is strongly recommended that you have your own image recipe \-\-- don\'t try appending to existing image recipes. Recipes for images are trivial to create and you usually want to fully customize their contents.

> 现在您准备创建一个映像配方了。有很多方法可以做到这一点。但是，强烈建议您有自己的映像配方 \-\-- 不要尝试附加到现有的映像配方。创建映像的配方很简单，通常您需要完全自定义其内容。

9. **Build your image and refine it**. Add what\'s missing and fix anything that\'s broken using your knowledge of the ``workflow <sdk-manual/extensible:using \`\`devtool\`\` in your sdk workflow>`` to identify where issues might be occurring.

> 构建你的镜像并优化它。使用您对 `workflow <sdk-manual/extensible:using \`\`devtool\`\` in your sdk workflow>` 的了解，添加缺失的内容并修复任何出现故障的内容，以确定可能发生问题的位置。

10. **Consider creating your own distribution**. When you get to a certain level of customization, consider creating your own distribution rather than using the default reference distribution.

> 考虑创建自己的发行版。当您达到一定程度的定制时，考虑创建自己的发行版，而不是使用默认的参考发行版。

```
Distribution settings define the packaging back-end (e.g. rpm or other) as well as the package feed and possibly the update solution. You would create your own distribution in a new layer inheriting from Poky but overriding what needs to change for your distribution. If you find yourself adding a lot of configuration to your local.conf file aside from paths and other typical local settings, it\'s time to `consider creating your own distribution <dev-manual/custom-distribution:creating your own distribution>`.

You can add product specifications that can customize the distribution if needed in other layers. You can also add other functionality specific to the product. But to update the distribution, not individual products, you update the distribution feature through that layer.
```

11. **Congratulations! You\'re well on your way.** Welcome to the Yocto Project community.

> 恭喜！你已经走到了一半。欢迎加入 Yocto Project 社区。
