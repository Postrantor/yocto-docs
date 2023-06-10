---
tip: translate by openai@2023-06-10 12:34:59
...
---
title: Upgrading Recipes
------------------------

Over time, upstream developers publish new versions for software built by layer recipes. It is recommended to keep recipes up-to-date with upstream version releases.

> 随着时间的推移，上游开发者发布由层配方构建的新版本的软件。建议保持配方与上游版本发布保持同步。

While there are several methods to upgrade a recipe, you might consider checking on the upgrade status of a recipe first. You can do so using the `devtool check-upgrade-status` command. See the \"`devtool-checking-on-the-upgrade-status-of-a-recipe`\" section in the Yocto Project Reference Manual for more information.

> 当有几种方法可以升级 recipes 时，您可以首先检查 recipes 的升级状态。您可以使用 `devtool check-upgrade-status` 命令来完成。有关更多信息，请参阅 Yocto Project 参考手册中的“`devtool-checking-on-the-upgrade-status-of-a-recipe`”部分。

The remainder of this section describes three ways you can upgrade a recipe. You can use the Automated Upgrade Helper (AUH) to set up automatic version upgrades. Alternatively, you can use `devtool upgrade` to set up semi-automatic version upgrades. Finally, you can manually upgrade a recipe by editing the recipe itself.

> 本节剩余部分描述了三种方法可以升级 recipes。您可以使用自动升级助手(AUH)来设置自动版本升级。或者，您可以使用 `devtool upgrade` 来设置半自动版本升级。最后，您可以通过编辑 recipes 本身来手动升级 recipes。

# Using the Auto Upgrade Helper (AUH)

The AUH utility works in conjunction with the OpenEmbedded build system in order to automatically generate upgrades for recipes based on new versions being published upstream. Use AUH when you want to create a service that performs the upgrades automatically and optionally sends you an email with the results.

> AUH 实用程序与 OpenEmbedded 构建系统配合使用，以便根据上游发布的新版本自动生成配方升级。当您想要创建一个自动执行升级并可选择发送您结果的电子邮件的服务时，请使用 AUH。

AUH allows you to update several recipes with a single use. You can also optionally perform build and integration tests using images with the results saved to your hard drive and emails of results optionally sent to recipe maintainers. Finally, AUH creates Git commits with appropriate commit messages in the layer\'s tree for the changes made to recipes.

> AUH 允许您一次性更新多个配方。您还可以使用镜像选择性地执行构建和集成测试，将结果保存到硬盘，并可选择将测试结果发送到配方维护者的电子邮件。最后，AUH 会为图层树中所做的配方更改创建具有适当提交消息的 Git 提交。

::: note
::: title
Note
:::

In some conditions, you should not use AUH to upgrade recipes and should instead use either `devtool upgrade` or upgrade your recipes manually:

- When AUH cannot complete the upgrade sequence. This situation usually results because custom patches carried by the recipe cannot be automatically rebased to the new version. In this case, `devtool upgrade` allows you to manually resolve conflicts.

> 当 AUH 无法完成升级序列时，这种情况通常是由于 recipes 中的自定义补丁无法自动重新基于新版本而导致的。在这种情况下，`devtool upgrade` 允许您手动解决冲突。

- When for any reason you want fuller control over the upgrade process. For example, when you want special arrangements for testing.
  :::

The following steps describe how to set up the AUH utility:

1. *Be Sure the Development Host is Set Up:* You need to be sure that your development host is set up to use the Yocto Project. For information on how to set up your host, see the \"`dev-manual/start:Preparing the Build Host`\" section.

> 确保开发主机已设置：您需要确保您的开发主机已设置好以使用 Yocto 项目。有关如何设置主机的信息，请参阅“dev-manual/start：准备构建主机”部分。

2. *Make Sure Git is Configured:* The AUH utility requires Git to be configured because AUH uses Git to save upgrades. Thus, you must have Git user and email configured. The following command shows your configurations:

> 确保 Git 已配置：AUH 实用程序需要 Git 配置，因为 AUH 使用 Git 保存升级。因此，您必须配置 Git 用户和电子邮件。以下命令显示您的配置：

```
$ git config --list
```

If you do not have the user and email configured, you can use the following commands to do so:

```
$ git config --global user.name some_name
$ git config --global user.email username@domain.com
```

3. *Clone the AUH Repository:* To use AUH, you must clone the repository onto your development host. The following command uses Git to create a local copy of the repository on your system:

> 3. *克隆 AUH 仓库：*要使用 AUH，您必须将存储库克隆到开发主机上。以下命令使用 Git 在您的系统上创建存储库的本地副本：

