---
tip: translate by baidu@2023-06-07 17:18:06
...
---
title: Upgrading Recipes
------------------------

Over time, upstream developers publish new versions for software built by layer recipes. It is recommended to keep recipes up-to-date with upstream version releases.

> 随着时间的推移，上游开发人员发布了按层配方构建的软件的新版本。建议将配方与上游版本保持同步。

While there are several methods to upgrade a recipe, you might consider checking on the upgrade status of a recipe first. You can do so using the `devtool check-upgrade-status` command. See the \"`devtool-checking-on-the-upgrade-status-of-a-recipe`{.interpreted-text role="ref"}\" section in the Yocto Project Reference Manual for more information.

> 虽然有几种方法可以升级配方，但您可以考虑先检查配方的升级状态。您可以使用“devtool check upgrade status”命令执行此操作。有关详细信息，请参阅 Yocto 项目参考手册中的“`devtool-checking-on-upgrade-status-of-a-precipe`｛.explored text role=“ref”｝”一节。

The remainder of this section describes three ways you can upgrade a recipe. You can use the Automated Upgrade Helper (AUH) to set up automatic version upgrades. Alternatively, you can use `devtool upgrade` to set up semi-automatic version upgrades. Finally, you can manually upgrade a recipe by editing the recipe itself.

> 本节的其余部分介绍了升级配方的三种方法。您可以使用自动升级帮助程序（AUH）设置自动版本升级。或者，您可以使用“devtool upgrade”设置半自动版本升级。最后，您可以通过编辑配方本身手动升级配方。

# Using the Auto Upgrade Helper (AUH)

The AUH utility works in conjunction with the OpenEmbedded build system in order to automatically generate upgrades for recipes based on new versions being published upstream. Use AUH when you want to create a service that performs the upgrades automatically and optionally sends you an email with the results.

> AUH 实用程序与 OpenEmbedded 构建系统协同工作，以便根据上游发布的新版本自动生成配方升级。当您想创建一个自动执行升级的服务，并可选择向您发送一封包含结果的电子邮件时，请使用 AUH。

AUH allows you to update several recipes with a single use. You can also optionally perform build and integration tests using images with the results saved to your hard drive and emails of results optionally sent to recipe maintainers. Finally, AUH creates Git commits with appropriate commit messages in the layer\'s tree for the changes made to recipes.

> AUH 允许您一次性更新多个食谱。您还可以选择使用保存到硬盘驱动器的图像和发送给配方维护人员的结果电子邮件来执行构建和集成测试。最后，AUH 在层的树中为配方的更改创建具有适当提交消息的 Git 提交。

::: note
::: title
Note
:::

In some conditions, you should not use AUH to upgrade recipes and should instead use either `devtool upgrade` or upgrade your recipes manually:

> 在某些情况下，您不应使用 AUH 来升级配方，而应使用“devtool upgrade”或手动升级配方：

- When AUH cannot complete the upgrade sequence. This situation usually results because custom patches carried by the recipe cannot be automatically rebased to the new version. In this case, `devtool upgrade` allows you to manually resolve conflicts.

> -当 AUH 无法完成升级序列时。这种情况通常是由于配方携带的自定义补丁无法自动重新基于新版本而导致的。在这种情况下，“devtool upgrade”允许您手动解决冲突。

- When for any reason you want fuller control over the upgrade process. For example, when you want special arrangements for testing.

> -无论出于何种原因，您都希望对升级过程进行更全面的控制。例如，当您需要特殊的测试安排时。
> :::

The following steps describe how to set up the AUH utility:

> 以下步骤介绍了如何设置 AUH 实用程序：

1. *Be Sure the Development Host is Set Up:* You need to be sure that your development host is set up to use the Yocto Project. For information on how to set up your host, see the \"`dev-manual/start:Preparing the Build Host`{.interpreted-text role="ref"}\" section.

> 1.*确保开发主机已经设置好：*您需要确保您的开发主机已设置好使用 Yocto 项目。有关如何设置主机的信息，请参阅\“`dev manual/start:准备生成主机`｛.depreted text role=”ref“｝\”一节。

