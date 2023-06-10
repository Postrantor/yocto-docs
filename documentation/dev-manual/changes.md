---
tip: translate by openai@2023-06-10 10:14:07
...
---
title: Making Changes to the Yocto Project
------------------------------------------

Because the Yocto Project is an open-source, community-based project, you can effect changes to the project. This section presents procedures that show you how to submit a defect against the project and how to submit a change.

> 因为 Yocto Project 是一个开源的社区项目，你可以对项目进行修改。本节介绍了如何提交缺陷和更改的程序。

# Submitting a Defect Against the Yocto Project

Use the Yocto Project implementation of [Bugzilla](https://www.bugzilla.org/about/) to submit a defect (bug) against the Yocto Project. For additional information on this implementation of Bugzilla see the \"`Yocto Project Bugzilla <resources-bugtracker>`.

> 使用 Yocto 项目实现的 [Bugzilla](https://www.bugzilla.org/about/) 提交对 Yocto 项目的缺陷(错误)。有关此 Bugzilla 实现的更多信息，请参见 Yocto 项目参考手册中的“Yocto 项目 Bugzilla <resources-bugtracker>”部分。有关以下步骤的更多详细信息，请参见 Yocto 项目：yocto_wiki：[Bugzilla wiki 页面\</Bugzilla_Configuration_and_Bug_Tracking\>]。

Use the following general steps to submit a bug:

1. Open the Yocto Project implementation of :yocto_bugs:[Bugzilla \<\>].
2. Click \"File a Bug\" to enter a new bug.
3. Choose the appropriate \"Classification\", \"Product\", and \"Component\" for which the bug was found. Bugs for the Yocto Project fall into one of several classifications, which in turn break down into several products and components. For example, for a bug against the `meta-intel` layer, you would choose \"Build System, Metadata & Runtime\", \"BSPs\", and \"bsps-meta-intel\", respectively.

> 请选择适当的“分类”，“产品”和“组件”来定位发现的错误。Yocto Project 中的错误可以分为几种类别，进而又可以分为几种产品和组件。例如，对于 meta-intel 层的错误，您可以选择“构建系统，元数据和运行时”，“BSPs”和“bsps-meta-intel”。

4. Choose the \"Version\" of the Yocto Project for which you found the bug (e.g. &DISTRO;).
5. Determine and select the \"Severity\" of the bug. The severity indicates how the bug impacted your work.
6. Choose the \"Hardware\" that the bug impacts.
7. Choose the \"Architecture\" that the bug impacts.
8. Choose a \"Documentation change\" item for the bug. Fixing a bug might or might not affect the Yocto Project documentation. If you are unsure of the impact to the documentation, select \"Don\'t Know\".

> 选择一个“文档更改”项目来修复这个 bug。修复 bug 可能会也可能不会影响 Yocto Project 文档。如果您不确定对文档的影响，请选择“不知道”。

9. Provide a brief \"Summary\" of the bug. Try to limit your summary to just a line or two and be sure to capture the essence of the bug.
10. Provide a detailed \"Description\" of the bug. You should provide as much detail as you can about the context, behavior, output, and so forth that surrounds the bug. You can even attach supporting files for output from logs by using the \"Add an attachment\" button.

> 请提供关于这个错误的详细描述。您应尽可能提供有关上下文、行为、输出等的详细信息。您甚至可以使用“添加附件”按钮添加来自日志的支持文件。

11. Click the \"Submit Bug\" button submit the bug. A new Bugzilla number is assigned to the bug and the defect is logged in the bug tracking system.

> 点击“提交错误”按钮提交错误。一个新的 Bugzilla 编号分配给错误，错误记录在错误跟踪系统中。

Once you file a bug, the bug is processed by the Yocto Project Bug Triage Team and further details concerning the bug are assigned (e.g. priority and owner). You are the \"Submitter\" of the bug and any further categorization, progress, or comments on the bug result in Bugzilla sending you an automated email concerning the particular change or progress to the bug.

> 一旦您提交了一个错误，Yocto 项目错误分类团队将处理该错误，并为该错误分配更多细节(例如优先级和所有者)。您是该错误的“提交者”，在 Bugzilla 中对错误进行任何进一步的分类、进度或评论，都会使您收到一封有关特定变更或进展的自动电子邮件。

# Submitting a Change to the Yocto Project

Contributions to the Yocto Project and OpenEmbedded are very welcome. Because the system is extremely configurable and flexible, we recognize that developers will want to extend, configure or optimize it for their specific uses.

> 对 Yocto 项目和 OpenEmbedded 的贡献是非常受欢迎的。由于该系统非常可配置和灵活，我们认识到开发人员将希望为其特定用途扩展、配置或优化它。

The Yocto Project uses a mailing list and a patch-based workflow that is similar to the Linux kernel but contains important differences. In general, there is a mailing list through which you can submit patches. You should send patches to the appropriate mailing list so that they can be reviewed and merged by the appropriate maintainer. The specific mailing list you need to use depends on the location of the code you are changing. Each component (e.g. layer) should have a `README` file that indicates where to send the changes and which process to follow.

> 项目 Yocto 使用邮件列表和基于补丁的工作流程，与 Linux 内核相似，但有重要的不同。一般来说，有一个邮件列表，您可以提交补丁。您应该将补丁发送到适当的邮件列表，以便由适当的维护者进行审查和合并。您需要使用的具体邮件列表取决于您正在更改的代码的位置。每个组件(例如层)应该有一个 `README` 文件，指示发送更改的位置以及要遵循的过程。

You can send the patch to the mailing list using whichever approach you feel comfortable with to generate the patch. Once sent, the patch is usually reviewed by the community at large. If somebody has concerns with the patch, they will usually voice their concern over the mailing list. If a patch does not receive any negative reviews, the maintainer of the affected layer typically takes the patch, tests it, and then based on successful testing, merges the patch.

> 你可以使用任何你觉得舒服的方法来生成补丁，然后把补丁发送到邮件列表。一旦发送，补丁通常会被整个社区审查。如果有人对补丁有疑虑，他们通常会通过邮件列表表达他们的担忧。如果补丁没有收到任何负面评论，受影响层的维护者通常会接受补丁，进行测试，然后根据测试成功，合并补丁。

The \"poky\" repository, which is the Yocto Project\'s reference build environment, is a hybrid repository that contains several individual pieces (e.g. BitBake, Metadata, documentation, and so forth) built using the combo-layer tool. The upstream location used for submitting changes varies by component:

> "Poky"存储库是 Yocto 项目的参考构建环境，它是一个混合存储库，包含使用 combo-layer 工具构建的几个单独组件(例如 BitBake，元数据，文档等)。提交更改所使用的上游位置因组件而异：

- *Core Metadata:* Send your patch to the :oe_lists:[openembedded-core \</g/openembedded-core\>] mailing list. For example, a change to anything under the `meta` or `scripts` directories should be sent to this mailing list.

> 请将您的补丁发送到 :oe_lists:[openembedded-core \</g/openembedded-core\>] 邮件列表。例如，对 `meta` 或 `scripts` 目录下的任何内容所做的更改都应发送到此邮件列表。

- *BitBake:* For changes to BitBake (i.e. anything under the `bitbake` directory), send your patch to the :oe_lists:[bitbake-devel \</g/bitbake-devel\>] mailing list.

> 对于 BitBake 的更改(即位于 `bitbake` 目录下的任何内容)，请将您的补丁发送到:oe_lists:[bitbake-devel \</g/bitbake-devel\>] 邮件列表。

- *\"meta-\*\" trees:* These trees contain Metadata. Use the :yocto_lists:[poky \</g/poky\>] mailing list.
- *Documentation*: For changes to the Yocto Project documentation, use the :yocto_lists:[docs \</g/docs\>] mailing list.

For changes to other layers hosted in the Yocto Project source repositories (i.e. `yoctoproject.org`) and tools use the :yocto_lists:[Yocto Project \</g/yocto/\>] general mailing list.

> 对于位于 Yocto 项目源代码存储库(即 `yoctoproject.org`)中的其他层次的更改以及工具，请使用：yocto_lists:[Yocto Project \</g/yocto/\>] 通用邮件列表。

::: note
::: title
Note
:::

Sometimes a layer\'s documentation specifies to use a particular mailing list. If so, use that list.
:::

For additional recipes that do not fit into the core Metadata, you should determine which layer the recipe should go into and submit the change in the manner recommended by the documentation (e.g. the `README` file) supplied with the layer. If in doubt, please ask on the Yocto general mailing list or on the openembedded-devel mailing list.

> 如果有额外的 recipes 不适合核心元数据，您应该确定该 recipes 应该放入哪一层，并按照文档(例如 `README` 文件)提供的建议提交更改。如果有疑问，请在 Yocto 通用邮件列表或 openembedded-devel 邮件列表上提问。

You can also push a change upstream and request a maintainer to pull the change into the component\'s upstream repository. You do this by pushing to a contribution repository that is upstream. See the \"`overview-manual/development-environment:git workflows and the yocto project`\" section in the Yocto Project Overview and Concepts Manual for additional concepts on working in the Yocto Project development environment.

> 你也可以推送更改到上游，并要求维护者将更改拉入组件的上游存储库。您可以通过推送到上游的贡献存储库来完成此操作。有关在 Yocto 项目开发环境中工作的其他概念，请参阅 Yocto 项目概述与概念手册中的“概览手册/开发环境：Git 工作流程和 Yocto 项目”部分。

Maintainers commonly use `-next` branches to test submissions prior to merging patches. Thus, you can get an idea of the status of a patch based on whether the patch has been merged into one of these branches. The commonly used testing branches for OpenEmbedded-Core are as follows:

> 维护者通常使用 `-next` 分支来测试提交的补丁，因此，您可以根据补丁是否已合并到这些分支中来了解补丁的状态。OpenEmbedded-Core 的常用测试分支如下：

- *openembedded-core \"master-next\" branch:* This branch is part of the :oe_[git:%60openembedded-core](git:%60openembedded-core) \</openembedded-core/\>\` repository and contains proposed changes to the core metadata.

> - *openembedded-core "master-next" 分支:* 这个分支是:oe_[git:%60openembedded-core](git:%60openembedded-core) \</openembedded-core/\>\` 仓库的一部分，包含了对核心元数据的建议更改。

- *poky \"master-next\" branch:* This branch is part of the :yocto_[git:%60poky](git:%60poky) \</poky/\>\` repository and combines proposed changes to BitBake, the core metadata and the poky distro.

> - *poky "master-next" 分支：*此分支是 :yocto_[git:%60poky](git:%60poky) \</poky/\>\` 仓库的一部分，它结合了对 BitBake、核心元数据和 poky 分发的建议更改。

Similarly, stable branches maintained by the project may have corresponding `-next` branches which collect proposed changes. For example, `&DISTRO_NAME_NO_CAP;-next` and `&DISTRO_NAME_NO_CAP_MINUS_ONE;-next` branches in both the \"openembdedded-core\" and \"poky\" repositories.

> 同样，由项目维护的稳定分支可能具有相应的 `-next` 分支，用于收集提出的变更。例如，在“openembdedded-core”和“poky”存储库中的 `&DISTRO_NAME_NO_CAP;-next` 和 `&DISTRO_NAME_NO_CAP_MINUS_ONE;-next` 分支。

Other layers may have similar testing branches but there is no formal requirement or standard for these so please check the documentation for the layers you are contributing to.

> 其他层可能有类似的测试分支，但没有正式的要求或标准，因此请查看您要贡献的层的文档。

The following sections provide procedures for submitting a change.

## Preparing Changes for Submission

1. *Make Your Changes Locally:* Make your changes in your local Git repository. You should make small, controlled, isolated changes. Keeping changes small and isolated aids review, makes merging/rebasing easier and keeps the change history clean should anyone need to refer to it in future.

> 1. *在本地做出更改：* 在您的本地 Git 存储库中做出更改。您应该做出小而受控的隔离更改。保持更改小而隔离有助于审查，使合并/重新设置更容易，并使更改历史保持干净，以便将来有人需要参考它。

2. *Stage Your Changes:* Stage your changes by using the `git add` command on each file you changed.
3. *Commit Your Changes:* Commit the change by using the `git commit` command. Make sure your commit information follows standards by following these accepted conventions:

> 3. *提交更改：* 使用 `git commit` 命令提交更改。通过遵循以下接受的约定确保您的提交信息遵守标准：

- Be sure to include a \"Signed-off-by:\" line in the same style as required by the Linux kernel. This can be done by using the `git commit -s` command. Adding this line signifies that you, the submitter, have agreed to the Developer\'s Certificate of Origin 1.1 as follows:

> 确保在与 Linux 内核要求相同的样式中包括一个“Signed-off-by：”行。这可以通过使用 `git commit -s` 命令来完成。添加此行表示您，提交者，已同意以下开发人员证书原件 1.1：

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

> 提供更改的单行摘要，如果需要更多解释，请在提交正文中提供更多细节。此摘要通常可在“简短列表”中查看。因此，提供简短描述的内容，可以向读者提供许多提交的摘要，在查看许多提交时很有用。您应该使用配方名称(如果更改配方)或更改文件的简短路径来前缀此简短描述。

- For the body of the commit message, provide detailed information that describes what you changed, why you made the change, and the approach you used. It might also be helpful if you mention how you tested the change. Provide as much detail as you can in the body of the commit message.

> 对于提交消息的正文，请提供详细信息，描述您做出了什么改变，为什么进行改变，以及您使用的方法。如果您提及了如何测试更改，也会有所帮助。尽可能详细地在提交消息的正文中提供信息。

```

::: note
::: title
Note
:::

You do not need to provide a more detailed explanation of a change if the change is minor to the point of the single line summary providing all the information.
:::

```

- If the change addresses a specific bug or issue that is associated with a bug-tracking ID, include a reference to that ID in your detailed description. For example, the Yocto Project uses a specific convention for bug references \-\-- any commit that addresses a specific bug should use the following form for the detailed description. Be sure to use the actual bug-tracking ID from Bugzilla for bug-id:

> 如果更改与 Bugzilla 中的特定 bug 或问题有关，请在详细描述中包括对该 ID 的引用。例如，Yocto Project 使用特定的约定来引用 bug - 任何解决特定 bug 的提交都应使用以下格式进行详细描述。请务必使用 Bugzilla 中的实际 bug-tracking ID，bug-id：

```

```
Fixes [YOCTO #bug-id]

detailed description of change
```

```

## Using Email to Submit a Patch

Depending on the components changed, you need to submit the email to a specific mailing list. For some guidance on which mailing list to use, see the `list <dev-manual/changes:submitting a change to the yocto project>`\" section in the Yocto Project Reference Manual.

> 根据更改的组件，您需要将电子邮件发送到特定的邮件列表。有关使用哪个邮件列表的指导，请参见本节开头的 `list <dev-manual/changes:submitting a change to the yocto project>`”部分。

Here is the general procedure on how to submit a patch through email without using the scripts once the steps in `dev-manual/changes:preparing changes for submission` have been followed:

> 以下是在遵循 dev-manual/changes：准备提交变更后，如何通过电子邮件而不使用脚本提交补丁的一般步骤：

1. *Format the Commit:* Format the commit into an email message. To format commits, use the `git format-patch` command. When you provide the command, you must include a revision list or a number of patches as part of the command. For example, either of these two commands takes your most recent single commit and formats it as an email message in the current directory:

> 1. *格式化提交：* 使用 `git format-patch` 命令将提交格式化为电子邮件消息。提供此命令时，必须包含修订列表或多个补丁作为命令的一部分。例如，以下两个命令均可将最近的单个提交格式化为当前目录中的电子邮件消息：

```

$ git format-patch -1

```

or :

```

$ git format-patch HEAD~

```

After the command is run, the current directory contains a numbered `.patch` file for the commit.

If you provide several commits as part of the command, the `git format-patch` command produces a series of numbered files in the current directory -- one for each commit. If you have more than one patch, you should also use the `--cover` option with the command, which generates a cover letter as the first \"patch\" in the series. You can then edit the cover letter to provide a description for the series of patches. For information on the `git format-patch` command, see `GIT_FORMAT_PATCH(1)` displayed using the `man git-format-patch` command.

> 如果您提供多个提交作为命令的一部分，git format-patch 命令会在当前目录中生成一系列编号文件--每次提交一个。如果你有多个补丁，你也应该使用 `--cover` 选项与命令，这会生成一封封面信作为系列的第一个“补丁”。然后，您可以编辑封面信件来提供补丁系列的描述。有关 git format-patch 命令的信息，请参阅使用 `man git-format-patch` 命令显示的 `GIT_FORMAT_PATCH(1)`。

::: note
::: title
Note
:::

If you are or will be a frequent contributor to the Yocto Project or to OpenEmbedded, you might consider requesting a contrib area and the necessary associated rights.

> 如果您现在或将来经常为 Yocto Project 或 OpenEmbedded 做贡献，您可以考虑申请一个贡献区域和必要的权限。
> :::

2. *Send the patches via email:* Send the patches to the recipients and relevant mailing lists by using the `git send-email` command.

   ::: note
   ::: title
   Note
   :::

   In order to use `git send-email`, you must have the proper Git packages installed on your host. For Ubuntu, Debian, and Fedora the package is `git-email`.

> 要使用 `git send-email`，您必须在您的主机上安装正确的 Git 包。对于 Ubuntu、Debian 和 Fedora，包的名称为 `git-email`。
> :::

The `git send-email` command sends email by using a local or remote Mail Transport Agent (MTA) such as `msmtp`, `sendmail`, or through a direct `smtp` configuration in your Git `~/.gitconfig` file. If you are submitting patches through email only, it is very important that you submit them without any whitespace or HTML formatting that either you or your mailer introduces. The maintainer that receives your patches needs to be able to save and apply them directly from your emails. A good way to verify that what you are sending will be applicable by the maintainer is to do a dry run and send them to yourself and then save and apply them as the maintainer would.

> 命令 `git send-email` 通过本地或远程邮件传输代理(MTA)，如 `msmtp`、`sendmail` 或通过 Git `~/.gitconfig` 文件中的直接 `smtp` 配置发送电子邮件。如果您只通过电子邮件提交补丁，非常重要的是，您必须提交您自己或您的邮件程序引入的任何空格或 HTML 格式。接收您补丁的维护者需要能够直接从您的电子邮件中保存并应用它们。验证您发送的内容将由维护者应用的一种好方法是进行干预运行，并将其发送给自己，然后像维护者一样保存和应用它们。

The `git send-email` command is the preferred method for sending your patches using email since there is no risk of compromising whitespace in the body of the message, which can occur when you use your own mail client. The command also has several options that let you specify recipients and perform further editing of the email message. For information on how to use the `git send-email` command, see `GIT-SEND-EMAIL(1)` displayed using the `man git-send-email` command.

> 命令 `git send-email` 是通过电子邮件发送补丁的首选方法，因为没有风险会破坏消息正文中的空白，这种情况可能会发生在您使用自己的邮件客户端时。该命令还具有几个选项，可以指定收件人并进一步编辑电子邮件消息。有关如何使用命令 `git send-email` 的信息，请参阅使用命令 `man git-send-email` 显示的 `GIT-SEND-EMAIL(1)`。

The Yocto Project uses a [Patchwork instance](https://patchwork.yoctoproject.org/) to track the status of patches submitted to the various mailing lists and to support automated patch testing. Each submitted patch is checked for common mistakes and deviations from the expected patch format and submitters are notified by patchtest if such mistakes are found. This process helps to reduce the burden of patch review on maintainers.

> 项目 Yocto 使用 [Patchwork 实例](https://patchwork.yoctoproject.org/)跟踪提交到各个邮件列表的补丁的状态，并支持自动补丁测试。每个提交的补丁都会检查常见的错误和偏离预期的补丁格式，如果发现此类错误，patchtest 会通知提交者。这个过程有助于减轻维护人员的补丁审查负担。

::: note
::: title
Note
:::

This system is imperfect and changes can sometimes get lost in the flow. Asking about the status of a patch or change is reasonable if the change has been idle for a while with no feedback.

> 这个系统不完善，有时变化会在流程中丢失。如果变化已经很久没有反馈，询问补丁或变更的状态是合理的。
> :::

## Using Scripts to Push a Change Upstream and Request a Pull

For larger patch series it is preferable to send a pull request which not only includes the patch but also a pointer to a branch that can be pulled from. This involves making a local branch for your changes, pushing this branch to an accessible repository and then using the `create-pull-request` and `send-pull-request` scripts from openembedded-core to create and send a patch series with a link to the branch for review.

> 对于较大的补丁系列，最好发送一个拉取请求，不仅包括补丁，还包括指向可以拉取的分支的指针。这涉及为您的更改创建本地分支，将此分支推送到可访问的存储库，然后使用 openembedded-core 中的 `create-pull-request` 和 `send-pull-request` 脚本创建并发送补丁系列，并附上指向该分支的链接以进行审查。

Follow this procedure to push a change to an upstream \"contrib\" Git repository once the steps in `dev-manual/changes:preparing changes for submission` have been followed:

> 请按照以下步骤，在完成“dev-manual/changes：准备提交的更改”的步骤后，将更改推送到上游“contrib”Git 存储库：

::: note
::: title
Note
:::

You can find general Git information on how to push a change upstream in the [Git Community Book](https://git-scm.com/book/en/v2/Distributed-Git-Distributed-Workflows).

> 你可以在 [Git Community Book](https://git-scm.com/book/en/v2/Distributed-Git-Distributed-Workflows) 中找到有关如何将更改推送到上游的一般 Git 信息。
> :::

1. *Push Your Commits to a \"Contrib\" Upstream:* If you have arranged for permissions to push to an upstream contrib repository, push the change to that repository:

> 1. *将你的提交推送到“贡献”上游：* 如果你已经获得了推送到上游贡献仓库的权限，请将该更改推送到该仓库：

```

$ git push upstream_remote_repo local_branch_name

```

For example, suppose you have permissions to push into the upstream `meta-intel-contrib` repository and you are working in a local branch named [your_name]`/README`:

> 例如，假设您有权限推送到上游 `meta-intel-contrib` 存储库中，并且您正在本地分支[your_name]`/README` 的分支中：

```

$ git push meta-intel-contrib your_name/README

```

2. *Determine Who to Notify:* Determine the maintainer or the mailing list that you need to notify for the change.

   Before submitting any change, you need to be sure who the maintainer is or what mailing list that you need to notify. Use either these methods to find out:

> 在提交任何更改之前，您需要确定维护者是谁，或者需要通知哪个邮件列表。使用以下任一方法来查找：

- *Maintenance File:* Examine the `maintainers.inc` file, which is located in the `Source Directory` at `meta/conf/distro/include`, to see who is responsible for code.

> -*维护文件：*检查位于 `meta/conf/distro/include` 中的 `maintainers.inc` 文件，以查看谁负责代码。

- *Search by File:* Using `overview-manual/development-environment:git`, you can enter the following command to bring up a short list of all commits against a specific file:

> - *按文件搜索：*使用 `overview-manual/development-environment:git`，您可以输入以下命令以提取特定文件的所有提交的简短列表：

```

```
git shortlog -- filename
```

Just provide the name of the file for which you are interested. The information returned is not ordered by history but does include a list of everyone who has committed grouped by name. From the list, you can see who is responsible for the bulk of the changes against the file.

```

- *Examine the List of Mailing Lists:* For a list of the Yocto Project and related mailing lists, see the \"`Mailing lists <resources-mailinglist>`\" section in the Yocto Project Reference Manual.

> 检查邮件列表：要查看 Yocto 项目及相关邮件列表，请参阅 Yocto 项目参考手册中的“邮件列表<resources-mailinglist>”部分。

3. *Make a Pull Request:* Notify the maintainer or the mailing list that you have pushed a change by making a pull request.

   The Yocto Project provides two scripts that conveniently let you generate and send pull requests to the Yocto Project. These scripts are `create-pull-request` and `send-pull-request`. You can find these scripts in the `scripts` directory within the `Source Directory` (e.g. `poky/scripts`).

> 项目 Yocto 提供了两个脚本，可以方便地为 Yocto 项目生成和发送拉取请求。这些脚本是 `create-pull-request` 和 `send-pull-request`。您可以在源目录(例如 `poky/scripts`)的 `scripts` 目录中找到这些脚本。

Using these scripts correctly formats the requests without introducing any whitespace or HTML formatting. The maintainer that receives your patches either directly or through the mailing list needs to be able to save and apply them directly from your emails. Using these scripts is the preferred method for sending patches.

> 使用这些脚本正确格式化请求而不引入任何空白或 HTML 格式。接收补丁的维护者可以通过电子邮件直接或通过邮件列表接收到它们，并能够直接保存和应用它们。使用这些脚本是发送补丁的首选方法。

First, create the pull request. For example, the following command runs the script, specifies the upstream repository in the contrib directory into which you pushed the change, and provides a subject line in the created patch files:

> 首先，创建拉取请求。例如，以下命令运行脚本，指定推送所做更改的 contrib 目录中的上游存储库，并在创建的补丁文件中提供主题行：

```

$ poky/scripts/create-pull-request -u meta-intel-contrib -s "Updated Manual Section Reference in README"

```

Running this script forms `*.patch` files in a folder named `pull-`[PID] in the current directory. One of the patch files is a cover letter.

> 运行此脚本会在当前目录的名为 `pull-`[PID]的文件夹中形成 `*.patch` 文件。其中一个补丁文件是封面信。

Before running the `send-pull-request` script, you must edit the cover letter patch to insert information about your change. After editing the cover letter, send the pull request. For example, the following command runs the script and specifies the patch directory and email address. In this example, the email address is a mailing list:

> 在运行 `send-pull-request` 脚本之前，您必须编辑封面信补丁，以插入有关您的更改的信息。编辑完封面信后，发送拉取请求。例如，以下命令运行脚本并指定补丁目录和电子邮件地址。在此示例中，电子邮件地址是邮件列表：

```

$ poky/scripts/send-pull-request -p ~/meta-intel/pull-10565 -t meta-intel@lists.yoctoproject.orgmailto:meta-intel@lists.yoctoproject.org

```

You need to follow the prompts as the script is interactive.

::: note
::: title
Note
:::

For help on using these scripts, simply provide the `-h` argument as follows:

```

$ poky/scripts/create-pull-request -h
$ poky/scripts/send-pull-request -h

```

:::

## Responding to Patch Review

You may get feedback on your submitted patches from other community members or from the automated patchtest service. If issues are identified in your patch then it is usually necessary to address these before the patch will be accepted into the project. In this case you should amend the patch according to the feedback and submit an updated version to the relevant mailing list, copying in the reviewers who provided feedback to the previous version of the patch.

> 你可以从其他社区成员或自动补丁测试服务中获得对提交的补丁的反馈。如果在您的补丁中发现问题，通常需要在补丁被接受到项目中之前解决这些问题。在这种情况下，您应该根据反馈修改补丁，并将更新版本发送到相关邮件列表，并将提供反馈的审查者复制到前一个版本的补丁中。

The patch should be amended using `git commit --amend` or perhaps `git rebase` for more expert git users. You should also modify the `[PATCH]` tag in the email subject line when sending the revised patch to mark the new iteration as `[PATCH v2]`, `[PATCH v3]`, etc as appropriate. This can be done by passing the `-v` argument to `git format-patch` with a version number.

> 补丁应使用 `git commit --amend` 或者对于更有经验的 Git 用户来说可以使用 `git rebase` 来修改。发送修改后的补丁时，应该修改电子邮件主题行中的 `[PATCH]` 标记，以便将新的迭代标记为 `[PATCH v2]`、`[PATCH v3]` 等，可以通过传递 `-v` 参数给 `git format-patch`，并带有版本号来实现。

Lastly please ensure that you also test your revised changes. In particular please don\'t just edit the patch file written out by `git format-patch` and resend it.

> 最后，请确保您也测试了修订后的更改。特别是请不要只是编辑由 git format-patch 编写出的补丁文件，然后重新发送它。

## Submitting Changes to Stable Release Branches

The process for proposing changes to a Yocto Project stable branch differs from the steps described above. Changes to a stable branch must address identified bugs or CVEs and should be made carefully in order to avoid the risk of introducing new bugs or breaking backwards compatibility. Typically bug fixes must already be accepted into the master branch before they can be backported to a stable branch unless the bug in question does not affect the master branch or the fix on the master branch is unsuitable for backporting.

> 对 Yocto Project 稳定分支提出变更的过程与上述步骤不同。对稳定分支的变更必须解决已识别的错误或 CVE，并且应该小心翼翼地进行，以避免引入新的错误或破坏向后兼容性的风险。通常，除非错误在主分支上不受影响或主分支上的修复不适合回滚，否则必须在将错误修复接受到主分支之后才能将其回滚到稳定分支。

The list of stable branches along with the status and maintainer for each branch can be obtained from the :yocto_wiki:[Releases wiki page \</Releases\>].

> 可以从:yocto_wiki:[发行版维基页面 \</Releases\>]获得稳定分支的列表，以及每个分支的状态和维护者。

::: note
::: title
Note
:::

Changes will not typically be accepted for branches which are marked as End-Of-Life (EOL).
:::

With this in mind, the steps to submit a change for a stable branch are as follows:

1. *Identify the bug or CVE to be fixed:* This information should be collected so that it can be included in your submission.

   See `dev-manual/vulnerabilities:checking for vulnerabilities` for details about CVE tracking.
2. *Check if the fix is already present in the master branch:* This will result in the most straightforward path into the stable branch for the fix.

   1. *If the fix is present in the master branch \-\-- submit a backport request by email:* You should send an email to the relevant stable branch maintainer and the mailing list with details of the bug or CVE to be fixed, the commit hash on the master branch that fixes the issue and the stable branches which you would like this fix to be backported to.

> 如果在主分支中存在修复程序，请通过电子邮件提交回退请求：您应该向相关的稳定分支维护人员和邮件列表发送电子邮件，其中包含要修复的错误或 CVE 的详细信息，主分支上修复该问题的提交哈希值以及您希望此修复程序回退到哪些稳定分支。

2. *If the fix is not present in the master branch \-\-- submit the fix to the master branch first:* This will ensure that the fix passes through the project\'s usual patch review and test processes before being accepted. It will also ensure that bugs are not left unresolved in the master branch itself. Once the fix is accepted in the master branch a backport request can be submitted as above.

> 如果修复不在主分支中存在，请先提交修复到主分支：这将确保修复经过项目通常的补丁审查和测试过程，然后才能被接受。这也将确保错误不会在主分支本身留下未解决的问题。一旦修复在主分支中被接受，就可以提交上述回滚请求。

3. *If the fix is unsuitable for the master branch \-\-- submit a patch directly for the stable branch:* This method should be considered as a last resort. It is typically necessary when the master branch is using a newer version of the software which includes an upstream fix for the issue or when the issue has been fixed on the master branch in a way that introduces backwards incompatible changes. In this case follow the steps in `dev-manual/changes:preparing changes for submission` but modify the subject header of your patch email to include the name of the stable branch which you are targetting. This can be done using the `--subject-prefix` argument to `git format-patch`, for example to submit a patch to the dunfell branch use `git format-patch --subject-prefix='&DISTRO_NAME_NO_CAP_MINUS_ONE;][PATCH' ...`.

> 3. *如果修复对于主分支不适用----直接为稳定分支提交补丁：* 这种方法应该被视为最后的手段。通常需要当主分支使用软件的新版本，其中包括了对该问题的上游修复，或者当该问题在主分支上以引入向后不兼容变更的方式被修复时。在这种情况下，遵循 `dev-manual/changes:preparing changes for submission`中的步骤，但是修改你补丁邮件的主题头，包括你针对的稳定分支的名称。这可以使用 `git format-patch` 的 `--subject-prefix` 参数完成，例如提交一个补丁到 dunfell 分支，使用 `git format-patch --subject-prefix='&DISTRO_NAME_NO_CAP_MINUS_ONE;][PATCH' ...`。
```