```
$ git clone  git://git.yoctoproject.org/auto-upgrade-helper
Cloning into 'auto-upgrade-helper'... remote: Counting objects: 768, done.
remote: Compressing objects: 100% (300/300), done.
remote: Total 768 (delta 499), reused 703 (delta 434)
Receiving objects: 100% (768/768), 191.47 KiB | 98.00 KiB/s, done.
Resolving deltas: 100% (499/499), done.
Checking connectivity... done.
```

AUH is not part of the `OpenEmbedded-Core (OE-Core)` repositories.

4. *Create a Dedicated Build Directory:* Run the `structure-core-script` that you use exclusively for running the AUH utility:

> 4. *创建专用构建目录：*运行 `structure-core-script`：

```
$ cd poky
$ source oe-init-build-env your_AUH_build_directory
```

Re-using an existing `Build Directory` and its configurations is not recommended as existing settings could cause AUH to fail or behave undesirably.

> 重复使用现有的构建目录及其配置不推荐，因为现有设置可能会导致 AUH 失败或表现不佳。

5. *Make Configurations in Your Local Configuration File:* Several settings are needed in the `local.conf` file in the build directory you just created for AUH. Make these following configurations:

> 5. *在本地配置文件中进行配置：* 在您刚刚为 AUH 创建的构建目录中的 `local.conf` 文件中需要设置几个设置。进行以下配置：

- If you want to enable `Build History <dev-manual/build-quality:maintaining build output quality>`, which is optional, you need the following lines in the `conf/local.conf` file:

> 如果你想启用可选的“构建历史 <dev-manual/build-quality:maintaining build output quality>”，你需要在 `conf/local.conf` 文件中添加以下行：

```
```

INHERIT =+ "buildhistory"
BUILDHISTORY_COMMIT = "1"

```

With this configuration and a successful upgrade, a build history \"diff\" file appears in the `upgrade-helper/work/recipe/buildhistory-diff.txt` file found in your `Build Directory`.
```

- If you want to enable testing through the `ref-classes-testimage` class, which is optional, you need to have the following set in your `conf/local.conf` file:

> 如果你想通过 `ref-classes-testimage` 类启用测试(这是可选的)，你需要在你的 `conf/local.conf` 文件中设置以下内容：

```
```

INHERIT += "testimage"

```

::: note
::: title
Note
:::

If your distro does not enable by default ptest, which Poky does, you need the following in your `local.conf` file:

```

DISTRO_FEATURES:append = " ptest"

```

:::
```

6. *Optionally Start a vncserver:* If you are running in a server without an X11 session, you need to start a vncserver:

   ```
   $ vncserver :1
   $ export DISPLAY=:1
   ```
7. *Create and Edit an AUH Configuration File:* You need to have the `upgrade-helper/upgrade-helper.conf` configuration file in your `Build Directory`. You can find a sample configuration file in the :yocto_[git:%60AUH](git:%60AUH) source repository \</auto-upgrade-helper/tree/\>\`.

> 您需要在构建目录中拥有 `upgrade-helper/upgrade-helper.conf` 配置文件。您可以在 yocto_[git:`AUH`](git:%60AUH%60)源代码库中找到一个示例配置文件 </auto-upgrade-helper/tree/>`.

Read through the sample file and make configurations as needed. For example, if you enabled build history in your `local.conf` as described earlier, you must enable it in `upgrade-helper.conf`.

> 读取样本文件，根据需要进行配置。例如，如果您在 `local.conf` 中像前面描述的那样启用了构建历史记录，则必须在 `upgrade-helper.conf` 中启用它。

Also, if you are using the default `maintainers.inc` file supplied with Poky and located in `meta-yocto` and you do not set a \"maintainers_whitelist\" or \"global_maintainer_override\" in the `upgrade-helper.conf` configuration, and you specify \"-e all\" on the AUH command-line, the utility automatically sends out emails to all the default maintainers. Please avoid this.

> 如果您使用 Poky 默认提供的位于 meta-yocto 中的 `maintainers.inc` 文件，并且您没有在 `upgrade-helper.conf` 配置中设置“maintainers_whitelist”或“global_maintainer_override”，并且您在 AUH 命令行上指定“-e all”，则该实用程序会自动向所有默认维护者发送电子邮件。请避免这种情况。

This next set of examples describes how to use the AUH:

- *Upgrading a Specific Recipe:* To upgrade a specific recipe, use the following form:

  ```
  $ upgrade-helper.py recipe_name
  ```

  For example, this command upgrades the `xmodmap` recipe:

  ```
  $ upgrade-helper.py xmodmap
  ```