2. *Make Sure Git is Configured:* The AUH utility requires Git to be configured because AUH uses Git to save upgrades. Thus, you must have Git user and email configured. The following command shows your configurations:

> 2.*确保配置了 Git：*AUH 实用程序需要配置 Git，因为 AUH 使用 Git 来保存升级。因此，您必须配置 Git 用户和电子邮件。以下命令显示您的配置：

```

> ```

$ git config --list

> $git配置--列表

```

> ```
> ```

If you do not have the user and email configured, you can use the following commands to do so:

> 如果没有配置用户和电子邮件，可以使用以下命令进行配置：

```

> ```

$ git config --global user.name some_name

> $git-config--全局用户名some_name

$ git config --global user.email username@domain.com

> $git-config--全局用户电子邮件username@domain.com

```

> ```
> ```

3. *Clone the AUH Repository:* To use AUH, you must clone the repository onto your development host. The following command uses Git to create a local copy of the repository on your system:

> 3.*克隆 AUH 存储库：*要使用 AUH，必须将存储库克隆到开发主机上。以下命令使用 Git 在系统上创建存储库的本地副本：

```

> ```

$ git clone  git://git.yoctoproject.org/auto-upgrade-helper

> $git克隆git://git.yoctoproject.org/auto-upgrade-helper

Cloning into 'auto-upgrade-helper'... remote: Counting objects: 768, done.

> 正在克隆到“自动升级帮助程序”中。。。remote：计数对象：768，已完成。

remote: Compressing objects: 100% (300/300), done.

> 远程：压缩对象：100%（300/300），已完成。

remote: Total 768 (delta 499), reused 703 (delta 434)

> 远程：总计768（增量499），重复使用703（增量434）

Receiving objects: 100% (768/768), 191.47 KiB | 98.00 KiB/s, done.

> 接收对象：100%（768/768），191.47 KiB|98.00 KiB/s，已完成。

Resolving deltas: 100% (499/499), done.

> 解决增量：100%（499/499），已完成。

Checking connectivity... done.

> 正在检查连接。。。完成。

```

> ```
> ```

AUH is not part of the `OpenEmbedded-Core (OE-Core)`{.interpreted-text role="term"} or `Poky`{.interpreted-text role="term"} repositories.

> AUH 不属于 `OpenEmbedded Core（OE Core）`{.expreted text role=“term”}或 `Poky`{.expreded text role=“term”}存储库的一部分。

4. *Create a Dedicated Build Directory:* Run the `structure-core-script`{.interpreted-text role="ref"} script to create a fresh `Build Directory`{.interpreted-text role="term"} that you use exclusively for running the AUH utility:

> 4.*创建专用生成目录：*运行“structure core script”｛.depreted text role=“ref”｝脚本以创建新的“生成目录”｛.repreted text role=“term”｝，该目录专门用于运行 AUH 实用程序：

```

> ```

$ cd poky

> $cd波基

$ source oe-init-build-env your_AUH_build_directory

> $source oe初始化构建环境your_AUH_build_directory

```

> ```
> ```

Re-using an existing `Build Directory`{.interpreted-text role="term"} and its configurations is not recommended as existing settings could cause AUH to fail or behave undesirably.

> 不建议重新使用现有的“生成目录”｛.explored text role=“term”｝及其配置，因为现有设置可能会导致 AUH 失败或行为不理想。

5. *Make Configurations in Your Local Configuration File:* Several settings are needed in the `local.conf` file in the build directory you just created for AUH. Make these following configurations:

> 5.*在本地配置文件中进行配置：*在您刚刚为 AUH 创建的构建目录中的“Local.conf”文件中需要几个设置。进行以下配置：

- If you want to enable `Build History <dev-manual/build-quality:maintaining build output quality>`{.interpreted-text role="ref"}, which is optional, you need the following lines in the `conf/local.conf` file:

