---
tip: translate by openai@2023-06-07 20:48:27
...
---
title: The Yocto Project Test Environment Manual
------------------------------------------------

# Welcome

Welcome to the Yocto Project Test Environment Manual! This manual is a work in progress. The manual contains information about the testing environment used by the Yocto Project to make sure each major and minor release works as intended. All the project\'s testing infrastructure and processes are publicly visible and available so that the community can see what testing is being performed, how it\'s being done and the current status of the tests and the project at any given time. It is intended that Other organizations can leverage off the process and testing environment used by the Yocto Project to create their own automated, production test environment, building upon the foundations from the project core.

> 欢迎来到 Yocto 项目测试环境手册！本手册正在不断完善中。本手册涵盖了 Yocto 项目使用的测试环境，以确保每个主要和次要版本的正常工作。该项目的所有测试基础设施和流程都是公开可见的，以便社区可以查看正在进行的测试，以及任何时候项目的测试状态。其他组织可以利用 Yocto 项目使用的流程和测试环境，建立自己的自动化生产测试环境，从项目核心开始构建。

Currently, the Yocto Project Test Environment Manual has no projected release date. This manual is a work-in-progress and is being initially loaded with information from the README files and notes from key engineers:

> 目前，Yocto 项目测试环境手册尚无预期发布日期。本手册正在初步根据 README 文件和关键工程师的注释加载信息，正处于进行中状态。

- *yocto-autobuilder2:* This :yocto_[git:%60README.md](git:%60README.md) \</yocto-autobuilder2/tree/README.md\>[ is the main README which details how to set up the Yocto Project Autobuilder. The ]\` repository represents the Yocto Project\'s console UI plugin to Buildbot and the configuration necessary to configure Buildbot to perform the testing the project requires.

> - Yocto-autobuilder2：这个：yocto_[git:README.md](git:README.md) </yocto-autobuilder2/tree/README.md> [是主要的 README，详细说明如何设置 Yocto 项目自动构建器。]` 存储库代表了 Yocto 项目的控制台 UI 插件到 Buildbot 以及配置 Buildbot 执行项目所需的测试所必需的配置。

- *yocto-autobuilder-helper:* This :yocto_[git:%60README](git:%60README) \</yocto-autobuilder-helper/tree/README/\>[ and repository contains Yocto Project Autobuilder Helper scripts and configuration. The ]__, Jenkins, or others. This repository has a branch per release of the project defining the tests to run on a per release basis.

> 这个 yocto-autobuilder-helper 仓库包含 Yocto 项目自动构建帮助器脚本和配置。它包含定义哪些测试要运行以及如何运行它们的“胶水”逻辑。因此，它可以由任何持续改进(CI)系统使用，以运行构建，支持获取正确的代码修订，配置构建和层，运行构建和收集结果。该代码独立于任何 CI 系统，这意味着该代码可以工作 Buildbot，Jenkins 或其他系统。此存储库每个项目发行版本均有一个分支，用于定义每个发行版本要运行的测试。

# Yocto Project Autobuilder Overview

The Yocto Project Autobuilder collectively refers to the software, tools, scripts, and procedures used by the Yocto Project to test released software across supported hardware in an automated and regular fashion. Basically, during the development of a Yocto Project release, the Autobuilder tests if things work. The Autobuilder builds all test targets and runs all the tests.

> Yocto 项目自动构建器指的是 Yocto 项目使用的软件、工具、脚本和程序，用于在受支持的硬件上自动和定期测试发布的软件。基本上，在开发 Yocto 项目发布版本期间，自动构建器会测试是否正常工作。自动构建器会构建所有测试目标并运行所有测试。