- *Upgrading a Specific Recipe to a Particular Version:* To upgrade a specific recipe to a particular version, use the following form:

  ```
  $ upgrade-helper.py recipe_name -t version
  ```

  For example, this command upgrades the `xmodmap` recipe to version 1.2.3:

  ```
  $ upgrade-helper.py xmodmap -t 1.2.3
  ```
- *Upgrading all Recipes to the Latest Versions and Suppressing Email Notifications:* To upgrade all recipes to their most recent versions and suppress the email notifications, use the following command:

> 升级所有配方到最新版本并禁止发送电子邮件通知：要升级所有配方到最新版本并禁止发送电子邮件通知，请使用以下命令：

```
$ upgrade-helper.py all
```

- *Upgrading all Recipes to the Latest Versions and Send Email Notifications:* To upgrade all recipes to their most recent versions and send email messages to maintainers for each attempted recipe as well as a status email, use the following command:

> 升级所有配方到最新版本并发送电子邮件通知：要将所有配方升级到最新版本，并为每个尝试的配方以及状态电子邮件发送电子邮件消息，请使用以下命令：

```
$ upgrade-helper.py -e all
```

Once you have run the AUH utility, you can find the results in the AUH `Build Directory`:

```
$/upgrade-helper/timestamp
```

The AUH utility also creates recipe update commits from successful upgrade attempts in the layer tree.

You can easily set up to run the AUH utility on a regular basis by using a cron job. See the :yocto_[git:%60weeklyjob.sh](git:%60weeklyjob.sh) \</auto-upgrade-helper/tree/weeklyjob.sh\>\` file distributed with the utility for an example.

> 你可以通过使用 cron 作业来轻松地设置定期运行 AUH 实用程序。请参阅随实用程序一起分发的:yocto_[git:`weeklyjob.sh`](git:%60weeklyjob.sh%60)</auto-upgrade-helper/tree/weeklyjob.sh> 文件以获取示例。

# Using `devtool upgrade`

As mentioned earlier, an alternative method for upgrading recipes to newer versions is to use `devtool upgrade </ref-manual/devtool-reference>`\" section in the Yocto Project Application Development and the Extensible Software Development Kit (eSDK) Manual.

> 正如之前提到的，升级 recipes 到新版本的另一种方法是使用 `devtool upgrade`(参见参考手册/devtool-reference)。您可以在 Yocto 项目应用开发和可扩展软件开发工具包(eSDK)手册中的“sdk-manual/extensible：使用 `devtool upgrade` 来创建支持软件的新版本的 recipes”部分中了解有关 `devtool upgrade` 的更多信息。

To see all the command-line options available with `devtool upgrade`, use the following help command:

```
$ devtool upgrade -h
```

If you want to find out what version a recipe is currently at upstream without any attempt to upgrade your local version of the recipe, you can use the following command:

> 如果你想查看源头上当前的 recipes 版本，而不尝试升级本地 recipes 版本，你可以使用以下命令：

```
$ devtool latest-version recipe_name
```

As mentioned in the previous section describing AUH, `devtool upgrade` works in a less-automated manner than AUH. Specifically, `devtool upgrade` only works on a single recipe that you name on the command line, cannot perform build and integration testing using images, and does not automatically generate commits for changes in the source tree. Despite all these \"limitations\", `devtool upgrade` updates the recipe file to the new upstream version and attempts to rebase custom patches contained by the recipe as needed.

> 在前面描述 AUH 的部分中提到，`devtool upgrade` 的工作方式比 AUH 更不自动化。具体来说，`devtool upgrade` 只能在命令行上指定的单个配方上工作，无法使用镜像进行构建和集成测试，也不会自动生成源树中的更改提交。尽管存在这些“限制”，`devtool upgrade` 仍将配方文件更新到新的上游版本，并尝试根据需要重新基础配方中包含的自定义补丁。

::: note
::: title
Note
:::

AUH uses much of `devtool upgrade` behind the scenes making AUH somewhat of a \"wrapper\" application for `devtool upgrade`.
:::

A typical scenario involves having used Git to clone an upstream repository that you use during build operations. Because you have built the recipe in the past, the layer is likely added to your configuration already. If for some reason, the layer is not added, you could add it easily using the \"``bitbake-layers <bsp-guide/bsp:creating a new bsp layer using the \`\`bitbake-layers\`\` script>``\" script. For example, suppose you use the `nano.bb` recipe from the `meta-oe` layer in the `meta-openembedded` repository. For this example, assume that the layer has been cloned into following area:

> 一个典型的场景包括使用 Git 克隆上游存储库，用于构建操作。由于您以前构建了该配方，因此层可能已添加到您的配置中。如果由于某种原因未添加该层，您可以使用“bitbake-layers”脚本轻松添加它。例如，假设您使用 meta-openembedded 存储库中 meta-oe 层中的 nano.bb 配方。对于此示例，假设该层已克隆到以下区域：

```
/home/scottrif/meta-openembedded
```

The following command from your `Build Directory`/conf/bblayers.conf`):