> -如果您想启用 `Build History＜dev manual/Build quality:maintaining Build output quality>`{.expreted text role=“ref”}（可选），则需要在 `conf/local.conf` 文件中使用以下行：

```
```

INHERIT =+ "buildhistory"
BUILDHISTORY_COMMIT = "1"

```

With this configuration and a successful upgrade, a build history \"diff\" file appears in the `upgrade-helper/work/recipe/buildhistory-diff.txt` file found in your `Build Directory`{.interpreted-text role="term"}.
```

- If you want to enable testing through the `ref-classes-testimage`{.interpreted-text role="ref"} class, which is optional, you need to have the following set in your `conf/local.conf` file:

> -如果您想通过 `ref-classes testimage`｛.explored text role=“ref”｝类（可选）启用测试，则需要在 `conf/local.conf` 文件中设置以下内容：

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

> 6.*可选地启动 vncserver:*如果您在没有 X11 会话的服务器中运行，则需要启动 vncserver：

```

> ```

$ vncserver :1

> $vncserver:1

$ export DISPLAY=:1

> $export显示=：1

```

> ```
> ```

7. *Create and Edit an AUH Configuration File:* You need to have the `upgrade-helper/upgrade-helper.conf` configuration file in your `Build Directory`{.interpreted-text role="term"}. You can find a sample configuration file in the :yocto\_[git:%60AUH](git:%60AUH) source repository \</auto-upgrade-helper/tree/\>\`.

> 7.*创建和编辑 AUH 配置文件：*您需要在“构建目录”中拥有“upgrade-helper/upgrade-helper.conf”配置文件{.depredicted text role=“term”}。您可以在：yocto\_[git:%60AUH]（git:%60AUH）源存储库\</auto-upgrade-helper/tree/\>\`中找到一个示例配置文件。

Read through the sample file and make configurations as needed. For example, if you enabled build history in your `local.conf` as described earlier, you must enable it in `upgrade-helper.conf`.

> 阅读示例文件并根据需要进行配置。例如，如果如前所述在“local.conf”中启用了构建历史记录，则必须在“upgrade-helper.conf”中启用它。

Also, if you are using the default `maintainers.inc` file supplied with Poky and located in `meta-yocto` and you do not set a \"maintainers_whitelist\" or \"global_maintainer_override\" in the `upgrade-helper.conf` configuration, and you specify \"-e all\" on the AUH command-line, the utility automatically sends out emails to all the default maintainers. Please avoid this.

> 此外，如果您使用 Poky 提供的默认 `maintainers.inc` 文件，该文件位于 `meta-yocto` 中，并且没有在 `upgrade-helper.conf` 配置中设置“maintainers_whitelist”或“global_maintainer _override”，并且在 AUH 命令行上指定了“-e all”，则该实用程序会自动向所有默认维护者发送电子邮件。请避免这种情况。

This next set of examples describes how to use the AUH:

> 下一组示例介绍了如何使用 AUH：

- *Upgrading a Specific Recipe:* To upgrade a specific recipe, use the following form:

  ```
  $ upgrade-helper.py recipe_name
  ```

  For example, this command upgrades the `xmodmap` recipe:

> 例如，此命令将升级“xmodmap”配方：

```
$ upgrade-helper.py xmodmap
```

- *Upgrading a Specific Recipe to a Particular Version:* To upgrade a specific recipe to a particular version, use the following form:

> -*将特定配方升级到特定版本：*要将特定配方更新到特定版本，请使用以下表格：

```
$ upgrade-helper.py recipe_name -t version
```

For example, this command upgrades the `xmodmap` recipe to version 1.2.3:

> 例如，此命令将“xmodmap”配方升级到 1.2.3 版本：

```
$ upgrade-helper.py xmodmap -t 1.2.3
```

- *Upgrading all Recipes to the Latest Versions and Suppressing Email Notifications:* To upgrade all recipes to their most recent versions and suppress the email notifications, use the following command:

> -*将所有配方升级到最新版本并取消电子邮件通知：*要将所有配方更新到最新的版本并取消邮件通知，请使用以下命令：

```
$ upgrade-helper.py all
```

- *Upgrading all Recipes to the Latest Versions and Send Email Notifications:* To upgrade all recipes to their most recent versions and send email messages to maintainers for each attempted recipe as well as a status email, use the following command:

> -*将所有配方升级到最新版本并发送电子邮件通知：*要将所有配方更新到最新的版本并向每个尝试配方的维护人员发送电子邮件以及状态电子邮件，请使用以下命令：

```
$ upgrade-helper.py -e all
```

Once you have run the AUH utility, you can find the results in the AUH `Build Directory`{.interpreted-text role="term"}:

> 运行 AUH 实用程序后，您可以在 AUH `Build Directory`｛.explored text role=“term”｝中找到结果：

```
${BUILDDIR}/upgrade-helper/timestamp
```

The AUH utility also creates recipe update commits from successful upgrade attempts in the layer tree.

> AUH 实用程序还通过层树中的成功升级尝试创建配方更新提交。

You can easily set up to run the AUH utility on a regular basis by using a cron job. See the :yocto\_[git:%60weeklyjob.sh](git:%60weeklyjob.sh) \</auto-upgrade-helper/tree/weeklyjob.sh\>\` file distributed with the utility for an example.

> 您可以通过使用 cron 作业轻松地设置为定期运行 AUH 实用程序。有关示例，请参阅随实用工具分发的：yocto\_[git:%60weeklyjob.sh]（git:%60weeklyjob.sh）\</auto-upgrade-helper/tree/weeklyjob-sh\>\`文件。

# Using `devtool upgrade`

As mentioned earlier, an alternative method for upgrading recipes to newer versions is to use `devtool upgrade </ref-manual/devtool-reference>`{.interpreted-text role="doc"}. You can read about `devtool upgrade` in general in the \"``sdk-manual/extensible:use \`\`devtool upgrade\`\` to create a version of the recipe that supports a newer version of the software``{.interpreted-text role="ref"}\" section in the Yocto Project Application Development and the Extensible Software Development Kit (eSDK) Manual.

> 如前所述，将配方升级到新版本的另一种方法是使用 `devtool upgrade</ref manual/devtool-reference>`{.expreted text role=“doc”}。您可以在 Yocto 项目应用程序开发和可扩展软件开发工具包（eSDK）手册中的“`` sdk manual/extensible:使用``` devtool upgrade\`` 创建一个支持新版本软件的配方”“{.depredicted text role=”ref“}\”部分中阅读有关“devtool 升级”的一般信息。

