---
tip: translate by openai@2023-06-07 20:59:35
...
---
title: Project Testing and Release Process
------------------------------------------

# Day to Day Development

This section details how the project tests changes, through automation on the Autobuilder or with the assistance of QA teams, through to making releases.

> 这一节详细介绍了项目如何通过自动构建器上的自动化测试或者 QA 团队的协助，进行变更测试，并最终发布。

The project aims to test changes against our test matrix before those changes are merged into the master branch. As such, changes are queued up in batches either in the `master-next` branch in the main trees, or in user trees such as `ross/mut` in `poky-contrib` (Ross Burton helps review and test patches and this is his testing tree).

> 项目的目标是在将更改合并到主分支之前，对我们的测试矩阵进行测试。因此，更改会排队在主树中的 `master-next` 分支中，或者在用户树中，如 `poky-contrib` 中的 `ross/mut`（Ross Burton 帮助审核和测试补丁，这是他的测试树）。

We have two broad categories of test builds, including \"full\" and \"quick\". On the Autobuilder, these can be seen as \"a-quick\" and \"a-full\", simply for ease of sorting in the UI. Use our Autobuilder console view to see where me manage most test-related items, available at: :yocto_ab:[/typhoon/#/console]{.title-ref}.

> 我们有两大类测试构建，包括“完整”和“快速”。在自动构建器上，这些可以看作是“a-quick”和“a-full”，仅仅是为了方便在用户界面中排序。使用我们的自动构建器控制台视图可以看到我们管理大多数测试相关项目，可在以下网址获取：:yocto_ab:[/typhoon/#/console]{.title-ref}。

Builds are triggered manually when the test branches are ready. The builds are monitored by the SWAT team. For additional information, see :yocto_wiki:[/Yocto_Build_Failure_Swat_Team]{.title-ref}. If successful, the changes would usually be merged to the `master` branch. If not successful, someone would respond to the changes on the mailing list explaining that there was a failure in testing. The choice of quick or full would depend on the type of changes and the speed with which the result was required.

> 构建在测试分支准备就绪时会被手动触发。构建由 SWAT 团队监控。有关更多信息，请参见：yocto_wiki:[Yocto_Build_Failure_Swat_Team]{.title-ref}。如果成功，通常会将更改合并到 `master` 分支。如果不成功，有人会在邮件列表上回复更改，解释测试失败。快速或完整的选择取决于更改的类型以及所需结果的速度。

The Autobuilder does build the `master` branch once daily for several reasons, in particular, to ensure the current `master` branch does build, but also to keep `yocto-testresults` (:yocto\_[git:%60/yocto-testresults/](git:%60/yocto-testresults/)[), buildhistory (:yocto_git:]{.title-ref}/poky-buildhistory/\`), and our sstate up to date. On the weekend, there is a master-next build instead to ensure the test results are updated for the less frequently run targets.

> 自动构建器每天会构建 `master` 分支，这是出于几个原因，尤其是确保当前 `master` 分支可以构建，同时也要保持 `yocto-testresults`（:yocto_[git:%60/yocto-testresults/](git:%60/yocto-testresults/)[）、buildhistory（:yocto_git:]{.title-ref}/poky-buildhistory/\`）和我们的 sstate 保持最新。周末会构建 master-next，以确保测试结果对较少运行的目标也是最新的。

Performance builds (buildperf-\* targets in the console) are triggered separately every six hours and automatically push their results to the buildstats repository at: :yocto\_[git:%60/yocto-buildstats/](git:%60/yocto-buildstats/)\`.

> 性能构建（控制台中的 buildperf-*目标）每六小时触发一次，并自动将结果推送到 buildstats 存储库：yocto_[git:%60/yocto-buildstats/](git:%60/yocto-buildstats/)。

The \'quick\' targets have been selected to be the ones which catch the most failures or give the most valuable data. We run \'fast\' ptests in this case for example but not the ones which take a long time. The quick target doesn\'t include \*-lsb builds for all architectures, some world builds and doesn\'t trigger performance tests or ltp testing. The full build includes all these things and is slower but more comprehensive.

> 选择了“快速”目标，以捕获最多的故障或提供最有价值的数据。例如，我们在这种情况下运行“快速”ptests，但不是那些耗时较长的 ptests。快速目标不包括所有体系结构的\*-lsb 构建，一些世界构建，也不触发性能测试或 LTP 测试。完整构建包括所有这些内容，速度较慢，但更全面。

# Release Builds

The project typically has two major releases a year with a six month cadence in April and October. Between these there would be a number of milestone releases (usually four) with the final one being stabilization only along with point releases of our stable branches.

> 项目通常每年有两个主要发布，每六个月在四月和十月之间进行一次。在这之间，将有许多里程碑发布（通常有四个），最后一个是只进行稳定性发布，以及我们稳定分支的点发布。

The build and release process for these project releases is similar to that in `test-manual/test-process:day to day development`{.interpreted-text role="ref"}, in that the a-full target of the Autobuilder is used but in addition the form is configured to generate and publish artifacts and the milestone number, version, release candidate number and other information is entered. The box to \"generate an email to QA\"is also checked.

> 为了发布这些项目，构建和发布过程与“test-manual/test-process：日常开发”中的相似，即使用 Autobuilder 的 a-full 目标，但另外还配置了表单以生成和发布构件以及里程碑号、版本号、发布候选号和其他信息。还要勾选“向 QA 发送电子邮件”的框。

When the build completes, an email is sent out using the send-qa-email script in the `yocto-autobuilder-helper` repository to the list of people configured for that release. Release builds are placed into a directory in [https://autobuilder.yocto.io/pub/releases](https://autobuilder.yocto.io/pub/releases) on the Autobuilder which is included in the email. The process from here is more manual and control is effectively passed to release engineering. The next steps include:

> 当构建完成时，会使用 `yocto-autobuilder-helper` 存储库中的 send-qa-email 脚本发送一封电子邮件到配置该发行版的人员列表。发行版构建将放入 Autobuilder 中 [https://autobuilder.yocto.io/pub/releases](https://autobuilder.yocto.io/pub/releases) 目录，并包含在电子邮件中。从这里开始，过程更加手动化，控制权实际上转交给发布工程师。下一步包括：

- QA teams respond to the email saying which tests they plan to run and when the results will be available.

> QA 团队回复邮件，说明他们计划运行哪些测试，以及结果何时可用。

- QA teams run their tests and share their results in the yocto-testresults-contrib repository, along with a summary of their findings.

> QA 团队在 yocto-testresults-contrib 仓库中运行他们的测试并共享他们的结果，以及他们发现的总结。

- Release engineering prepare the release as per their process.
- Test results from the QA teams are included into the release in separate directories and also uploaded to the yocto-testresults repository alongside the other test results for the given revision.

> 测试结果由 QA 团队收集，分别放入发布的单独目录中，并且也上传到 yocto-testresults 仓库里，与给定版本的其他测试结果一起。

- The QA report in the final release is regenerated using resulttool to include the new test results and the test summaries from the teams (as headers to the generated report).

> 报告在最终发布中使用 resulttool 重新生成，以包括新的测试结果和来自团队的测试摘要（作为生成报告的标题）。

- The release is checked against the release checklist and release readiness criteria.
- A final decision on whether to release is made by the YP TSC who have final oversight on release readiness.

> 最终是否发布的决定由 YP TSC 做出，他们对发布准备情况有最终监督。
