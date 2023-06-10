---
tip: translate by openai@2023-06-07 21:14:51
...
---
title: Yocto Project Compatible
---
# Introduction


After the introduction of layers to OpenEmbedded, it quickly became clear that while some layers were popular and worked well, others developed a reputation for being \"problematic\". Those were layers which didn\'t interoperate well with others and tended to assume they controlled all the aspects of the final output. This usually isn\'t intentional but happens because such layers are often created by developers with a particular focus (e.g. a company\'s `BSP<Board Support Package (BSP)>`{.interpreted-text role="term"}) whilst the end users have a different one (e.g. integrating that `BSP<Board Support Package (BSP)>`{.interpreted-text role="term"} into a product).

> 随着OpenEmbedded引入层，很快就发现有些层很受欢迎，而另一些层则因为“有问题”而声名狼藉。这些层不能很好地与其他层协同工作，往往假定它们控制最终输出的所有方面。这通常不是有意为之，而是因为这些层通常是由具有特定关注点（例如公司的板级支持包（BSP））的开发人员创建的，而最终用户则有不同的关注点（例如将该BSP集成到产品中）。


As a result of noticing such patterns and friction between layers, the project developed the \"Yocto Project Compatible\" badge program, allowing layers following the best known practises to be marked as being widely compatible with other ones. This takes the form of a set of \"yes/no\" binary answer questions where layers can declare if they meet the appropriate criteria. In the second version of the program, a script was added to make validation easier and clearer, the script is called `yocto-check-layer` and is available in `OpenEmbedded-Core (OE-Core)`{.interpreted-text role="term"}.

> 结果，为了发现这种模式和层之间的摩擦，该项目开发了“Yocto项目兼容”徽章计划，允许遵循最佳实践的层被标记为与其他层广泛兼容。这里采用一组“是/否”二元答案问题的形式，层可以声明它们是否满足适当的标准。在第二个版本的计划中，增加了一个脚本，以使验证更容易和更清晰，该脚本称为`yocto-check-layer`，并可在`OpenEmbedded-Core（OE-Core）`中找到。


See `dev-manual/layers:making sure your layer is compatible with yocto project`{.interpreted-text role="ref"} for details.

> 请参阅`dev-manual/layers:making sure your layer is compatible with yocto project`以获取详细信息。

# Benefits


`overview-manual/yp-intro:the yocto project layer model`{.interpreted-text role="ref"} is powerful and flexible: it gives users the ultimate power to change pretty much any aspect of the system but as with most things, power comes with responsibility. The Yocto Project would like to see people able to mix and match BSPs with distro configs or software stacks and be able to merge succesfully. Over time, the project identified characteristics in layers that allow them to operate well together. \"anti-patterns\" were also found, preventing layers from working well together.

> Yocto项目层模型强大而灵活：它为用户提供了改变系统几乎任何方面的终极权力，但是像大多数事情一样，权力伴随着责任。Yocto项目希望看到人们能够将BSP与发行版配置或软件堆栈混合和匹配，并能够成功合并。随着时间的推移，该项目确定了允许它们共同运作的层的特征。也发现了“反模式”，阻止层之间正常工作。


The intent of the compatibility program is simple: if the layer passes the compatibility tests, it is considered \"well behaved\" and should operate and cooperate well with other compatible layers.

> 程序兼容性的目的很简单：如果层通过了兼容性测试，它就被认为是“行为良好”的，并且应该能够与其他兼容层很好地运行和协作。


The benefits of compatibility can be seen from multiple different user and member perspectives. From a hardware perspective (a `overview-manual/concepts:bsp layer`{.interpreted-text role="ref"}), compatibility means the hardware can be used in many different products and use cases without impacting the software stacks being run with it. For a company developing a product, compatibility gives you a specification / standard you can require in a contract and then know it will have certain desired characteristics for interoperability. It also puts constraints on how invasive the code bases are into the rest of the system, meaning that multiple different separate hardware support layers can coexist (e.g. for multiple product lines from different hardware manufacturers). This can also make it easier for one or more parties to upgrade those system components for security purposes during the lifecycle of a product.

> 好处的兼容性可以从多个不同的用户和成员角度看到。从硬件角度来看（一个“概览手册/概念：bsp层”），兼容性意味着硬件可以在许多不同的产品和用例中使用而不会影响运行的软件堆栈。对于开发产品的公司来说，兼容性给您提供了一个规范/标准，您可以在合同中要求，然后知道它将具有某些期望的互操作性特征。它还对代码库对系统的其余部分的侵入性施加了限制，这意味着多个不同的单独的硬件支持层可以共存（例如，用于来自不同硬件制造商的多个产品系列）。这也可以使一个或多个当事方在产品的生命周期中更容易升级这些系统组件以提高安全性。

# Validating a layer


The badges are available to members of the Yocto Project (as member benefit) and to open source projects run on a non-commercial basis. However, anyone can answer the questions and run the script.

> 徽章可供Yocto Project成员（作为会员福利）和非商业基础上运行的开源项目使用。但是，任何人都可以回答问题并运行脚本。