To see all the command-line options available with `devtool upgrade`, use the following help command:

> 要查看“devtool upgrade”提供的所有命令行选项，请使用以下帮助命令：

```
$ devtool upgrade -h
```

If you want to find out what version a recipe is currently at upstream without any attempt to upgrade your local version of the recipe, you can use the following command:

> 如果您想在不尝试升级本地配方的情况下了解配方当前处于上游的版本，可以使用以下命令：

```
$ devtool latest-version recipe_name
```

As mentioned in the previous section describing AUH, `devtool upgrade` works in a less-automated manner than AUH. Specifically, `devtool upgrade` only works on a single recipe that you name on the command line, cannot perform build and integration testing using images, and does not automatically generate commits for changes in the source tree. Despite all these \"limitations\", `devtool upgrade` updates the recipe file to the new upstream version and attempts to rebase custom patches contained by the recipe as needed.

> 如前一节描述 AUH 所述，“devtool 升级”的自动化程度低于 AUH。具体而言，“devtool upgrade”仅适用于您在命令行中命名的单个配方，不能使用映像执行构建和集成测试，并且不会自动为源树中的更改生成提交。尽管存在所有这些“限制”，“devtool upgrade”仍会将配方文件更新为新的上游版本，并尝试根据需要重新调整配方中包含的自定义补丁的基础。

::: note
::: title
Note
:::

AUH uses much of `devtool upgrade` behind the scenes making AUH somewhat of a \"wrapper\" application for `devtool upgrade`.