The Yocto Project uses now uses standard upstream [Buildbot](https://docs.buildbot.net/0.9.15.post1/) (version 9) to drive its integration and testing. Buildbot Nine has a plug-in interface that the Yocto Project customizes using code from the `yocto-autobuilder2` repository, adding its own console UI plugin. The resulting UI plug-in allows you to visualize builds in a way suited to the project\'s needs.

> 项目 Yocto 现在使用标准上游 [Buildbot](https://docs.buildbot.net/0.9.15.post1/)(版本 9)来驱动其整合和测试。Buildbot Nine 具有插件接口，Yocto 项目使用 yocto-autobuilder2 存储库中的代码进行自定义，并添加自己的控制台 UI 插件。生成的 UI 插件允许您以项目需求的方式可视化构建。

A `helper` layer provides configuration and job management through scripts found in the `yocto-autobuilder-helper` repository. The `helper` layer contains the bulk of the build configuration information and is release-specific, which makes it highly customizable on a per-project basis. The layer is CI system-agnostic and contains a number of Helper scripts that can generate build configurations from simple JSON files.

> 一个“助手”层通过 yocto-autobuilder-helper 存储库中的脚本提供配置和作业管理。“助手”层包含了大部分构建配置信息，并且是特定版本的，这使得它可以根据项目进行高度定制。该层是 CI 系统无关的，包含许多助手脚本，可以从简单的 JSON 文件生成构建配置。

::: note
::: title
Note
:::

The project uses Buildbot for historical reasons but also because many of the project developers have knowledge of Python. It is possible to use the outer layers from another Continuous Integration (CI) system such as `Jenkins <Jenkins_(software)>` instead of Buildbot.

> 项目出于历史原因使用 Buildbot，但也是因为许多项目开发人员都熟悉 Python。也可以使用其他持续集成(CI)系统(如 Jenkins)的外层替代 Buildbot。
> :::

The following figure shows the Yocto Project Autobuilder stack with a topology that includes a controller and a cluster of workers:

> 以下图表显示了 Yocto 项目自动构建器堆栈，其拓扑包括控制器和一组工作者：

![image](figures/ab-test-cluster.png)

# Yocto Project Tests \-\-- Types of Testing Overview

The Autobuilder tests different elements of the project by using the following types of tests:

> 自动构建器通过使用以下类型的测试来测试项目的不同元素：

- *Build Testing:* Tests whether specific configurations build by varying `MACHINE`, other configuration options, and the specific target images being built (or world). Used to trigger builds of all the different test configurations on the Autobuilder. Builds usually cover many different targets for different architectures, machines, and distributions, as well as different configurations, such as different init systems. The Autobuilder tests literally hundreds of configurations and targets.

> - *构建测试：* 通过变更 `MACHINE`, 其他配置选项，以及所构建的特定目标镜像(或世界)来测试特定配置是否可以构建。用于在自动构建器上触发所有不同测试配置的构建。构建通常覆盖许多不同的目标，用于不同的架构、机器和发行版，以及不同的配置，例如不同的初始系统。自动构建器实际上测试了数百种配置和目标。

- *Sanity Checks During the Build Process:* Tests initiated through the `ref-classes-insane` class. These checks ensure the output of the builds are correct. For example, does the ELF architecture in the generated binaries match the target system? ARM binaries would not work in a MIPS system!

> - *在构建过程中的健全性检查：* 通过 `ref-classes-insane` 类启动的测试。这些检查确保构建的输出是正确的。例如，生成的二进制文件中的 ELF 架构是否与目标系统匹配？ARM 二进制文件在 MIPS 系统中无法工作！

- *Build Performance Testing:* Tests whether or not commonly used steps during builds work efficiently and avoid regressions. Tests to time commonly used usage scenarios are run through `oe-build-perf-test`. These tests are run on isolated machines so that the time measurements of the tests are accurate and no other processes interfere with the timing results. The project currently tests performance on two different distributions, Fedora and Ubuntu, to ensure we have no single point of failure and can ensure the different distros work effectively.

> *构建性能测试：*测试构建过程中常用步骤是否有效，以避免退化。通过“oe-build-perf-test”来测试常用的使用场景的时间。这些测试在隔离的机器上运行，以便测试的时间测量准确，没有其他进程干扰时间结果。该项目目前在两个不同的发行版上进行性能测试，Fedora 和 Ubuntu，以确保我们没有单点故障，并确保不同的发行版有效地工作。

- *eSDK Testing:* Image tests initiated through the following command:

  ```
  $ bitbake image -c testsdkext
  ```

  The tests utilize the `ref-classes-testsdk` class and the `do_testsdkext` task.

> 测试使用 `ref-classes-testsdk` 类和 `do_testsdkext` 任务。

- *Feature Testing:* Various scenario-based tests are run through the `OpenEmbedded Self test (oe-selftest) <ref-manual/release-process:Testing and Quality Assurance>`. We test oe-selftest on each of the main distributions we support.

> *功能测试：* 通过 `OpenEmbedded Self test(oe-selftest)<ref-manual/release-process:Testing and Quality Assurance>` 运行各种基于场景的测试。我们在支持的主要分发版本上测试 oe-selftest。

- *Image Testing:* Image tests initiated through the following command:

  ```
  $ bitbake image -c testimage
  ```

  The tests utilize the `ref-classes-testimage` task.

> 测试使用 `ref-classes-testimage` 任务。

- *Layer Testing:* The Autobuilder has the possibility to test whether specific layers work with the test of the system. The layers tested may be selected by members of the project. Some key community layers are also tested periodically.

> *层测试：自动构建器具有可以测试特定层是否可以与系统测试兼容的可能性。测试的层可以由项目成员选择。一些关键的社区层也会定期进行测试。*

- *Package Testing:* A Package Test (ptest) runs tests against packages built by the OpenEmbedded build system on the target machine. See the `Testing Packages With ptest <dev-manual/packages:Testing Packages With ptest>`\" Wiki page for more information on Ptest.

> 包测试：包测试(ptest)在目标机器上对由 OpenEmbedded 构建系统构建的包运行测试。有关 Ptest 的更多信息，请参阅 Yocto 项目开发任务手册中的“使用 Ptest 测试包”部分以及“Ptest”Wiki 页面。

- *SDK Testing:* Image tests initiated through the following command:

  ```
  $ bitbake image -c testsdk
  ```

  The tests utilize the `ref-classes-testsdk` class and the `do_testsdk` task.

> 测试使用 `ref-classes-testsdk` 类和 `do_testsdk` 任务。

- *Unit Testing:* Unit tests on various components of the system run through `bitbake-selftest <ref-manual/release-process:Testing and Quality Assurance>`.

> *单元测试：通过 `bitbake-selftest <ref-manual/release-process:Testing and Quality Assurance>` 对系统的各个组件进行单元测试。

- *Automatic Upgrade Helper:* This target tests whether new versions of software are available and whether we can automatically upgrade to those new versions. If so, this target emails the maintainers with a patch to let them know this is possible.

> - *自动升级助手：*此目标测试是否有新版本的软件可用，以及我们是否可以自动升级到这些新版本。如果是，此目标会发送带有补丁的电子邮件给维护人员，以告知他们这是可能的。

# How Tests Map to Areas of Code

Tests map into the codebase as follows:

> 测试映射到代码库如下：

- *bitbake-selftest:*

  These tests are self-contained and test BitBake as well as its APIs, which include the fetchers. The tests are located in `bitbake/lib/*/tests`.

> 这些测试是独立的，既测试 BitBake，也测试其 API，其中包括获取器。测试位于 `bitbake/lib/*/tests` 中。

Some of these tests run the `bitbake` command, so `bitbake/bin` must be added to the `PATH` before running `bitbake-selftest`. From within the BitBake repository, run the following:

> 这些测试中有些会运行 `bitbake` 命令，因此在运行 `bitbake-selftest` 之前必须将 `bitbake/bin` 添加到 `PATH` 中。从 BitBake 存储库中运行以下内容：

```
$ export PATH=$PWD/bin:$PATH
```

After that, you can run the selftest script:

> 之后，您可以运行自检脚本：

```
$ bitbake-selftest
```

The default output is quiet and just prints a summary of what was run. To see more information, there is a verbose option:

> 默认输出是安静的，只打印运行的摘要。要查看更多信息，有一个详细选项：

```
$ bitbake-selftest -v
```

To skip tests that access the Internet, use the `BB_SKIP_NETTESTS` variable when running \"bitbake-selftest\" as follows:

> 要跳过访问 Internet 的测试，运行"bitbake-selftest"时使用 `BB_SKIP_NETTESTS` 变量：

```
$ BB_SKIP_NETTESTS=yes bitbake-selftest
```

Use this option when you wish to skip tests that access the network, which are mostly necessary to test the fetcher modules. To specify individual test modules to run, append the test module name to the \"bitbake-selftest\" command. For example, to specify the tests for the bb.data.module, run:

> 当你希望跳过访问网络的测试时，使用这个选项，这些测试对于测试获取模块是必要的。要指定要运行的单独的测试模块，请将测试模块名称附加到“bitbake-selftest”命令后面。例如，要指定 bb.data.module 的测试，请运行：

```
$ bitbake-selftest bb.test.data.module
```

You can also specify individual tests by defining the full name and module plus the class path of the test, for example:

> 你也可以通过定义测试的完整名称和模块以及类路径来指定单个测试，例如：

```
$ bitbake-selftest bb.tests.data.TestOverrides.test_one_override
```

The tests are based on [Python unittest](https://docs.python.org/3/library/unittest.html).

> 测试基于 [Python unittest](https://docs.python.org/3/library/unittest.html)。

- *oe-selftest:*

  - These tests use OE to test the workflows, which include testing specific features, behaviors of tasks, and API unit tests.

> 这些测试使用 OE 来测试工作流程，其中包括测试特定功能、任务的行为和 API 单元测试。

- The tests can take advantage of parallelism through the \"-j\" option, which can specify a number of threads to spread the tests across. Note that all tests from a given class of tests will run in the same thread. To parallelize large numbers of tests you can split the class into multiple units.

> 测试可以通过“-j”选项利用并行性，该选项可以指定要将测试分散到的线程数。请注意，来自给定测试类的所有测试将在同一个线程中运行。要并行化大量测试，可以将该类分成多个单元。

- The tests are based on Python unittest.
- The code for the tests resides in `meta/lib/oeqa/selftest/cases/`.
- To run all the tests, enter the following command:

  ```
  $ oe-selftest -a
  ```
- To run a specific test, use the following command form where testname is the name of the specific test:

> 要运行特定的测试，请使用以下命令形式，其中 testname 是特定测试的名称：

```
```

$ oe-selftest -r <testname>

```

For example, the following command would run the tinfoil getVar API test:

```

$ oe-selftest -r tinfoil.TinfoilTests.test_getvar

```

It is also possible to run a set of tests. For example the following command will run all of the tinfoil tests:

```

$ oe-selftest -r tinfoil

```
```

- *testimage:*

  - These tests build an image, boot it, and run tests against the image\'s content.
  - The code for these tests resides in `meta/lib/oeqa/runtime/cases/`.
  - You need to set the `IMAGE_CLASSES` variable as follows:

    ```
    IMAGE_CLASSES += "testimage"
    ```
  - Run the tests using the following command form:

    ```
    $ bitbake image -c testimage
    ```
- *testsdk:*

  - These tests build an SDK, install it, and then run tests against that SDK.
  - The code for these tests resides in `meta/lib/oeqa/sdk/cases/`.
  - Run the test using the following command form:

    ```
    $ bitbake image -c testsdk
    ```
- *testsdk_ext:*

  - These tests build an extended SDK (eSDK), install that eSDK, and run tests against the eSDK.
  - The code for these tests resides in `meta/lib/oeqa/esdk`.
  - To run the tests, use the following command form:

    ```
    $ bitbake image -c testsdkext
    ```
- *oe-build-perf-test:*

  - These tests run through commonly used usage scenarios and measure the performance times.
  - The code for these tests resides in `meta/lib/oeqa/buildperf`.
  - To run the tests, use the following command form:

    ```
    $ oe-build-perf-test <options>
    ```

    The command takes a number of options, such as where to place the test results. The Autobuilder Helper Scripts include the `build-perf-test-wrapper` script with examples of how to use the oe-build-perf-test from the command line.

    Use the `oe-git-archive` command to store test results into a Git repository.

    Use the `oe-build-perf-report` command to generate text reports and HTML reports with graphs of the performance data. For examples, see :yocto_dl:[/releases/yocto/yocto-2.7/testresults/buildperf-centos7/perf-centos7.yoctoproject.org_warrior_20190414204758_0e39202.html].
  - The tests are contained in `lib/oeqa/buildperf/test_basic.py`.

# Test Examples

This section provides example tests for each of the tests listed in the `test-manual/intro:How Tests Map to Areas of Code` section.

> 这一部分为“test-manual/intro：测试如何映射到代码区域”中列出的每个测试提供了示例测试。

For oeqa tests, testcases for each area reside in the main test directory at `meta/lib/oeqa/selftest/cases` directory.

> 对于 oeqa 测试，每个区域的测试用例位于主测试目录的 `meta/lib/oeqa/selftest/cases` 目录中。

For oe-selftest. bitbake testcases reside in the `lib/bb/tests/` directory.

> 对于 oe-selftest，bitbake 测试用例位于 `lib/bb/tests/` 目录中。

## `bitbake-selftest`

A simple test example from `lib/bb/tests/data.py` is:

> 一个来自 `lib/bb/tests/data.py` 的简单测试示例是：

```
class DataExpansions(unittest.TestCase):
   def setUp(self):
         self.d = bb.data.init()
         self.d["foo"] = "value_of_foo"
         self.d["bar"] = "value_of_bar"
         self.d["value_of_foo"] = "value_of_'value_of_foo'"

   def test_one_var(self):
         val = self.d.expand("$")
         self.assertEqual(str(val), "value_of_foo")
```

In this example, a `DataExpansions` class of tests is created, derived from standard Python unittest. The class has a common `setUp` function which is shared by all the tests in the class. A simple test is then added to test that when a variable is expanded, the correct value is found.

> 在这个例子中，创建了一个名为 `DataExpansions` 的测试类，它是从标准的 Python unittest 派生而来的。该类具有一个共享的 `setUp` 函数，由类中的所有测试共享。然后添加了一个简单的测试，以测试在变量扩展时是否找到正确的值。

BitBake selftests are straightforward Python unittest. Refer to the Python unittest documentation for additional information on writing these tests at: [https://docs.python.org/3/library/unittest.html](https://docs.python.org/3/library/unittest.html).

> BitBake 的自我测试是简单的 Python 单元测试。有关编写这些测试的更多信息，请参阅 Python 单元测试文档：[https://docs.python.org/3/library/unittest.html](https://docs.python.org/3/library/unittest.html)。

## `oe-selftest`

These tests are more complex due to the setup required behind the scenes for full builds. Rather than directly using Python\'s unittest, the code wraps most of the standard objects. The tests can be simple, such as testing a command from within the OE build environment using the following example:

> 这些测试由于需要在幕后为完整构建设置而变得更加复杂。与直接使用 Python 的 unittest 不同，代码包装了大多数标准对象。测试可以很简单，比如使用以下示例在 OE 构建环境中测试命令：

```
class BitbakeLayers(OESelftestTestCase):
   def test_bitbakelayers_showcrossdepends(self):
         result = runCmd('bitbake-layers show-cross-depends')
         self.assertTrue('aspell' in result.output, msg = "No dependencies were shown. bitbake-layers show-cross-depends output: %s"% result.output)
```

This example, taken from `meta/lib/oeqa/selftest/cases/bblayers.py`, creates a testcase from the `OESelftestTestCase` class, derived from `unittest.TestCase`, which runs the `bitbake-layers` command and checks the output to ensure it contains something we know should be here.

> 这个例子，来自“meta/lib/oeqa/selftest/cases/bblayers.py”，从 `OESelftestTestCase` 类创建了一个测试用例，该类继承自 `unittest.TestCase`，它运行 `bitbake-layers` 命令，并检查输出，以确保其包含我们知道应该在这里的东西。

The `oeqa.utils.commands` module contains Helpers which can assist with common tasks, including:

> 模块 `oeqa.utils.commands` 包含可以协助常见任务的帮助程序，包括：

- *Obtaining the value of a bitbake variable:* Use `oeqa.utils.commands.get_bb_var()` or use `oeqa.utils.commands.get_bb_vars()` for more than one variable

> 获取 bitbake 变量的值：使用 `oeqa.utils.commands.get_bb_var()` 或者使用 `oeqa.utils.commands.get_bb_vars()` 获取多个变量。

- *Running a bitbake invocation for a build:* Use `oeqa.utils.commands.bitbake()`
- *Running a command:* Use `oeqa.utils.commandsrunCmd()`

There is also a `oeqa.utils.commands.runqemu()` function for launching the `runqemu` command for testing things within a running, virtualized image.

> 也有一个 `oeqa.utils.commands.runqemu()` 函数，用于在运行的虚拟镜像中启动 `runqemu` 命令来进行测试。

You can run these tests in parallel. Parallelism works per test class, so tests within a given test class should always run in the same build, while tests in different classes or modules may be split into different builds. There is no data store available for these tests since the tests launch the `bitbake` command and exist outside of its context. As a result, common bitbake library functions (bb.\*) are also unavailable.

> 你可以并行运行这些测试。并行性是按测试类别工作的，因此给定测试类中的测试应该始终在同一构建中运行，而不同类或模块中的测试可能被分割到不同的构建中。由于测试启动 `bitbake` 命令并且存在于其上下文之外，因此这些测试没有可用的数据存储。因此，常见的 bitbake 库函数(bb.*)也不可用。

## `testimage`

These tests are run once an image is up and running, either on target hardware or under QEMU. As a result, they are assumed to be running in a target image environment, as opposed to a host build environment. A simple example from `meta/lib/oeqa/runtime/cases/python.py` contains the following:

> 这些测试在镜像启动后运行一次，无论是在目标硬件上还是在 QEMU 下。因此，他们被假定为在目标镜像环境中运行，而不是在主机构建环境中运行。来自 `meta/lib/oeqa/runtime/cases/python.py` 的简单示例包含以下内容：

```
class PythonTest(OERuntimeTestCase):
   @OETestDepends(['ssh.SSHTest.test_ssh'])
   @OEHasPackage(['python3-core'])
   def test_python3(self):
      cmd = "python3 -c \\"import codecs; print(codecs.encode('Uryyb, jbeyq', 'rot13'))\""
      status, output = self.target.run(cmd)
      msg = 'Exit status was not 0. Output: %s' % output
      self.assertEqual(status, 0, msg=msg)
```

In this example, the `OERuntimeTestCase` class wraps `unittest.TestCase`. Within the test, `self.target` represents the target system, where commands can be run on it using the `run()` method.

> 在这个例子中，`OERuntimeTestCase` 类包装了 `unittest.TestCase`。在测试中，`self.target` 代表目标系统，可以使用 `run()` 方法在其上运行命令。

To ensure certain test or package dependencies are met, you can use the `OETestDepends` and `OEHasPackage` decorators. For example, the test in this example would only make sense if python3-core is installed in the image.

> 为了确保满足某些测试或包依赖，您可以使用 `OETestDepends` 和 `OEHasPackage` 装饰器。例如，此示例中的测试只有在镜像中安装了 python3-core 时才有意义。

## `testsdk_ext`

These tests are run against built extensible SDKs (eSDKs). The tests can assume that the eSDK environment has already been setup. An example from `meta/lib/oeqa/sdk/cases/devtool.py` contains the following:

> 这些测试是针对构建的可扩展 SDK(eSDK)运行的。测试可以假设 eSDK 环境已经设置好。来自 `meta/lib/oeqa/sdk/cases/devtool.py` 的一个例子包含以下内容：

```
class DevtoolTest(OESDKExtTestCase):
   @classmethod def setUpClass(cls):
      myapp_src = os.path.join(cls.tc.esdk_files_dir, "myapp")
      cls.myapp_dst = os.path.join(cls.tc.sdk_dir, "myapp")
      shutil.copytree(myapp_src, cls.myapp_dst)
      subprocess.check_output(['git', 'init', '.'], cwd=cls.myapp_dst)
      subprocess.check_output(['git', 'add', '.'], cwd=cls.myapp_dst)
      subprocess.check_output(['git', 'commit', '-m', "'test commit'"], cwd=cls.myapp_dst)

   @classmethod
   def tearDownClass(cls):
      shutil.rmtree(cls.myapp_dst)
   def _test_devtool_build(self, directory):
      self._run('devtool add myapp %s' % directory)
      try:
      self._run('devtool build myapp')
      finally:
      self._run('devtool reset myapp')
   def test_devtool_build_make(self):
      self._test_devtool_build(self.myapp_dst)
```

In this example, the `devtool` command is tested to see whether a sample application can be built with the `devtool build` command within the eSDK.

> 在这个例子中，我们测试 `devtool` 命令，看看是否可以使用 `devtool build` 命令在 eSDK 中构建一个样例应用程序。

## `testsdk`

These tests are run against built SDKs. The tests can assume that an SDK has already been extracted and its environment file has been sourced. A simple example from `meta/lib/oeqa/sdk/cases/python2.py` contains the following:

> 这些测试是针对已构建的 SDK 运行的。测试可以假设已经提取了 SDK 并且已经源了其环境文件。来自 `meta/lib/oeqa/sdk/cases/python2.py` 的一个简单示例包含以下内容：

```
class Python3Test(OESDKTestCase):
   def setUp(self):
         if not (self.tc.hasHostPackage("nativesdk-python3-core") or
               self.tc.hasHostPackage("python3-core-native")):
            raise unittest.SkipTest("No python3 package in the SDK")

   def test_python3(self):
         cmd = "python3 -c \\"import codecs; print(codecs.encode('Uryyb, jbeyq', 'rot13'))\""
         output = self._run(cmd)
         self.assertEqual(output, "Hello, world\n")
```

In this example, if nativesdk-python3-core has been installed into the SDK, the code runs the python3 interpreter with a basic command to check it is working correctly. The test would only run if Python3 is installed in the SDK.

> 在这个例子中，如果 nativesdk-python3-core 已经安装到 SDK 中，代码将运行 python3 解释器，并使用基本命令来检查它是否正常工作。只有在 SDK 中安装了 Python3 时，测试才会运行。

## `oe-build-perf-test`

The performance tests usually measure how long operations take and the resource utilization as that happens. An example from `meta/lib/oeqa/buildperf/test_basic.py` contains the following:

> 性能测试通常测量操作所花费的时间以及在此期间所使用的资源。来自 `meta/lib/oeqa/buildperf/test_basic.py` 的一个示例包含以下内容：

```
class Test3(BuildPerfTestCase):
   def test3(self):
         """Bitbake parsing (bitbake -p)"""
         # Drop all caches and parse
         self.rm_cache()
         oe.path.remove(os.path.join(self.bb_vars['TMPDIR'], 'cache'), True)
         self.measure_cmd_resources(['bitbake', '-p'], 'parse_1',
                  'bitbake -p (no caches)')
         # Drop tmp/cache
         oe.path.remove(os.path.join(self.bb_vars['TMPDIR'], 'cache'), True)
         self.measure_cmd_resources(['bitbake', '-p'], 'parse_2',
                  'bitbake -p (no tmp/cache)')
         # Parse with fully cached data
         self.measure_cmd_resources(['bitbake', '-p'], 'parse_3',
                  'bitbake -p (cached)')
```

This example shows how three specific parsing timings are measured, with and without various caches, to show how BitBake\'s parsing performance trends over time.

> 这个例子展示了如何测量三个特定的解析时间，有和没有各种缓存，以显示 BitBake 的解析性能如何随时间变化。

# Considerations When Writing Tests

When writing good tests, there are several things to keep in mind. Since things running on the Autobuilder are accessed concurrently by multiple workers, consider the following:

> 写好测试时，需要注意几件事。由于 Autobuilder 上的东西被多个工作者同时访问，请考虑以下内容：

**Running \"cleanall\" is not permitted.**

> 运行"cleanall"是不允许的。

This can delete files from `DL_DIR` must be set to an isolated directory.

> 这可能会从 `DL_DIR` 设置为一个隔离的目录。

**Running \"cleansstate\" is not permitted.**

> 运行"cleansstate"是不允许的。

This can delete files from `SSTATE_DIR` must be set to an isolated directory. Alternatively, you can use the \"-f\" option with the `bitbake` command to \"taint\" tasks by changing the sstate checksums to ensure sstate cache items will not be reused.

> 这可能会破坏其他并行运行的构建。如果需要这样做，必须将 SSTATE_DIR 设置为一个隔离的目录。或者，您可以使用 bitbake 命令的“-f”选项来“污染”任务，以更改 sstate 校验和以确保不会重复使用 sstate 缓存项。

**Tests should not change the metadata.**

> 测试不应更改元数据。

This is particularly true for oe-selftests since these can run in parallel and changing metadata leads to changing checksums, which confuses BitBake while running in parallel. If this is necessary, copy layers to a temporary location and modify them. Some tests need to change metadata, such as the devtool tests. To protect the metadata from changes, set up temporary copies of that data first.

> 这对于 oe-selftests 尤其如此，因为它们可以并行运行，更改元数据会导致更改校验和，这会在 BitBake 并行运行时混淆。如果有必要，请将层复制到临时位置并进行修改。一些测试需要更改元数据，例如 devtool 测试。为了保护元数据免受更改，首先要设置该数据的临时副本。
