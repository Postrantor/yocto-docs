---
tip: translate by openai@2023-06-07 21:01:46
...
---
title: Understanding the Yocto Project Autobuilder
--------------------------------------------------

# Execution Flow within the Autobuilder

The \"a-full\" and \"a-quick\" targets are the usual entry points into the Autobuilder and it makes sense to follow the process through the system starting there. This is best visualized from the Autobuilder Console view (:yocto_ab:[/typhoon/#/console]).

> "a-full"和"a-quick"目标通常是进入自动构建器的入口点，从系统中按照这个过程走下去是最合理的。最好从自动构建器控制台视图中查看(:yocto_ab:[/typhoon/#/console])。

Each item along the top of that view represents some \"target build\" and these targets are all run in parallel. The \'full\' build will trigger the majority of them, the \"quick\" build will trigger some subset of them. The Autobuilder effectively runs whichever configuration is defined for each of those targets on a separate buildbot worker. To understand the configuration, you need to look at the entry on `config.json` file within the `yocto-autobuilder-helper` repository. The targets are defined in the 'overrides\' section, a quick example could be qemux86-64 which looks like:

> 每个视图顶部的项目代表一些“目标构建”，这些目标都是并行运行的。“完整的”构建将触发其中的大多数，“快速”构建将触发一些子集。Autobuilder 有效地在单独的 buildbot worker 上运行为每个目标定义的配置。要了解配置，您需要查看 yocto-autobuilder-helper 存储库中的 config.json 文件中的条目。目标在“覆盖”部分中定义，快速示例可以是 qemux86-64，看起来像：

```
"qemux86-64" : {
      "MACHINE" : "qemux86-64",
      "TEMPLATE" : "arch-qemu",
      "step1" : {
            "extravars" : [
                  "IMAGE_FSTYPES:append = ' wic wic.bmap'"
                 ]
     }
},
```

And to expand that, you need the \"arch-qemu\" entry from the \"templates\" section, which looks like:

> 要扩展它，您需要从“模板”部分获取“ arch-qemu”条目，看起来像：

```
"arch-qemu" : {
      "BUILDINFO" : true,
      "BUILDHISTORY" : true,
      "step1" : {
            "BBTARGETS" : "core-image-sato core-image-sato-dev core-image-sato-sdk core-image-minimal core-image-minimal-dev core-image-sato:do_populate_sdk",
      "SANITYTARGETS" : "core-image-minimal:do_testimage core-image-sato:do_testimage core-image-sato-sdk:do_testimage core-image-sato:do_testsdk"
      },
      "step2" : {
            "SDKMACHINE" : "x86_64",
            "BBTARGETS" : "core-image-sato:do_populate_sdk core-image-minimal:do_populate_sdk_ext core-image-sato:do_populate_sdk_ext",
            "SANITYTARGETS" : "core-image-sato:do_testsdk core-image-minimal:do_testsdkext core-image-sato:do_testsdkext"
      },
      "step3" : {
            "BUILDHISTORY" : false,
            "EXTRACMDS" : ["$ -j 15"],
            "ADDLAYER" : ["$/../meta-selftest"]
      }
},
```

Combining these two entries you can see that \"qemux86-64\" is a three step build where the `bitbake BBTARGETS` would be run, then `bitbake SANITYTARGETS` for each step; all for `MACHINE="qemux86-64"` but with differing `SDKMACHINE` settings. In step 1 an extra variable is added to the `auto.conf` file to enable wic image generation.

> 将这两个条目结合起来，您可以看到“qemux86-64”是一个三步构建，其中将运行 `bitbake BBTARGETS`，然后为每个步骤运行 `bitbake SANITYTARGETS`；所有的 `MACHINE =“qemux86-64”` 但具有不同的 `SDKMACHINE` 设置。在第一步中，将一个额外的变量添加到 `auto.conf` 文件中以启用 wic 镜像生成。

While not every detail of this is covered here, you can see how the template mechanism allows quite complex configurations to be built up yet allows duplication and repetition to be kept to a minimum.

> 虽然这里没有涵盖每一个细节，但你可以看到模板机制如何允许构建复杂的配置，同时又将重复和重复降到最低。

The different build targets are designed to allow for parallelization, so different machines are usually built in parallel, operations using the same machine and metadata are built sequentially, with the aim of trying to optimize build efficiency as much as possible.

> 不同的构建目标旨在允许并行化，因此通常会并行构建不同的机器，使用相同机器和元数据的操作会按顺序构建，旨在尽可能优化构建效率。

The `config.json` file is processed by the scripts in the Helper repository in the `scripts` directory. The following section details how this works.

> `config.json` 文件由 Helper 存储库中 `scripts` 目录中的脚本处理。下面部分详细介绍了这是如何工作的。

# Autobuilder Target Execution Overview

For each given target in a build, the Autobuilder executes several steps. These are configured in `yocto-autobuilder2/builders.py` and roughly consist of:

> 對於每個在構建中給定的目標，Autobuilder 將執行幾個步驟。這些步驟在 yocto-autobuilder2/builders.py 中配置，主要包括：

1. *Run clobberdir*.

> 运行 clobberdir

This cleans out any previous build. Old builds are left around to allow easier debugging of failed builds. For additional information, see `test-manual/understand-autobuilder:clobberdir`.

> 这会清理任何以前的构建。为了更容易调试失败的构建，会留下旧的构建。有关更多信息，请参阅 test-manual/understand-autobuilder:clobberdir。

2. *Obtain yocto-autobuilder-helper*

> 获取 yocto-autobuilder-helper

This step clones the `yocto-autobuilder-helper` git repository. This is necessary to prevent the requirement to maintain all the release or project-specific code within Buildbot. The branch chosen matches the release being built so we can support older releases and still make changes in newer ones.

> 这一步克隆 `yocto-autobuilder-helper` git 存储库。这是必要的，以防止在 Buildbot 中维护所有发行版或特定项目的代码的需求。所选择的分支与要构建的发行版相匹配，因此我们可以支持较旧的发行版，并在较新的发行版中进行更改。

3. *Write layerinfo.json*

> 写 layerinfo.json

This transfers data in the Buildbot UI when the build was configured to the Helper.

> 这在构建被配置到帮助程序时，传输 Buildbot UI 中的数据。

4. *Call scripts/shared-repo-unpack*

> 调用脚本/共享存储库解包

This is a call into the Helper scripts to set up a checkout of all the pieces this build might need. It might clone the BitBake repository and the OpenEmbedded-Core repository. It may clone the Poky repository, as well as additional layers. It will use the data from the `layerinfo.json` file to help understand the configuration. It will also use a local cache of repositories to speed up the clone checkouts. For additional information, see `test-manual/understand-autobuilder:Autobuilder Clone Cache`.

> 这是一个调用帮助脚本来设置此构建可能需要的所有部分的检出。它可能克隆 BitBake 存储库和 OpenEmbedded-Core 存储库。它可能克隆 Poky 存储库以及其他层。它将使用 layerinfo.json 文件中的数据来帮助理解配置。它还将使用本地存储库的缓存来加速克隆检出。有关更多信息，请参见 test-manual / understand-autobuilder：Autobuilder Clone Cache。

This step has two possible modes of operation. If the build is part of a parent build, it\'s possible that all the repositories needed may already be available, ready in a pre-prepared directory. An \"a-quick\" or \"a-full\" build would prepare this before starting the other sub-target builds. This is done for two reasons:

> 这一步有两种可能的操作模式。如果构建是父构建的一部分，那么所有需要的存储库可能已经可用，已准备好在预先准备的目录中。“a-quick”或“a-full”构建将在开始其他子目标构建之前进行此操作，这是出于两个原因：

- the upstream may change during a build, for example, from a forced push and this ensures we have matching content for the whole build

> 上游可能会在构建过程中发生变化，例如从强制推送，这确保我们为整个构建拥有匹配的内容。

- if 15 Workers all tried to pull the same data from the same repos, we can hit resource limits on upstream servers as they can think they are under some kind of network attack

> 如果 15 个工人都试图从同一个仓库中拉取相同的数据，我们可能会遇到上游服务器的资源限制，因为它们可能会认为自己正在遭受某种网络攻击。

This pre-prepared directory is shared among the Workers over NFS. If the build is an individual build and there is no \"shared\" directory available, it would clone from the cache and the upstreams as necessary. This is considered the fallback mode.

> 这个预先准备的目录通过 NFS 在工作者之间共享。如果构建是一个单独的构建，没有“共享”目录可用，它将从缓存和上游克隆必要的内容。这被认为是后备模式。

5. *Call scripts/run-config*

> 调用脚本/运行配置

This is another call into the Helper scripts where it\'s expected that the main functionality of this target will be executed.

> 这是另一次调用 Helper 脚本，预期这个目标的主要功能将被执行。

# Autobuilder Technology

The Autobuilder has Yocto Project-specific functionality to allow builds to operate with increased efficiency and speed.

> 自动构建器具有专用于 Yocto 项目的功能，可以提高构建的效率和速度。

## clobberdir

When deleting files, the Autobuilder uses `clobberdir`, which is a special script that moves files to a special location, rather than deleting them. Files in this location are deleted by an `rm` command, which is run under `ionice -c 3`. For example, the deletion only happens when there is idle IO capacity on the Worker. The Autobuilder Worker Janitor runs this deletion. See `test-manual/understand-autobuilder:Autobuilder Worker Janitor`.

> 当删除文件时，Autobuilder 会使用 `clobberdir`，它是一个特殊的脚本，将文件移动到一个特殊的位置，而不是删除它们。该位置的文件由 `rm` 命令在 `ionice -c 3` 下运行删除。例如，只有在 Worker 上有空闲 IO 容量时，才会发生删除操作。Autobuilder Worker Janitor 会运行这个删除操作。详见 `test-manual/understand-autobuilder:Autobuilder Worker Janitor`。

## Autobuilder Clone Cache

Cloning repositories from scratch each time they are required was slow on the Autobuilder. We therefore have a stash of commonly used repositories pre-cloned on the Workers. Data is fetched from these during clones first, then \"topped up\" with later revisions from any upstream when necessary. The cache is maintained by the Autobuilder Worker Janitor. See `test-manual/understand-autobuilder:Autobuilder Worker Janitor`.

> 每次需要时从头开始克隆存储库在自动构建器上很慢。因此，我们在工人上预克隆了常用的存储库。首先从这些存储库中获取数据，然后根据需要从任何上游“补充”更新的修订版本。缓存由自动构建器工人管理员维护。参见 test-manual/understand-autobuilder：自动构建器工人管理员。

## Autobuilder Worker Janitor

This is a process running on each Worker that performs two basic operations, including background file deletion at IO idle (see `test-manual/understand-autobuilder:Autobuilder Target Execution Overview`: Run clobberdir) and maintenance of a cache of cloned repositories to improve the speed the system can checkout repositories.

> 这是一个在每个工作者上运行的过程，它执行两项基本操作，包括在 IO 空闲时进行后台文件删除(参见 `test-manual/understand-autobuilder：Autobuilder Target Execution Overview`：运行 clobberdir)以及维护克隆存储库的缓存以提高系统检出存储库的速度。

## Shared DL_DIR

The Workers are all connected over NFS which allows `DL_DIR` to be shared between them. This reduces network accesses from the system and allows the build to be sped up. Usage of the directory within the build system is designed to be able to be shared over NFS.

> 工人们都通过 NFS 连接在一起，这使得 `DL_DIR` 可以在他们之间共享。这减少了系统的网络访问，可以加快构建速度。构建系统中的目录使用设计可以通过 NFS 共享。

## Shared SSTATE_DIR

The Workers are all connected over NFS which allows the `sstate` directory to be shared between them. This means once a Worker has built an artifact, all the others can benefit from it. Usage of the directory within the directory is designed for sharing over NFS.

> 工人们都通过 NFS 连接在一起，这使得 `sstate` 目录可以在他们之间共享。这意味着一旦一个工人构建了一个工件，其他所有人都可以从中受益。目录内的目录使用设计用于在 NFS 上共享。

## Resulttool

All of the different tests run as part of the build generate output into `testresults.json` files. This allows us to determine which tests ran in a given build and their status. Additional information, such as failure logs or the time taken to run the tests, may also be included.

> 所有作为构建一部分运行的不同测试都会将输出生成到 `testresults.json` 文件中。这样可以确定给定构建中运行的测试以及它们的状态。还可以包括失败日志或运行测试所花费的时间等其他信息。

Resulttool is part of OpenEmbedded-Core and is used to manipulate these json results files. It has the ability to merge files together, display reports of the test results and compare different result files.

> Resulttool 是 OpenEmbedded-Core 的一部分，用于操作这些 json 结果文件。它具有合并文件、显示测试结果报告以及比较不同结果文件的能力。

For details, see :yocto_wiki:[/Resulttool].

> 详情请参见：yocto_wiki：[/Resulttool]。

# run-config Target Execution

The `scripts/run-config` execution is where most of the work within the Autobuilder happens. It runs through a number of steps; the first are general setup steps that are run once and include:

> `scripts/run-config` 执行是 Autobuilder 中发生的大部分工作。它经历了一系列步骤；第一步是一次性运行的通用设置步骤，包括：

1. Set up any `buildtools` tarball if configured.

> 设置任何配置的 `buildtools` 压缩包。

2. Call \"buildhistory-init\" if `ref-classes-buildhistory` is configured.

> 若配置了 `ref-classes-buildhistory`，请调用“buildhistory-init”。

For each step that is configured in `config.json`, it will perform the following:

> 对于 config.json 中配置的每一步，它将执行以下操作：

1. Add any layers that are specified using the `bitbake-layers add-layer` command (logging as stepXa)

> 使用 `bitbake-layers add-layer` 命令添加任何指定的图层(以 stepXa 的方式登录)

2. Call the `scripts/setup-config` script to generate the necessary `auto.conf` configuration file for the build

> 调用 `scripts/setup-config` 脚本来生成必要的 `auto.conf` 配置文件以进行构建。

3. Run the `bitbake BBTARGETS` command (logging as stepXb)

> 运行 `bitbake BBTARGETS` 命令(以 stepXb 登录)

4. Run the `bitbake SANITYTARGETS` command (logging as stepXc)

> 运行 `bitbake SANITYTARGETS` 命令(以 stepXc 的身份登录)

5. Run the `EXTRACMDS` command, which are run within the BitBake build environment (logging as stepXd)

> 运行 `EXTRACMDS` 命令，这些命令在 BitBake 构建环境中运行(作为 stepXd 登录)。

6. Run the `EXTRAPLAINCMDS` command(s), which are run outside the BitBake build environment (logging as stepXd)

> 运行在 BitBake 构建环境之外的 `EXTRAPLAINCMDS` 命令(以 stepXd 的身份登录)

7. Remove any layers added in step 1 using the `bitbake-layers remove-layer` command (logging as stepXa)

> 使用 `bitbake-layers remove-layer` 命令移除在步骤 1 中添加的任何层(作为步骤 Xa 记录)

Once the execution steps above complete, `run-config` executes a set of post-build steps, including:

> 一旦上述执行步骤完成，`run-config` 将执行一系列后期构建步骤，包括：

1. Call `scripts/publish-artifacts` to collect any output which is to be saved from the build.

> 调用 `scripts/publish-artifacts` 收集构建过程中需要保存的任何输出。

2. Call `scripts/collect-results` to collect any test results to be saved from the build.

> 调用 `scripts/collect-results` 收集从构建中保存的任何测试结果。

3. Call `scripts/upload-error-reports` to send any error reports generated to the remote server.

> 调用 `scripts/upload-error-reports` 将生成的任何错误报告发送到远程服务器。

4. Cleanup the `Build Directory` if the build was successful, else rename it to \"build-renamed\" for potential future debugging.

> 如果构建成功，使用 test-manual/understand-autobuilder:clobberdir 清理“Build Directory”，否则将其重命名为“build-renamed”，以便将来调试。

# Deploying Yocto Autobuilder

The most up to date information about how to setup and deploy your own Autobuilder can be found in README.md in the `yocto-autobuilder2` repository.

> 最新关于如何设置和部署自己的自动构建器的信息可以在 yocto-autobuilder2 存储库中的 README.md 中找到。

We hope that people can use the `yocto-autobuilder2` code directly but it is inevitable that users will end up needing to heavily customise the `yocto-autobuilder-helper` repository, particularly the `config.json` file as they will want to define their own test matrix.

> 我们希望人们可以直接使用 `yocto-autobuilder2` 代码，但不可避免的是用户最终需要大量定制 `yocto-autobuilder-helper` 存储库，特别是 `config.json` 文件，因为他们将想要定义自己的测试矩阵。

The Autobuilder supports wo customization options:

> 自动构建器支持两种定制选项：

- variable substitution
- overlaying configuration files

The standard `config.json` minimally attempts to allow substitution of the paths. The Helper script repository includes a `local-example.json` file to show how you could override these from a separate configuration file. Pass the following into the environment of the Autobuilder:

> 标准的 `config.json` 最少试图允许替换路径。Helper 脚本存储库包括一个 `local-example.json` 文件，以显示如何从单独的配置文件中覆盖这些内容。将以下内容传递到 Autobuilder 的环境中：

```
$ ABHELPER_JSON="config.json local-example.json"
```

As another example, you could also pass the following into the environment:

> 作为另一个例子，您还可以将以下内容传递给环境：

```
$ ABHELPER_JSON="config.json /some/location/local.json"
```

One issue users often run into is validation of the `config.json` files. A tip for minimizing issues from invalid json files is to use a Git `pre-commit-hook.sh` script to verify the JSON file before committing it. Create a symbolic link as follows:

> 用户经常遇到的一个问题是对 `config.json` 文件的验证。为了尽量减少无效 JSON 文件带来的问题，建议使用 Git `pre-commit-hook.sh` 脚本在提交之前验证 JSON 文件。按照以下方式创建一个符号链接：

```
$ ln -s ../../scripts/pre-commit-hook.sh .git/hooks/pre-commit
```