> AUH 在幕后使用了大量的“devtool upgrade”，使 AUH 在某种程度上成为“devtool 升级”的“包装器”应用程序。
> :::

A typical scenario involves having used Git to clone an upstream repository that you use during build operations. Because you have built the recipe in the past, the layer is likely added to your configuration already. If for some reason, the layer is not added, you could add it easily using the \"``bitbake-layers <bsp-guide/bsp:creating a new bsp layer using the \`\`bitbake-layers\`\` script>``{.interpreted-text role="ref"}\" script. For example, suppose you use the `nano.bb` recipe from the `meta-oe` layer in the `meta-openembedded` repository. For this example, assume that the layer has been cloned into following area:

> 一个典型的场景涉及使用 Git 克隆您在构建操作中使用的上游存储库。因为您已经在过去构建了配方，所以该层很可能已经添加到您的配置中。如果由于某种原因，没有添加该层，您可以使用\“``位烘焙层<nbsp指南：使用```位烘烤层\`\\`脚本>`` 创建一个新的 nbsp 层 <nbsp；{.depreted text role=“ref”}\”脚本轻松添加该层。例如，假设您使用“meta openembedded”存储库中“meta oe”层的“nano.bb”配方。对于本例，假设图层已克隆到以下区域：

```
/home/scottrif/meta-openembedded
```

The following command from your `Build Directory`{.interpreted-text role="term"} adds the layer to your build configuration (i.e. `${BUILDDIR}/conf/bblayers.conf`):

> 来自“构建目录”的以下命令｛.explored text role=“term”｝将层添加到构建配置中（即“$｛BUILDDIR｝/conf/bblayers.conf”）：

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

> 对于这个例子，假设上游的“nano.bb”配方具有 2.9.3 版本号。但是，本地存储库中的版本是 2.7.4。生成目录中的以下命令会自动为您升级配方：

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

> 没有必要使用“-V”选项。省略版本号会导致“devtool upgrade”将配方升级到最新版本。
> :::

Continuing with this example, you can use `devtool build` to build the newly upgraded recipe:

> 继续这个例子，您可以使用“devtool build”来构建新升级的配方：

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

> 在“devtool 升级”工作流程中，您可以部署和测试重建的软件。然而，对于本例，运行“devtool finish”会在工作区中的源代码清理干净后清理工作区。这通常意味着使用 Git 来暂存和提交升级过程中生成的更改的提交。

Once the tree is clean, you can clean things up in this example with the following command from the `${BUILDDIR}/workspace/sources/nano` directory:

> 一旦清理了树，您就可以在本例中使用“$｛BUILDDIR｝/workspace/sources/nano”目录中的以下命令进行清理：

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

> 使用“devtool finish”命令清理工作区，并根据您的提交创建一个补丁文件。在这种情况下，该工具将所有补丁文件放回名为“nano”的子目录中的源目录中。

# Manually Upgrading a Recipe

