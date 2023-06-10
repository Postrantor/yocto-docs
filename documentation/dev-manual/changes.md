---
tip: translate by baidu@2023-06-07 17:09:37
...
---
title: Making Changes to the Yocto Project
------------------------------------------

Because the Yocto Project is an open-source, community-based project, you can effect changes to the project. This section presents procedures that show you how to submit a defect against the project and how to submit a change.

> 因为 Yocto 项目是一个开源的、基于社区的项目，所以您可以对项目进行更改。本节介绍了向您展示如何针对项目提交缺陷以及如何提交更改的过程。

# Submitting a Defect Against the Yocto Project

Use the Yocto Project implementation of [Bugzilla](https://www.bugzilla.org/about/) to submit a defect (bug) against the Yocto Project. For additional information on this implementation of Bugzilla see the \"`Yocto Project Bugzilla <resources-bugtracker>`{.interpreted-text role="ref"}\" section in the Yocto Project Reference Manual. For more detail on any of the following steps, see the Yocto Project :yocto_wiki:[Bugzilla wiki page \</Bugzilla_Configuration_and_Bug_Tracking\>]{.title-ref}.

> 使用[Bugzilla]的 Yocto 项目实施([https://www.bugzilla.org/about/](https://www.bugzilla.org/about/))提交针对 Yocto 项目的缺陷（bug）。有关 Bugzilla 实现的更多信息，请参阅《Yocto 项目参考手册》中的\“`Yocto Project Bugzilla＜resources bugtracker＞`{.depreted text role=”ref“}\”一节。有关以下任何步骤的更多详细信息，请参阅 Yocto 项目：Yocto_wiki:[Bugzilla wiki page\</Bugzilla _Configuration_and_Bug_Track\>]｛.title-ref｝。

Use the following general steps to submit a bug:

> 使用以下常规步骤提交错误：

1. Open the Yocto Project implementation of :yocto_bugs:[Bugzilla \<\>]{.title-ref}.

> 1.打开 Yocto 项目实现：Yocto_bugs:[Bugzilla \<\>]｛.title-ref｝。

2. Click \"File a Bug\" to enter a new bug.

> 2.单击“提交 Bug”以输入新的 Bug。

3. Choose the appropriate \"Classification\", \"Product\", and \"Component\" for which the bug was found. Bugs for the Yocto Project fall into one of several classifications, which in turn break down into several products and components. For example, for a bug against the `meta-intel` layer, you would choose \"Build System, Metadata & Runtime\", \"BSPs\", and \"bsps-meta-intel\", respectively.

> 3.选择发现错误的“分类”、“产品”和“组件”。Yocto 项目的 Bug 分为几个类别之一，这些类别又分为几个产品和组件。例如，对于“meta intel”层的错误，您可以分别选择“Build System，Metadata&Runtime”、“BSPs”和“BSPs meta intel \”。

4. Choose the \"Version\" of the Yocto Project for which you found the bug (e.g. &DISTRO;).

> 4.选择您发现错误的 Yocto 项目的“版本”（例如&DISTRO；）。

5. Determine and select the \"Severity\" of the bug. The severity indicates how the bug impacted your work.

> 5.确定并选择错误的“严重性”。严重性表示错误如何影响您的工作。

6. Choose the \"Hardware\" that the bug impacts.

> 6.选择错误影响的“硬件”。

7. Choose the \"Architecture\" that the bug impacts.

> 7.选择 bug 影响的“体系结构”。

8. Choose a \"Documentation change\" item for the bug. Fixing a bug might or might not affect the Yocto Project documentation. If you are unsure of the impact to the documentation, select \"Don\'t Know\".

> 8.为错误选择“文档更改”项。修复错误可能会也可能不会影响 Yocto 项目文档。如果您不确定对文档的影响，请选择“不知道”。

9. Provide a brief \"Summary\" of the bug. Try to limit your summary to just a line or two and be sure to capture the essence of the bug.

> 9.提供错误的简要“摘要”。尽量将你的摘要限制在一两行，并确保捕捉到错误的本质。

10. Provide a detailed \"Description\" of the bug. You should provide as much detail as you can about the context, behavior, output, and so forth that surrounds the bug. You can even attach supporting files for output from logs by using the \"Add an attachment\" button.

> 10.提供错误的详细“说明”。您应该尽可能多地提供有关 bug 周围的上下文、行为、输出等的详细信息。您甚至可以使用“添加附件”按钮为日志输出附加支持文件。

11. Click the \"Submit Bug\" button submit the bug. A new Bugzilla number is assigned to the bug and the defect is logged in the bug tracking system.

> 11.单击“提交 Bug”按钮提交 Bug。一个新的 Bugzilla 编号被分配给 bug，缺陷被记录在 bug 跟踪系统中。

Once you file a bug, the bug is processed by the Yocto Project Bug Triage Team and further details concerning the bug are assigned (e.g. priority and owner). You are the \"Submitter\" of the bug and any further categorization, progress, or comments on the bug result in Bugzilla sending you an automated email concerning the particular change or progress to the bug.

> 一旦你提交了一个 bug，Yocto 项目 bug 分类团队就会处理这个 bug，并分配关于这个 bug 的更多细节（例如优先级和所有者）。您是该漏洞的“提交者”，对该漏洞的任何进一步分类、进展或评论都会导致 Bugzilla 自动向您发送一封关于该漏洞的特定更改或进展的电子邮件。

# Submitting a Change to the Yocto Project

Contributions to the Yocto Project and OpenEmbedded are very welcome. Because the system is extremely configurable and flexible, we recognize that developers will want to extend, configure or optimize it for their specific uses.

> 非常欢迎对 Yocto 项目和 OpenEmbedded 的贡献。由于该系统具有极强的可配置性和灵活性，我们认识到开发人员会希望针对其特定用途对其进行扩展、配置或优化。

The Yocto Project uses a mailing list and a patch-based workflow that is similar to the Linux kernel but contains important differences. In general, there is a mailing list through which you can submit patches. You should send patches to the appropriate mailing list so that they can be reviewed and merged by the appropriate maintainer. The specific mailing list you need to use depends on the location of the code you are changing. Each component (e.g. layer) should have a `README` file that indicates where to send the changes and which process to follow.

> Yocto 项目使用了一个邮件列表和一个基于补丁的工作流程，该工作流程类似于 Linux 内核，但包含了重要的差异。一般来说，有一个邮件列表，您可以通过该列表提交修补程序。您应该将修补程序发送到相应的邮件列表，以便相应的维护人员可以对其进行审查和合并。您需要使用的特定邮件列表取决于您正在更改的代码的位置。每个组件（例如层）都应该有一个“自述”文件，该文件指示将更改发送到何处以及遵循哪个过程。

You can send the patch to the mailing list using whichever approach you feel comfortable with to generate the patch. Once sent, the patch is usually reviewed by the community at large. If somebody has concerns with the patch, they will usually voice their concern over the mailing list. If a patch does not receive any negative reviews, the maintainer of the affected layer typically takes the patch, tests it, and then based on successful testing, merges the patch.

> 您可以使用任何您觉得合适的方法将补丁发送到邮件列表中以生成补丁。补丁一经发送，通常会由整个社区进行审查。如果有人对补丁有顾虑，他们通常会通过邮件列表表达他们的担忧。如果补丁没有收到任何负面评价，受影响层的维护者通常会接受补丁，对其进行测试，然后在成功测试的基础上合并补丁。

The \"poky\" repository, which is the Yocto Project\'s reference build environment, is a hybrid repository that contains several individual pieces (e.g. BitBake, Metadata, documentation, and so forth) built using the combo-layer tool. The upstream location used for submitting changes varies by component:

> “poky”存储库是 Yocto 项目的参考构建环境，是一个混合存储库，包含使用组合层工具构建的几个单独部分（例如 BitBake、元数据、文档等）。用于提交更改的上游位置因组件而异：

- *Core Metadata:* Send your patch to the :oe_lists:[openembedded-core \</g/openembedded-core\>]{.title-ref} mailing list. For example, a change to anything under the `meta` or `scripts` directories should be sent to this mailing list.

> -*核心元数据：*将您的补丁发送到：oe_lists:[openembedded Core\</g/openembedded-Core\>]｛.title-ref｝邮件列表。例如，对“meta”或“scripts”目录下的任何内容的更改都应发送到此邮件列表。

- *BitBake:* For changes to BitBake (i.e. anything under the `bitbake` directory), send your patch to the :oe_lists:[bitbake-devel \</g/bitbake-devel\>]{.title-ref} mailing list.

> -*BitBake:*对于 BitBake 的更改（即“BitBake”目录下的任何内容），请将您的补丁发送到：oe_lists:[BitBake-devel\</g/BitBake-dvel\>]｛.title-ref｝邮件列表。

- *\"meta-\*\" trees:* These trees contain Metadata. Use the :yocto_lists:[poky \</g/poky\>]{.title-ref} mailing list.

> -*\“meta-\*\”树：*这些树包含元数据。使用：yocto_lists:[poky\</g/poky\>]｛.title-ref｝邮件列表。

- *Documentation*: For changes to the Yocto Project documentation, use the :yocto_lists:[docs \</g/docs\>]{.title-ref} mailing list.

> -*文档*：对于 Yocto 项目文档的更改，请使用：Yocto_lists:[docs\</g/docs\>]｛.title-ref｝邮件列表。

For changes to other layers hosted in the Yocto Project source repositories (i.e. `yoctoproject.org`) and tools use the :yocto_lists:[Yocto Project \</g/yocto/\>]{.title-ref} general mailing list.

> 对于 Yocto Project 源存储库（即“yoctoproject.org”）和工具中托管的其他层的更改，请使用：Yocto_lists:[Yocto Project\</g/Yocto/\>]{.title-ref}常规邮件列表。

::: note
::: title
Note
:::

Sometimes a layer\'s documentation specifies to use a particular mailing list. If so, use that list.

> 有时一个层的文档指定使用一个特定的邮件列表。如果是，请使用该列表。
> :::

For additional recipes that do not fit into the core Metadata, you should determine which layer the recipe should go into and submit the change in the manner recommended by the documentation (e.g. the `README` file) supplied with the layer. If in doubt, please ask on the Yocto general mailing list or on the openembedded-devel mailing list.

> 对于不适合核心元数据的其他配方，您应该确定配方应该进入哪个层，并按照该层提供的文档（例如“自述”文件）建议的方式提交更改。如果有疑问，请在 Yocto 普通邮件列表或 openembedded-devel 邮件列表中询问。

You can also push a change upstream and request a maintainer to pull the change into the component\'s upstream repository. You do this by pushing to a contribution repository that is upstream. See the \"`overview-manual/development-environment:git workflows and the yocto project`{.interpreted-text role="ref"}\" section in the Yocto Project Overview and Concepts Manual for additional concepts on working in the Yocto Project development environment.

> 您还可以向上游推送更改，并请求维护人员将更改拉入组件的上游存储库。您可以通过推送到上游的贡献存储库来实现这一点。有关在 yocto 项目开发环境中工作的其他概念，请参阅 yocto project overview and Concepts manual 中的\“`overview manual/development environment:git workflows and the yocto project`｛.explored text role=”ref“｝\”部分。

Maintainers commonly use `-next` branches to test submissions prior to merging patches. Thus, you can get an idea of the status of a patch based on whether the patch has been merged into one of these branches. The commonly used testing branches for OpenEmbedded-Core are as follows:

> 维护人员通常使用“-next”分支在合并修补程序之前测试提交的内容。因此，您可以根据修补程序是否已合并到其中一个分支中来了解修补程序的状态。OpenEmbedded Core 常用的测试分支如下：

- *openembedded-core \"master-next\" branch:* This branch is part of the :oe\_[git:%60openembedded-core](git:%60openembedded-core) \</openembedded-core/\>\` repository and contains proposed changes to the core metadata.

> -*openembedded core“master next”分支：*此分支是：oe \_[git:%60openembeded core]（git:%60open embedded core）\</openembedded-core/\>\`存储库的一部分，并包含对核心元数据的拟议更改。

- *poky \"master-next\" branch:* This branch is part of the :yocto\_[git:%60poky](git:%60poky) \</poky/\>\` repository and combines proposed changes to BitBake, the core metadata and the poky distro.

> -*poky“master next”分支：*该分支是：yocto\_[git:%60poky]（git:%60poky）\</poky/\>\`存储库的一部分，并结合了对 BitBake、核心元数据和 poky 发行版的拟议更改。

Similarly, stable branches maintained by the project may have corresponding `-next` branches which collect proposed changes. For example, `&DISTRO_NAME_NO_CAP;-next` and `&DISTRO_NAME_NO_CAP_MINUS_ONE;-next` branches in both the \"openembdedded-core\" and \"poky\" repositories.

> 同样，项目维护的稳定分支可能有相应的“下一个”分支，用于收集建议的更改。例如，`&DISTRO_NAME_NO_CAP-下一个` 和 `&DISTRO_NAME_NO_CAP_MINUS_ONE-next` 分支在“openembdeded core”和“poky”存储库中。

Other layers may have similar testing branches but there is no formal requirement or standard for these so please check the documentation for the layers you are contributing to.

> 其他层可能有类似的测试分支，但没有针对这些分支的正式要求或标准，因此请查看您参与的层的文档。

The following sections provide procedures for submitting a change.

> 以下各节提供了提交变更的程序。

## Preparing Changes for Submission

1. *Make Your Changes Locally:* Make your changes in your local Git repository. You should make small, controlled, isolated changes. Keeping changes small and isolated aids review, makes merging/rebasing easier and keeps the change history clean should anyone need to refer to it in future.

> 1.*在本地进行更改：*在本地 Git 存储库中进行更改。您应该进行小的、可控的、孤立的更改。保持小规模和孤立的更改有助于审查，使合并/重新建立基础更容易，并在将来有人需要参考时保持更改历史记录的清洁。

2. *Stage Your Changes:* Stage your changes by using the `git add` command on each file you changed.

> 2.*暂存您的更改：*在您更改的每个文件上使用“git-add”命令来暂存更改。

3. *Commit Your Changes:* Commit the change by using the `git commit` command. Make sure your commit information follows standards by following these accepted conventions:

> 3.*提交更改：*使用“git-Commit”命令提交更改。通过遵循以下公认的约定，确保您的提交信息符合标准：

- Be sure to include a \"Signed-off-by:\" line in the same style as required by the Linux kernel. This can be done by using the `git commit -s` command. Adding this line signifies that you, the submitter, have agreed to the Developer\'s Certificate of Origin 1.1 as follows:

> -请确保以 Linux 内核所需的相同样式包含\“Signed off by:\”行。这可以通过使用“git commit-s”命令来完成。添加这一行表示您，提交人，已同意开发商原产地证书 1.1，如下所示：

```
 ```none
 Developer's Certificate of Origin 1.1

 By making a contribution to this project, I certify that:

 (a) The contribution was created in whole or in part by me and I
     have the right to submit it under the open source license
     indicated in the file; or

 (b) The contribution is based upon previous work that, to the best
     of my knowledge, is covered under an appropriate open source
     license and I have the right under that license to submit that
     work with modifications, whether created in whole or in part
     by me, under the same open source license (unless I am
     permitted to submit under a different license), as indicated
     in the file; or

 (c) The contribution was provided directly to me by some other
     person who certified (a), (b) or (c) and I have not modified
     it.

 (d) I understand and agree that this project and the contribution
     are public and that a record of the contribution (including all
     personal information I submit with it, including my sign-off) is
     maintained indefinitely and may be redistributed consistent with
     this project or the open source license(s) involved.
```

```

- Provide a single-line summary of the change and, if more explanation is needed, provide more detail in the body of the commit. This summary is typically viewable in the \"shortlist\" of changes. Thus, providing something short and descriptive that gives the reader a summary of the change is useful when viewing a list of many commits. You should prefix this short description with the recipe name (if changing a recipe), or else with the short form path to the file being changed.

> -提供更改的单行摘要，如果需要更多解释，请在提交正文中提供更多细节。此摘要通常可在更改的“短名单”中查看。因此，当查看许多提交的列表时，提供一些简短而描述性的内容，让读者对更改进行总结是非常有用的。您应该在这个简短的描述前面加上配方名称（如果正在更改配方），或者加上要更改的文件的简短路径。

- For the body of the commit message, provide detailed information that describes what you changed, why you made the change, and the approach you used. It might also be helpful if you mention how you tested the change. Provide as much detail as you can in the body of the commit message.

> -对于提交消息的正文，请提供详细信息，说明您更改了什么、更改的原因以及使用的方法。如果您提到您是如何测试更改的，这可能也会有所帮助。在提交消息的正文中提供尽可能多的细节。

```

::: note
::: title
Note
:::

You do not need to provide a more detailed explanation of a change if the change is minor to the point of the single line summary providing all the information.
:::

```

- If the change addresses a specific bug or issue that is associated with a bug-tracking ID, include a reference to that ID in your detailed description. For example, the Yocto Project uses a specific convention for bug references \-\-- any commit that addresses a specific bug should use the following form for the detailed description. Be sure to use the actual bug-tracking ID from Bugzilla for bug-id:

> -如果更改解决了与错误跟踪 ID 相关联的特定错误或问题，请在详细描述中包含对该 ID 的引用。例如，Yocto 项目对 bug 引用使用了特定的约定\-任何解决特定 bug 的提交都应该使用以下形式进行详细描述。请确保使用 Bugzilla 的实际错误跟踪 ID 作为错误 ID：

```

```
Fixes [YOCTO #bug-id]

detailed description of change
```

```

## Using Email to Submit a Patch

Depending on the components changed, you need to submit the email to a specific mailing list. For some guidance on which mailing list to use, see the `list <dev-manual/changes:submitting a change to the yocto project>`{.interpreted-text role="ref"} at the beginning of this section. For a description of all the available mailing lists, see the \"`Mailing Lists <resources-mailinglist>`{.interpreted-text role="ref"}\" section in the Yocto Project Reference Manual.

> 根据更改的组件，您需要将电子邮件提交到特定的邮件列表。有关使用哪一个邮件列表的一些指导，请参阅本节开头的 `list<devmanual/changes:submit a change to the yocto project>`{.depredicted text role=“ref”}。有关所有可用邮件列表的说明，请参阅《Yocto 项目参考手册》中的“`mailing lists<resources mailinglist>`{.depreted text role=“ref”}\”一节。

Here is the general procedure on how to submit a patch through email without using the scripts once the steps in `dev-manual/changes:preparing changes for submission`{.interpreted-text role="ref"} have been followed:

> 以下是如何在不使用脚本的情况下通过电子邮件提交修补程序的一般过程，一旦遵循了 `dev manual/changes:prepare changes for submission`{.depredicted text role=“ref”}中的步骤：

1. *Format the Commit:* Format the commit into an email message. To format commits, use the `git format-patch` command. When you provide the command, you must include a revision list or a number of patches as part of the command. For example, either of these two commands takes your most recent single commit and formats it as an email message in the current directory:

> 1.*格式化提交：*将提交格式化为电子邮件。要格式化提交，请使用“git-format-patch”命令。提供命令时，必须将修订列表或多个修补程序作为命令的一部分。例如，这两个命令中的任何一个都会接受您最近的一次提交，并将其格式化为当前目录中的电子邮件：

```

> ```
> ```

$ git format-patch -1

> $git 格式修补程序-1

```

> ```
> ```

or :

> 或：

```

> ```
> ```

$ git format-patch HEAD~

> $git 格式补丁 HEAD~

```

> ```
> ```

After the command is run, the current directory contains a numbered `.patch` file for the commit.

> 命令运行后，当前目录包含一个编号为“.patch”的提交文件。

If you provide several commits as part of the command, the `git format-patch` command produces a series of numbered files in the current directory -- one for each commit. If you have more than one patch, you should also use the `--cover` option with the command, which generates a cover letter as the first \"patch\" in the series. You can then edit the cover letter to provide a description for the series of patches. For information on the `git format-patch` command, see `GIT_FORMAT_PATCH(1)` displayed using the `man git-format-patch` command.

> 如果您在命令中提供了几个提交，那么“git-format-patch”命令会在当前目录中生成一系列编号的文件——每个提交一个。如果您有多个修补程序，您还应该在命令中使用“--cover”选项，该命令会生成一个封面字母作为该系列中的第一个“修补程序”。然后，您可以编辑求职信以提供一系列修补程序的说明。有关“git format patch”命令的信息，请参阅使用“man-git format pattch”命令显示的“git_format_patch（1）”。

::: note

> ：：：注释

::: title

> ：：标题

Note

> 笔记

:::

> ：：：

If you are or will be a frequent contributor to the Yocto Project or to OpenEmbedded, you might consider requesting a contrib area and the necessary associated rights.

> 如果您是或将是 Yocto 项目或 OpenEmbedded 的经常贡献者，您可以考虑申请贡献区域和必要的相关权利。

:::

> ：：：

2. *Send the patches via email:* Send the patches to the recipients and relevant mailing lists by using the `git send-email` command.

> 2.*通过电子邮件发送补丁：*使用“git Send email”命令将补丁发送给收件人和相关邮件列表。

::: note

> ：：：注释

::: title

> ：：标题

Note

> 笔记

:::

> ：：：

In order to use `git send-email`, you must have the proper Git packages installed on your host. For Ubuntu, Debian, and Fedora the package is `git-email`.

> 为了使用“git-send email”，您必须在主机上安装正确的 git 软件包。对于 Ubuntu、Debian 和 Fedora，软件包是“git-email'”。

:::

> ：：：

The `git send-email` command sends email by using a local or remote Mail Transport Agent (MTA) such as `msmtp`, `sendmail`, or through a direct `smtp` configuration in your Git `~/.gitconfig` file. If you are submitting patches through email only, it is very important that you submit them without any whitespace or HTML formatting that either you or your mailer introduces. The maintainer that receives your patches needs to be able to save and apply them directly from your emails. A good way to verify that what you are sending will be applicable by the maintainer is to do a dry run and send them to yourself and then save and apply them as the maintainer would.

> “git send email”命令通过使用本地或远程邮件传输代理（MTA）（如“msmtp”、“sendmail”）或通过 git 文件中的直接“smtp”配置发送电子邮件。如果你只通过电子邮件提交补丁，那么提交补丁时不要使用任何空白或 HTML 格式，这一点非常重要。接收补丁的维护人员需要能够直接从您的电子邮件中保存和应用补丁。验证您发送的内容是否适用于维护人员的一个好方法是进行试运行并将其发送给自己，然后像维护人员那样保存和应用它们。

The `git send-email` command is the preferred method for sending your patches using email since there is no risk of compromising whitespace in the body of the message, which can occur when you use your own mail client. The command also has several options that let you specify recipients and perform further editing of the email message. For information on how to use the `git send-email` command, see `GIT-SEND-EMAIL(1)` displayed using the `man git-send-email` command.

> “git send email”命令是使用电子邮件发送修补程序的首选方法，因为当您使用自己的邮件客户端时，不会有泄露邮件正文中空白的风险。该命令还有几个选项，可用于指定收件人和执行电子邮件的进一步编辑。有关如何使用“git send e-mail”命令的信息，请参阅使用“man git send-email”命令显示的“git-send-email（1）”。

The Yocto Project uses a [Patchwork instance](https://patchwork.yoctoproject.org/) to track the status of patches submitted to the various mailing lists and to support automated patch testing. Each submitted patch is checked for common mistakes and deviations from the expected patch format and submitters are notified by patchtest if such mistakes are found. This process helps to reduce the burden of patch review on maintainers.

> Yocto 项目使用[修补程序实例](https://patchwork.yoctoproject.org/)以跟踪提交到各种邮件列表的补丁的状态，并支持自动补丁测试。检查每个提交的补丁是否存在常见错误和与预期补丁格式的偏差，如果发现此类错误，补丁测试会通知提交者。这个过程有助于减轻维护人员的补丁审查负担。

::: note
::: title
Note
:::

This system is imperfect and changes can sometimes get lost in the flow. Asking about the status of a patch or change is reasonable if the change has been idle for a while with no feedback.

> 这个系统是不完美的，变化有时会在流程中丢失。如果更改已经闲置了一段时间而没有反馈，那么询问补丁或更改的状态是合理的。
> :::

## Using Scripts to Push a Change Upstream and Request a Pull

For larger patch series it is preferable to send a pull request which not only includes the patch but also a pointer to a branch that can be pulled from. This involves making a local branch for your changes, pushing this branch to an accessible repository and then using the `create-pull-request` and `send-pull-request` scripts from openembedded-core to create and send a patch series with a link to the branch for review.

> 对于较大的补丁系列，最好发送一个拉取请求，该请求不仅包括补丁，还包括指向可以从中拉取的分支的指针。这包括为您的更改创建一个本地分支，将该分支推送到一个可访问的存储库，然后使用 openembedded core 中的“创建拉请求”和“发送拉请求”脚本来创建并发送一个带有链接的补丁系列到该分支以供审查。

Follow this procedure to push a change to an upstream \"contrib\" Git repository once the steps in `dev-manual/changes:preparing changes for submission`{.interpreted-text role="ref"} have been followed:

> 一旦遵循了 `dev manual/changes:prepare changes for submission`｛.explored text role=“ref”｝中的步骤，请按照此过程将更改推送到上游的“contrib”Git 存储库：

::: note
::: title
Note
:::

You can find general Git information on how to push a change upstream in the [Git Community Book](https://git-scm.com/book/en/v2/Distributed-Git-Distributed-Workflows).

> 你可以在[Git 社区书]中找到关于如何向上游推动变革的一般 Git 信息([https://git-scm.com/book/en/v2/Distributed-Git-Distributed-Workflows](https://git-scm.com/book/en/v2/Distributed-Git-Distributed-Workflows))。
> :::

1. *Push Your Commits to a \"Contrib\" Upstream:* If you have arranged for permissions to push to an upstream contrib repository, push the change to that repository:

> 1.*将您的提交推送到\“Contrib”上游：*如果您已安排将权限推送到上游 Contrib 存储库，请将更改推送到该存储库：

```

> ```
> ```

$ git push upstream_remote_repo local_branch_name

> $git 推送上行_移动_本地_分支_名称

```

> ```
> ```

For example, suppose you have permissions to push into the upstream `meta-intel-contrib` repository and you are working in a local branch named [your_name]{.title-ref}`/README`. The following command pushes your local commits to the `meta-intel-contrib` upstream repository and puts the commit in a branch named [your_name]{.title-ref}`/README`:

> 例如，假设您有权推送到上游的“meta intel contrib”存储库，并且您正在名为[your_name]｛.title ref｝`/README'的本地分支中工作。以下命令将您的本地提交推送到“meta-intel contrib’上游存储库，并将提交放入名为[your_name]{.title ref}`/README'的分支中：

```

> ```
> ```

$ git push meta-intel-contrib your_name/README

> $git 推送 meta intel contrib your_name/README

```

> ```
> ```

2. *Determine Who to Notify:* Determine the maintainer or the mailing list that you need to notify for the change.

> 2.*确定通知谁：*确定更改需要通知的维护人员或邮件列表。

Before submitting any change, you need to be sure who the maintainer is or what mailing list that you need to notify. Use either these methods to find out:

> 在提交任何更改之前，您需要确定谁是维护者，或者您需要通知哪些邮件列表。使用以下任一方法查找：

- *Maintenance File:* Examine the `maintainers.inc` file, which is located in the `Source Directory`{.interpreted-text role="term"} at `meta/conf/distro/include`, to see who is responsible for code.

> -*维护文件：*检查 `maintainers.inc` 文件，该文件位于 `meta/conf/distro/include` 的 `Source Directory`{.reflated-text role=“term”}中，查看谁负责代码。

- *Search by File:* Using `overview-manual/development-environment:git`{.interpreted-text role="ref"}, you can enter the following command to bring up a short list of all commits against a specific file:

> -*按文件搜索：*使用 `overview manual/development environment:git`｛.explored text role=“ref”｝，您可以输入以下命令来显示针对特定文件的所有提交的短列表：

```

```
git shortlog -- filename
```

Just provide the name of the file for which you are interested. The information returned is not ordered by history but does include a list of everyone who has committed grouped by name. From the list, you can see who is responsible for the bulk of the changes against the file.

```

- *Examine the List of Mailing Lists:* For a list of the Yocto Project and related mailing lists, see the \"`Mailing lists <resources-mailinglist>`{.interpreted-text role="ref"}\" section in the Yocto Project Reference Manual.

> -*检查邮件列表列表：*有关 Yocto 项目和相关邮件列表的列表，请参阅《Yocto Project Reference Manual》中的“`Mailing Lists<resources mailinglist>`{.depreted text role=“ref”}\”一节。

3. *Make a Pull Request:* Notify the maintainer or the mailing list that you have pushed a change by making a pull request.

> 3.*提出拉取请求：*通知维护人员或邮件列表，您已经通过提出拉取申请推动了更改。

The Yocto Project provides two scripts that conveniently let you generate and send pull requests to the Yocto Project. These scripts are `create-pull-request` and `send-pull-request`. You can find these scripts in the `scripts` directory within the `Source Directory`{.interpreted-text role="term"} (e.g. `poky/scripts`).

> Yocto 项目提供了两个脚本，可以方便地生成拉取请求并将其发送到 Yocto Project。这些脚本是“创建请求”和“发送请求”。您可以在“源目录”中的“脚本”目录中找到这些脚本（例如“poky/scripts”）。

Using these scripts correctly formats the requests without introducing any whitespace or HTML formatting. The maintainer that receives your patches either directly or through the mailing list needs to be able to save and apply them directly from your emails. Using these scripts is the preferred method for sending patches.

> 使用这些脚本可以正确地格式化请求，而不会引入任何空白或 HTML 格式。直接或通过邮件列表接收补丁的维护人员需要能够直接从您的电子邮件中保存和应用补丁。使用这些脚本是发送修补程序的首选方法。

First, create the pull request. For example, the following command runs the script, specifies the upstream repository in the contrib directory into which you pushed the change, and provides a subject line in the created patch files:

> 首先，创建 pull 请求。例如，以下命令运行脚本，在 contrib 目录中指定上游存储库，将更改推送到该目录中，并在创建的修补程序文件中提供主题行：

```

> ```
> ```

$ poky/scripts/create-pull-request -u meta-intel-contrib -s "Updated Manual Section Reference in README"

> $poky/scripts/create pull request-u meta intel contrib-s“自述文件中更新的手册章节参考”

```

> ```
> ```

Running this script forms `*.patch` files in a folder named `pull-`[PID]{.title-ref} in the current directory. One of the patch files is a cover letter.

> 运行此脚本会在当前目录中名为“pull-”[PID]｛.title-ref｝的文件夹中形成“*.patch”文件。其中一个补丁文件是一封求职信。

Before running the `send-pull-request` script, you must edit the cover letter patch to insert information about your change. After editing the cover letter, send the pull request. For example, the following command runs the script and specifies the patch directory and email address. In this example, the email address is a mailing list:

> 在运行“发送请求”脚本之前，必须编辑求职信补丁以插入有关更改的信息。编辑求职信后，发送撤回请求。例如，以下命令运行脚本并指定修补程序目录和电子邮件地址。在本例中，电子邮件地址是一个邮件列表：

```

> ```
> ```

$ poky/scripts/send-pull-request -p ~/meta-intel/pull-10565 -t meta-intel@lists.yoctoproject.orgmailto:meta-intel@lists.yoctoproject.org

> $poky/scripts/发送 pull-request-p~/meta-intel/pull10565-tmeta-intel@lists.yoctoproject.org

```

> ```
> ```

You need to follow the prompts as the script is interactive.

> 您需要按照提示进行操作，因为脚本是交互式的。

::: note

> ：：：注释

::: title

> ：：标题

Note

> 笔记

:::

> ：：：

For help on using these scripts, simply provide the `-h` argument as follows:

> 有关使用这些脚本的帮助，只需提供“-h”参数，如下所示：

```

> ```
> ```

$ poky/scripts/create-pull-request -h

> $poky/脚本/创建拉取请求-h

$ poky/scripts/send-pull-request -h

> $poky/脚本/发送拉取请求-h

```

> ```
> ```

:::

> ：：：

## Responding to Patch Review

You may get feedback on your submitted patches from other community members or from the automated patchtest service. If issues are identified in your patch then it is usually necessary to address these before the patch will be accepted into the project. In this case you should amend the patch according to the feedback and submit an updated version to the relevant mailing list, copying in the reviewers who provided feedback to the previous version of the patch.

> 您可能会从其他社区成员或自动补丁测试服务获得关于您提交的补丁的反馈。如果在您的修补程序中发现了问题，那么在将修补程序接受到项目中之前，通常有必要解决这些问题。在这种情况下，您应该根据反馈修改补丁，并将更新的版本提交到相关邮件列表中，复制给向先前版本的补丁提供反馈的审阅者。

The patch should be amended using `git commit --amend` or perhaps `git rebase` for more expert git users. You should also modify the `[PATCH]` tag in the email subject line when sending the revised patch to mark the new iteration as `[PATCH v2]`, `[PATCH v3]`, etc as appropriate. This can be done by passing the `-v` argument to `git format-patch` with a version number.

> 对于更专业的 git 用户，应该使用“git commit--modify”或“git rebase”来修改补丁。发送修订后的补丁时，您还应修改电子邮件主题行中的“[PACH]”标记，以将新迭代标记为“[PACH v2]'、“[PATCH v3]”等（视情况而定）。这可以通过将“-v”参数传递给带有版本号的“git format patch”来实现。

Lastly please ensure that you also test your revised changes. In particular please don\'t just edit the patch file written out by `git format-patch` and resend it.

> 最后，请确保您还测试了修改后的更改。特别是，请不要只是编辑“git-format-patch”编写的补丁文件并重新发送。

## Submitting Changes to Stable Release Branches

The process for proposing changes to a Yocto Project stable branch differs from the steps described above. Changes to a stable branch must address identified bugs or CVEs and should be made carefully in order to avoid the risk of introducing new bugs or breaking backwards compatibility. Typically bug fixes must already be accepted into the master branch before they can be backported to a stable branch unless the bug in question does not affect the master branch or the fix on the master branch is unsuitable for backporting.

> 对 Yocto 项目稳定分支机构提出变更的过程与上述步骤不同。对稳定分支的更改必须解决已识别的错误或 CVE，并且应该小心进行，以避免引入新错误或破坏向后兼容性的风险。通常情况下，错误修复必须已经被主分支接受，然后才能将其反向移植到稳定分支，除非有问题的错误不会影响主分支，或者主分支上的修复不适合反向移植。

The list of stable branches along with the status and maintainer for each branch can be obtained from the :yocto_wiki:[Releases wiki page \</Releases\>]{.title-ref}.

> 稳定分支的列表以及每个分支的状态和维护者可以从：yocto_wiki:[发布 wiki 页面\</Releases\>]｛.title-ref｝中获得。

::: note
::: title
Note
:::

Changes will not typically be accepted for branches which are marked as End-Of-Life (EOL).

> 对于标记为寿命终止（EOL）的分支机构，通常不接受变更。
> :::

With this in mind, the steps to submit a change for a stable branch are as follows:

> 考虑到这一点，为稳定分支提交更改的步骤如下：

1. *Identify the bug or CVE to be fixed:* This information should be collected so that it can be included in your submission.

> 1.*确定要修复的错误或 CVE：*应收集这些信息，以便将其包含在您提交的文件中。

See `dev-manual/vulnerabilities:checking for vulnerabilities`{.interpreted-text role="ref"} for details about CVE tracking.

> 有关 CVE 跟踪的详细信息，请参阅“dev manual/vulnerables:checking for 漏洞”｛.depreced text role=“ref”｝。

2. *Check if the fix is already present in the master branch:* This will result in the most straightforward path into the stable branch for the fix.

> 2.*检查修复程序是否已存在于主分支中：*这将导致修复程序进入稳定分支的最直接路径。

1. *If the fix is present in the master branch \-\-- submit a backport request by email:* You should send an email to the relevant stable branch maintainer and the mailing list with details of the bug or CVE to be fixed, the commit hash on the master branch that fixes the issue and the stable branches which you would like this fix to be backported to.

> 1.*如果修复程序存在于主分支中\-通过电子邮件提交后端口请求：*您应该向相关的稳定分支维护者发送一封电子邮件，并附上要修复的 bug 或 CVE 的详细信息、修复问题的主分支上的提交哈希以及您希望将此修复程序后端口到的稳定分支。

2. *If the fix is not present in the master branch \-\-- submit the fix to the master branch first:* This will ensure that the fix passes through the project\'s usual patch review and test processes before being accepted. It will also ensure that bugs are not left unresolved in the master branch itself. Once the fix is accepted in the master branch a backport request can be submitted as above.

> 2.*如果主分支中没有修复程序\-首先将修复程序提交给主分支：*这将确保修复程序在被接受之前通过项目的常规补丁审查和测试过程。它还将确保错误不会在主分支本身中未解决。一旦在主分支中接受了修复，就可以如上所述提交后台端口请求。

3. *If the fix is unsuitable for the master branch \-\-- submit a patch directly for the stable branch:* This method should be considered as a last resort. It is typically necessary when the master branch is using a newer version of the software which includes an upstream fix for the issue or when the issue has been fixed on the master branch in a way that introduces backwards incompatible changes. In this case follow the steps in `dev-manual/changes:preparing changes for submission`{.interpreted-text role="ref"} and `dev-manual/changes:using email to submit a patch`{.interpreted-text role="ref"} but modify the subject header of your patch email to include the name of the stable branch which you are targetting. This can be done using the `--subject-prefix` argument to `git format-patch`, for example to submit a patch to the dunfell branch use `git format-patch --subject-prefix='&DISTRO_NAME_NO_CAP_MINUS_ONE;][PATCH' ...`.

> 3.*如果修复不适合主分支\-直接为稳定分支提交补丁：*这种方法应被视为最后的手段。当主分支使用包括问题的上游修复的较新版本的软件时，或者当问题已经以引入向后不兼容的更改的方式在主分支上修复时，这通常是必要的。在这种情况下，请遵循 `dev manual/changes:prepare changes for submission`｛.depreted text role=“ref”｝和 `dev manual/changes:using e-mail to submit a patch`｛.epreted text role=“ref”}中的步骤，但要修改修补程序电子邮件的主题标题，以包含您要针对的稳定分支的名称。这可以使用“git format patch”的“--subject prefix”参数来完成，例如，要向 dunfall 分支提交修补程序，请使用“git-format patch--subject refix='&DISTRO_NAME_NO_CAP_MINUS_ONE；][修补程序“…”。
```