The project encourages all layer maintainers to review the questions and the output from the script against their layer, as the way some layers are constructed often has unintended consequences. The questions and the script are designed to highlight known issues which are often easy to solve. This makes layers easier to use and therefore more popular.

> 项目鼓励所有图层维护者审查问题和脚本的输出，因为某些图层的构建方式往往会造成意想不到的后果。问题和脚本旨在突出已知的问题，这些问题通常很容易解决。这使得图层更容易使用，因此更受欢迎。


It is intended that over time, the tests will evolve as new best known practices are identified, and as new interoperability issues are found, unnecessarily restricting layer interoperability. If anyone becomes aware of either type, please let the project know through the :yocto_home:[technical calls \</public-virtual-meetings/\>]{.title-ref}, the :yocto_home:[mailing lists \</community/mailing-lists/\>]{.title-ref} or through the :oe_wiki:[Technical Steering Committee (TSC) \</TSC\>]{.title-ref}. The TSC is responsible for the technical criteria used by the program.

> 预期随着新的最佳实践的被发现，以及新的互操作性问题出现，而不必要地限制了层次的互操作性，随着时间的推移，测试将会演变。如果有人意识到这两种类型，请通过:yocto_home:[技术电话\</public-virtual-meetings/\>]{.title-ref}、:yocto_home:[邮件列表\</community/mailing-lists/\>]{.title-ref}或:oe_wiki:[技术指导委员会（TSC）\</TSC\>]{.title-ref}让项目知道。TSC负责该计划使用的技术标准。


Layers are divided into three types:

> 分层被分为三种类型：


- `"BSP" or "hardware support"<overview-manual/concepts:bsp layer>`{.interpreted-text role="ref"} layers contain support for particular pieces of hardware. This includes kernel and boot loader configuration, and any recipes for firmware or kernel modules needed for the hardware. Such layers usually correspond to a `MACHINE`{.interpreted-text role="term"} setting.

> BSP或硬件支持层包含对特定硬件的支持。这包括内核和引导加载程序的配置，以及为硬件所需的固件或内核模块的配方。这些层通常对应于MACHINE设置。

- `"distro" layers<overview-manual/concepts:distro layer>`{.interpreted-text role="ref"} defined as layers providing configuration options and settings such as the choice of init system, compiler and optimisation options, and configuration and choices of software components. This would usually correspond to a `DISTRO`{.interpreted-text role="term"} setting.

> "发行版"层<overview-manual/concepts:distro layer>{.interpreted-text role="ref"}定义为提供配置选项和设置的层，如初始系统、编译器和优化选项，以及软件组件的配置和选择。这通常对应于"发行版"{.interpreted-text role="term"}设置。

- \"software\" layers are usually recipes. A layer might target a particular graphical UI or software stack component.

> 软件层通常是配方。一个层可能针对特定的图形用户界面或软件堆栈组件。


Here are key best practices the program tries to encourage:

> 这里是程序试图鼓励的关键最佳实践：


- A layer should clearly show who maintains it, and who change submissions and bug reports should be sent to.

> 一层应清楚地表明谁维护它，以及谁应改变提交和缺陷报告应发送到何处。

- Where multiple types of functionality are present, the layer should be internally divided into sublayers to separate these components. That\'s because some users may only need one of them and separability is a key best practice.

> 当存在多种类型的功能时，层应该内部分成子层以将这些组件分开。这是因为一些用户可能只需要其中的一个，而可分离性是一个关键的最佳实践。

- Adding a layer to a build should not modify that build, unless the user changes a configuration setting to activate the layer, by selecting a `MACHINE`{.interpreted-text role="term"}, a `DISTRO`{.interpreted-text role="term"} or a `DISTRO_FEATURES`{.interpreted-text role="term"} setting.

> 添加一个层到构建不应该修改这个构建，除非用户改变配置设置来激活这个层，通过选择`MACHINE`、`DISTRO`或`DISTRO_FEATURES`设置。

- Layers should be documenting where they don't support normal \"core\" functionality such as where debug symbols are disabled or missing, where development headers and on-target library usage may not work or where functionality like the SDK/eSDK would not be expected to work.

> 层应该记录哪些不支持正常的“核心”功能，比如调试符号被禁用或丢失，开发头文件和目标库的使用可能不起作用，或者像SDK/eSDK这样的功能不可能期望起作用。


The project does test the compatibility status of the core project layers on its `Autobuilder </test-manual/understand-autobuilder>`{.interpreted-text role="doc"}.

> 项目在其“自动构建器”<test-manual/understand-autobuilder>上测试核心项目层的兼容性状态。


The official form to submit compatibility requests with is at :yocto_home:[/ecosystem/branding/compatible-registration/]{.title-ref}. Applicants can display the badge they get when their application is successful.

> 申请兼容性要求的官方表格位于：yocto_home：[/ecosystem/branding/compatible-registration/]{.title-ref}。申请人在申请成功后可以显示他们获得的徽章。