If for some reason you choose not to upgrade recipes using `dev-manual/upgrading-recipes:Using the Auto Upgrade Helper (AUH)`{.interpreted-text role="ref"} or by ``dev-manual/upgrading-recipes:Using \`\`devtool upgrade\`\` ``{.interpreted-text role="ref"}, you can manually edit the recipe files to upgrade the versions.

> 如果出于某种原因，您选择不使用 `devmanual/upgrading recipes:using the Auto upgrade Helper（AUH）`{.depredicted text role=“ref”}或 `devmanual-upgrading recipes:using\`\`devtool upgrade\`\``{.depresected text rol=“ref”｝升级配方，则可以手动编辑配方文件以升级版本。

::: note
::: title
Note
:::

Manually updating multiple recipes scales poorly and involves many steps. The recommendation to upgrade recipe versions is through AUH or `devtool upgrade`, both of which automate some steps and provide guidance for others needed for the manual process.

> 手动更新多个食谱的规模很小，需要很多步骤。建议通过 AUH 或“devtool 升级”来升级配方版本，这两种方法都会自动执行一些步骤，并为手动过程所需的其他步骤提供指导。
> :::

To manually upgrade recipe versions, follow these general steps:

> 要手动升级配方版本，请遵循以下常规步骤：

1. *Change the Version:* Rename the recipe such that the version (i.e. the `PV`{.interpreted-text role="term"} part of the recipe name) changes appropriately. If the version is not part of the recipe name, change the value as it is set for `PV`{.interpreted-text role="term"} within the recipe itself.

> 1.*更改版本：*重命名配方，使其版本（即配方名称的 `PV`｛.explored text role=“term”｝部分）适当更改。如果版本不是配方名称的一部分，请更改该值，因为它是为配方本身中的 `PV`｛.explored text role=“term”｝设置的。

2. *Update* `SRCREV`{.interpreted-text role="term"} *if Needed*: If the source code your recipe builds is fetched from Git or some other version control system, update `SRCREV`{.interpreted-text role="term"} to point to the commit hash that matches the new version.

> 2.*如果需要，请更新* `SRCREV`｛.explored text role=“term”｝*：如果您的配方构建的源代码是从 Git 或其他版本控制系统获取的，请更新 `SRCREV`｛..explored textrole=”term“｝以指向与新版本匹配的提交哈希。

3. *Build the Software:* Try to build the recipe using BitBake. Typical build failures include the following:

> 3.*构建软件：*尝试使用 BitBake 构建配方。典型的生成失败包括以下内容：

- License statements were updated for the new version. For this case, you need to review any changes to the license and update the values of `LICENSE`{.interpreted-text role="term"} and `LIC_FILES_CHKSUM`{.interpreted-text role="term"} as needed.

> -已针对新版本更新许可证声明。在这种情况下，您需要查看对许可证的任何更改，并根据需要更新 `license`｛.depredicted text role=“term”｝和 `LIC_FILES_CHKSUM`｛.epredicted textrole=”term“｝的值。

```
 ::: note
 ::: title
 Note
 :::

 License changes are often inconsequential. For example, the license text\'s copyright year might have changed.
 :::
```

- Custom patches carried by the older version of the recipe might fail to apply to the new version. For these cases, you need to review the failures. Patches might not be necessary for the new version of the software if the upgraded version has fixed those issues. If a patch is necessary and failing, you need to rebase it into the new version.

> -旧版本配方携带的自定义补丁可能无法应用于新版本。对于这些情况，您需要回顾失败。如果升级版本已经修复了这些问题，则新版本的软件可能不需要修补程序。如果一个修补程序是必要的并且失败了，您需要将其重新设置为新版本。

4. *Optionally Attempt to Build for Several Architectures:* Once you successfully build the new software for a given architecture, you could test the build for other architectures by changing the `MACHINE`{.interpreted-text role="term"} variable and rebuilding the software. This optional step is especially important if the recipe is to be released publicly.

> 4.*可选择尝试为多个体系结构构建：*一旦您成功地为给定的体系结构构建了新软件，您就可以通过更改 `MACHINE`｛.explored text role=“term”｝变量并重新构建软件来测试其他体系结构的构建。如果配方要公开发布，这个可选步骤尤其重要。

5. *Check the Upstream Change Log or Release Notes:* Checking both these reveals if there are new features that could break backwards-compatibility. If so, you need to take steps to mitigate or eliminate that situation.

> 5.*查看上游更改日志或发布说明：*同时查看这两项可以发现是否有可能破坏向后兼容性的新功能。如果是这样，您需要采取措施来缓解或消除这种情况。

6. *Optionally Create a Bootable Image and Test:* If you want, you can test the new software by booting it onto actual hardware.

> 6.*（可选）创建一个可引导映像并进行测试：*如果您愿意，您可以通过将新软件引导到实际硬件上来测试它。

7. *Create a Commit with the Change in the Layer Repository:* After all builds work and any testing is successful, you can create commits for any changes in the layer holding your upgraded recipe.

> 7.*使用层存储库中的更改创建提交：*在所有构建工作和任何测试成功后，您可以为保存升级配方的层中的任何更改创建提交。