> 以下命令来自您的“构建目录”，将层添加到构建配置(即 `$/conf/bblayers.conf`)：

```
$ bitbake-layers add-layer /home/scottrif/meta-openembedded/meta-oe
NOTE: Starting bitbake server...
Parsing recipes: 100% |##########################################| Time: 0:00:55
Parsing of 1431 .bb files complete (0 cached, 1431 parsed). 2040 targets, 56 skipped, 0 masked, 0 errors.
Removing 12 recipes from the x86_64 sysroot: 100% |##############| Time: 0:00:00
Removing 1 recipes from the x86_64_i586 sysroot: 100% |##########| Time: 0:00:00
Removing 5 recipes from the i586 sysroot: 100% |#################| Time: 0:00:00
Removing 5 recipes from the qemux86 sysroot: 100% |##############| Time: 0:00:00
```

For this example, assume that the `nano.bb` recipe that is upstream has a 2.9.3 version number. However, the version in the local repository is 2.7.4. The following command from your build directory automatically upgrades the recipe for you:

> 对于这个例子，假设上游的 `nano.bb` recipes 有一个 2.9.3 的版本号。但是本地仓库中的版本是 2.7.4。以下来自您的构建目录的命令可以自动为您升级 recipes：

```
$ devtool upgrade nano -V 2.9.3
NOTE: Starting bitbake server...
NOTE: Creating workspace layer in /home/scottrif/poky/build/workspace
Parsing recipes: 100% |##########################################| Time: 0:00:46
Parsing of 1431 .bb files complete (0 cached, 1431 parsed). 2040 targets, 56 skipped, 0 masked, 0 errors.
NOTE: Extracting current version source...
NOTE: Resolving any missing task queue dependencies
       .
       .
       .
NOTE: Executing SetScene Tasks
NOTE: Executing RunQueue Tasks
NOTE: Tasks Summary: Attempted 74 tasks of which 72 didn't need to be rerun and all succeeded.
Adding changed files: 100% |#####################################| Time: 0:00:00
NOTE: Upgraded source extracted to /home/scottrif/poky/build/workspace/sources/nano
NOTE: New recipe is /home/scottrif/poky/build/workspace/recipes/nano/nano_2.9.3.bb
```

::: note
::: title
Note
:::

Using the `-V` option is not necessary. Omitting the version number causes `devtool upgrade` to upgrade the recipe to the most recent version.
:::

Continuing with this example, you can use `devtool build` to build the newly upgraded recipe:

```
$ devtool build nano
NOTE: Starting bitbake server...
Loading cache: 100% |################################################################################################| Time: 0:00:01
Loaded 2040 entries from dependency cache.
Parsing recipes: 100% |##############################################################################################| Time: 0:00:00
Parsing of 1432 .bb files complete (1431 cached, 1 parsed). 2041 targets, 56 skipped, 0 masked, 0 errors.
NOTE: Resolving any missing task queue dependencies
       .
       .
       .
NOTE: Executing SetScene Tasks
NOTE: Executing RunQueue Tasks
NOTE: nano: compiling from external source tree /home/scottrif/poky/build/workspace/sources/nano
NOTE: Tasks Summary: Attempted 520 tasks of which 304 didn't need to be rerun and all succeeded.
```

Within the `devtool upgrade` workflow, you can deploy and test your rebuilt software. For this example, however, running `devtool finish` cleans up the workspace once the source in your workspace is clean. This usually means using Git to stage and submit commits for the changes generated by the upgrade process.

> 在 `devtool upgrade` 工作流程中，您可以部署和测试重建的软件。但是，对于本例，运行 `devtool finish` 会在工作区中的源代码清理完之后清理工作区。这通常意味着使用 Git 来阶段和提交升级过程生成的更改的提交。

Once the tree is clean, you can clean things up in this example with the following command from the `$/workspace/sources/nano` directory:

```
$ devtool finish nano meta-oe
NOTE: Starting bitbake server...
Loading cache: 100% |################################################################################################| Time: 0:00:00
Loaded 2040 entries from dependency cache.
Parsing recipes: 100% |##############################################################################################| Time: 0:00:01
Parsing of 1432 .bb files complete (1431 cached, 1 parsed). 2041 targets, 56 skipped, 0 masked, 0 errors.
NOTE: Adding new patch 0001-nano.bb-Stuff-I-changed-when-upgrading-nano.bb.patch
NOTE: Updating recipe nano_2.9.3.bb
NOTE: Removing file /home/scottrif/meta-openembedded/meta-oe/recipes-support/nano/nano_2.7.4.bb
NOTE: Moving recipe file to /home/scottrif/meta-openembedded/meta-oe/recipes-support/nano
NOTE: Leaving source tree /home/scottrif/poky/build/workspace/sources/nano as-is; if you no longer need it then please delete it manually
```

Using the `devtool finish` command cleans up the workspace and creates a patch file based on your commits. The tool puts all patch files back into the source directory in a sub-directory named `nano` in this case.

> 使用 `devtool finish` 命令可以清理工作区，并基于您的提交创建补丁文件。该工具将所有补丁文件放回源目录的 `nano` 子目录中。

# Manually Upgrading a Recipe

If for some reason you choose not to upgrade recipes using `dev-manual/upgrading-recipes:Using the Auto Upgrade Helper (AUH)`, you can manually edit the recipe files to upgrade the versions.

> 如果出于某种原因你不使用「自动升级助手(AUH)」(dev-manual/upgrading-recipes:Using the Auto Upgrade Helper (AUH))或者「devtool upgrade」(dev-manual/upgrading-recipes:Using ``devtool upgrade``)来升级 recipes，你可以手动编辑 recipes 文件来升级版本。

::: note
::: title
Note
:::

Manually updating multiple recipes scales poorly and involves many steps. The recommendation to upgrade recipe versions is through AUH or `devtool upgrade`, both of which automate some steps and provide guidance for others needed for the manual process.

> 手动更新多个配方的规模不佳，需要经历许多步骤。建议通过 AUH 或“devtool upgrade”来升级配方版本，这两者都可以自动化一些步骤，并为手动进程提供指导。
> :::

To manually upgrade recipe versions, follow these general steps:

1. *Change the Version:* Rename the recipe such that the version (i.e. the `PV` within the recipe itself.

> 1. *更改版本：*重命名 recipes，使版本(即 recipes 名称中的 `PV` 的值。

2. *Update* `SRCREV` to point to the commit hash that matches the new version.

> 如果需要，更新 `SRCREV`：如果你的配方从 Git 或其他版本控制系统获取源代码，请将 `SRCREV` 更新为与新版本匹配的提交哈希值。

3. *Build the Software:* Try to build the recipe using BitBake. Typical build failures include the following:

   - License statements were updated for the new version. For this case, you need to review any changes to the license and update the values of `LICENSE` as needed.

> 在新版本中更新了许可声明。在这种情况下，您需要查看对许可证的任何更改，并根据需要更新 `LICENSE` 的值。

```
 ::: note
 ::: title
 Note
 :::

 License changes are often inconsequential. For example, the license text\'s copyright year might have changed.
 :::
```

- Custom patches carried by the older version of the recipe might fail to apply to the new version. For these cases, you need to review the failures. Patches might not be necessary for the new version of the software if the upgraded version has fixed those issues. If a patch is necessary and failing, you need to rebase it into the new version.

> 较旧版本的配方所携带的自定义补丁可能无法应用于新版本。对于这些情况，您需要查看失败。如果升级版本已解决了这些问题，则可能不需要新版本的补丁。如果需要补丁并且失败，则需要将其重新基于新版本。

4. *Optionally Attempt to Build for Several Architectures:* Once you successfully build the new software for a given architecture, you could test the build for other architectures by changing the `MACHINE` variable and rebuilding the software. This optional step is especially important if the recipe is to be released publicly.

> *可选择尝试编译多种体系结构：* 一旦为给定体系结构成功构建新软件，可以通过更改 `MACHINE` 变量并重新构建软件来测试其他体系结构的构建。如果要公开发布配方，则此可选步骤尤为重要。

5. *Check the Upstream Change Log or Release Notes:* Checking both these reveals if there are new features that could break backwards-compatibility. If so, you need to take steps to mitigate or eliminate that situation.

> 检查上游变更日志或发行说明：检查这两者可以查看是否有可能破坏向后兼容性的新功能。如果有，你需要采取措施来减轻或消除这种情况。

6. *Optionally Create a Bootable Image and Test:* If you want, you can test the new software by booting it onto actual hardware.
7. *Create a Commit with the Change in the Layer Repository:* After all builds work and any testing is successful, you can create commits for any changes in the layer holding your upgraded recipe.

> 在所有构建都能够正常工作，以及任何测试都成功之后，您可以为包含您升级的配方的层中的任何更改创建提交。
