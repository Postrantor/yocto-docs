---
tip: translate by openai@2023-06-10 12:09:55
...
---
title: Performing Automated Runtime Testing
-------------------------------------------

The OpenEmbedded build system makes available a series of automated tests for images to verify runtime functionality. You can run these tests on either QEMU or actual target hardware. Tests are written in Python making use of the `unittest` module, and the majority of them run commands on the target system over SSH. This section describes how you set up the environment to use these tests, run available tests, and write and add your own tests.

> 开放式嵌入式构建系统为验证运行时功能提供了一系列自动化测试。您可以在 QEMU 或实际目标硬件上运行这些测试。测试使用 Python 编写，使用 `unittest` 模块，大多数测试都是通过 SSH 在目标系统上运行命令。本节介绍如何设置环境以使用这些测试，运行可用测试，编写和添加自己的测试。

For information on the test and QA infrastructure available within the Yocto Project, see the \"`ref-manual/release-process:testing and quality assurance`{.interpreted-text role="ref"}\" section in the Yocto Project Reference Manual.

> 对于 Yocto 项目中可用的测试和 QA 基础设施的信息，请参阅 Yocto 项目参考手册中的“ref-manual / release-process：testing and quality assurance”部分。

# Enabling Tests

Depending on whether you are planning to run tests using QEMU or on the hardware, you have to take different steps to enable the tests. See the following subsections for information on how to enable both types of tests.

> 根据您是否计划使用 QEMU 还是硬件运行测试，您必须采取不同的步骤来启用测试。有关如何启用两种类型测试的信息，请参阅以下子节。

## Enabling Runtime Tests on QEMU

In order to run tests, you need to do the following:

- *Set up to avoid interaction with sudo for networking:* To accomplish this, you must do one of the following:

  - Add `NOPASSWD` for your user in `/etc/sudoers` either for all commands or just for `runqemu-ifup`. You must provide the full path as that can change if you are using multiple clones of the source repository.

> 在/etc/sudoers 中为您的用户添加'NOPASSWD'，无论是对所有命令还是仅对'runqemu-ifup'，您都必须提供完整的路径，因为如果您使用多个源存储库克隆，路径可能会发生变化。

```
::: note
::: title
Note
:::

On some distributions, you also need to comment out \"Defaults requiretty\" in `/etc/sudoers`.
:::
```

- Manually configure a tap interface for your system.
- Run as root the script in `scripts/runqemu-gen-tapdevs`, which should generate a list of tap devices. This is the option typically chosen for Autobuilder-type environments.

> 请以 root 身份运行脚本 `scripts/runqemu-gen-tapdevs`，它会生成一系列 tap 设备。这是通常用于自动构建环境的选择。

```
::: note
::: title
Note
:::

- Be sure to use an absolute path when calling this script with sudo.
- The package recipe `qemu-helper-native` is required to run this script. Build the package using the following command:

  ```
  $ bitbake qemu-helper-native
  ```

:::
```

- *Set the DISPLAY variable:* You need to set this variable so that you have an X server available (e.g. start `vncserver` for a headless machine).
- *Be sure your host\'s firewall accepts incoming connections from 192.168.7.0/24:* Some of the tests (in particular DNF tests) start an HTTP server on a random high number port, which is used to serve files to the target. The DNF module serves `${WORKDIR}/oe-rootfs-repo` so it can run DNF channel commands. That means your host\'s firewall must accept incoming connections from 192.168.7.0/24, which is the default IP range used for tap devices by `runqemu`.

> 确保您的主机防火墙接受来自 192.168.7.0/24 的传入连接：某些测试（特别是 DNF 测试）会在一个随机的高端号上启动一个 HTTP 服务器，用于向目标提供文件。DNF 模块提供 `${WORKDIR}/oe-rootfs-repo`，这样它就可以运行 DNF 频道命令。这意味着您的主机防火墙必须接受来自 192.168.7.0/24 的传入连接，这是 `runqemu` 默认使用的 tap 设备的 IP 范围。

- *Be sure your host has the correct packages installed:* Depending your host\'s distribution, you need to have the following packages installed:
  - Ubuntu and Debian: `sysstat` and `iproute2`
  - openSUSE: `sysstat` and `iproute2`
  - Fedora: `sysstat` and `iproute`
  - CentOS: `sysstat` and `iproute`

Once you start running the tests, the following happens:

1. A copy of the root filesystem is written to `${WORKDIR}/testimage`.
2. The image is booted under QEMU using the standard `runqemu` script.
3. A default timeout of 500 seconds occurs to allow for the boot process to reach the login prompt. You can change the timeout period by setting `TEST_QEMUBOOT_TIMEOUT`{.interpreted-text role="term"} in the `local.conf` file.

> 默认超时时间为 500 秒，以便让引导过程达到登录提示符。您可以通过在 `local.conf` 文件中设置 `TEST_QEMUBOOT_TIMEOUT` 来更改超时时间。

4. Once the boot process is reached and the login prompt appears, the tests run. The full boot log is written to `${WORKDIR}/testimage/qemu_boot_log`.

> 一旦启动过程到达并出现登录提示，就会运行测试。完整的启动日志被写入 `${WORKDIR}/testimage/qemu_boot_log`。

5. Each test module loads in the order found in `TEST_SUITES`{.interpreted-text role="term"}. You can find the full output of the commands run over SSH in `${WORKDIR}/testimgage/ssh_target_log`.

> 每个测试模块按照 `TEST_SUITES` 中的顺序加载。您可以在 `${WORKDIR}/testimgage/ssh_target_log` 中找到通过 SSH 运行的命令的完整输出。

6. If no failures occur, the task running the tests ends successfully. You can find the output from the `unittest` in the task log at `${WORKDIR}/temp/log.do_testimage`.

> 如果没有发生故障，运行测试的任务就会成功结束。您可以在任务日志 `${WORKDIR}/temp/log.do_testimage` 中找到 `unittest` 的输出。

## Enabling Runtime Tests on Hardware

The OpenEmbedded build system can run tests on real hardware, and for certain devices it can also deploy the image to be tested onto the device beforehand.

> 开放式嵌入式构建系统可以在真实硬件上运行测试，并且对于某些设备，它还可以事先将要测试的镜像部署到设备上。

For automated deployment, a \"controller image\" is installed onto the hardware once as part of setup. Then, each time tests are to be run, the following occurs:

> 为了自动部署，一旦设置完成，就会在硬件上安装一个“控制器镜像”。每次要运行测试时，都会发生以下情况：

1. The controller image is booted into and used to write the image to be tested to a second partition.
2. The device is then rebooted using an external script that you need to provide.
3. The device boots into the image to be tested.

When running tests (independent of whether the image has been deployed automatically or not), the device is expected to be connected to a network on a pre-determined IP address. You can either use static IP addresses written into the image, or set the image to use DHCP and have your DHCP server on the test network assign a known IP address based on the MAC address of the device.

> 当运行测试（无论图像是否被自动部署）时，设备预期会连接到一个预定的 IP 地址的网络上。您可以在图像中使用静态 IP 地址，或者将图像设置为使用 DHCP，并在测试网络上使用 DHCP 服务器根据设备的 MAC 地址分配一个已知的 IP 地址。

In order to run tests on hardware, you need to set `TEST_TARGET`{.interpreted-text role="term"} to an appropriate value. For QEMU, you do not have to change anything, the default value is \"qemu\". For running tests on hardware, the following options are available:

> 为了在硬件上运行测试，您需要将 `TEST_TARGET` 设置为适当的值。 对于 QEMU，您不必更改任何内容，默认值为“qemu”。 对于在硬件上运行测试，有以下选项可用：

- *\"simpleremote\":* Choose \"simpleremote\" if you are going to run tests on a target system that is already running the image to be tested and is available on the network. You can use \"simpleremote\" in conjunction with either real hardware or an image running within a separately started QEMU or any other virtual machine manager.

> 选择“simpleremote”如果您要在网络中已经运行要测试的镜像的目标系统上运行测试。您可以将“simpleremote”与实际硬件或在单独启动的 QEMU 或任何其他虚拟机管理器中运行的镜像一起使用。

- *\"SystemdbootTarget\":* Choose \"SystemdbootTarget\" if your hardware is an EFI-based machine with `systemd-boot` as bootloader and `core-image-testmaster` (or something similar) is installed. Also, your hardware under test must be in a DHCP-enabled network that gives it the same IP address for each reboot.

> 选择"SystemdbootTarget"，如果您的硬件是基于 EFI 的机器，其中有 systemd-boot 作为启动程序，并且已安装 core-image-testmaster（或类似的）。此外，您的测试硬件必须处于启用 DHCP 的网络中，为其每次重新启动提供相同的 IP 地址。

If you choose \"SystemdbootTarget\", there are additional requirements and considerations. See the \"`dev-manual/runtime-testing:selecting systemdboottarget`{.interpreted-text role="ref"}\" section, which follows, for more information.

> 如果您选择"SystemdbootTarget"，则会有额外的要求和考虑。有关更多信息，请参见下面的"dev-manual / runtime-testing：selecting systemdboottarget"部分。

- *\"BeagleBoneTarget\":* Choose \"BeagleBoneTarget\" if you are deploying images and running tests on the BeagleBone \"Black\" or original \"White\" hardware. For information on how to use these tests, see the comments at the top of the BeagleBoneTarget `meta-yocto-bsp/lib/oeqa/controllers/beaglebonetarget.py` file.

> 选择"BeagleBoneTarget"如果您正在部署图像并在 BeagleBone "Black"或原始"White"硬件上运行测试。有关如何使用这些测试的信息，请参阅 BeagleBoneTarget `meta-yocto-bsp/lib/oeqa/controllers/beaglebonetarget.py` 文件顶部的注释。

- *\"EdgeRouterTarget\":* Choose \"EdgeRouterTarget\" if you are deploying images and running tests on the Ubiquiti Networks EdgeRouter Lite. For information on how to use these tests, see the comments at the top of the EdgeRouterTarget `meta-yocto-bsp/lib/oeqa/controllers/edgeroutertarget.py` file.

> 选择“EdgeRouterTarget”如果您正在部署图像并在 Ubiquiti Networks EdgeRouter Lite 上运行测试。有关如何使用这些测试的信息，请参阅 EdgeRouterTarget `meta-yocto-bsp/lib/oeqa/controllers/edgeroutertarget.py` 文件顶部的注释。

- *\"GrubTarget\":* Choose \"GrubTarget\" if you are deploying images and running tests on any generic PC that boots using GRUB. For information on how to use these tests, see the comments at the top of the GrubTarget `meta-yocto-bsp/lib/oeqa/controllers/grubtarget.py` file.

> - *"GrubTarget":* 如果要在使用 GRUB 启动的任何通用 PC 上部署镜像并运行测试，请选择“GrubTarget”。有关如何使用这些测试的信息，请参见 GrubTarget `meta-yocto-bsp/lib/oeqa/controllers/grubtarget.py` 文件顶部的注释。

- *\"your-target\":* Create your own custom target if you want to run tests when you are deploying images and running tests on a custom machine within your BSP layer. To do this, you need to add a Python unit that defines the target class under `lib/oeqa/controllers/` within your layer. You must also provide an empty `__init__.py`. For examples, see files in `meta-yocto-bsp/lib/oeqa/controllers/`.

> 如果你想在部署映像和在 BSP 层上的自定义机器上运行测试时，请创建自己的自定义目标。要做到这一点，您需要在您的层内的 `lib / oeqa / controllers /` 中添加一个定义目标类的 Python 单元。您还必须提供一个空的 `__init__.py`。有关示例，请参阅 `meta-yocto-bsp / lib / oeqa / controllers /` 中的文件。

## Selecting SystemdbootTarget

If you did not set `TEST_TARGET`{.interpreted-text role="term"} to \"SystemdbootTarget\", then you do not need any information in this section. You can skip down to the \"`dev-manual/runtime-testing:running tests`{.interpreted-text role="ref"}\" section.

> 如果你没有将 `TEST_TARGET` 设置为"SystemdbootTarget"，那么你不需要本节中的任何信息。你可以跳到"dev-manual/runtime-testing:running tests"节中。

If you did set `TEST_TARGET`{.interpreted-text role="term"} to \"SystemdbootTarget\", you also need to perform a one-time setup of your controller image by doing the following:

> 如果您将 TEST_TARGET 设置为"SystemdbootTarget"，您还需要通过执行以下步骤来执行一次性设置控制器映像：

1. *Set EFI_PROVIDER:* Be sure that `EFI_PROVIDER`{.interpreted-text role="term"} is as follows:

   ```
   EFI_PROVIDER = "systemd-boot"
   ```
2. *Build the controller image:* Build the `core-image-testmaster` image. The `core-image-testmaster` recipe is provided as an example for a \"controller\" image and you can customize the image recipe as you would any other recipe.

> 请帮助我翻译：“2. *构建控制器镜像：*构建 `core-image-testmaster` 镜像。`core-image-testmaster` 配方作为“控制器”镜像的示例提供，您可以像其他配方一样自定义镜像配方。

Here are the image recipe requirements:

- Inherits `core-image` so that kernel modules are installed.
- Installs normal linux utilities not BusyBox ones (e.g. `bash`, `coreutils`, `tar`, `gzip`, and `kmod`).
- Uses a custom `Initramfs`{.interpreted-text role="term"} image with a custom installer. A normal image that you can install usually creates a single root filesystem partition. This image uses another installer that creates a specific partition layout. Not all Board Support Packages (BSPs) can use an installer. For such cases, you need to manually create the following partition layout on the target:

> 使用自定义的 Initramfs 图像和自定义安装程序。通常可以安装的普通图像会创建单个根文件系统分区。此图像使用另一个安装程序，可创建特定的分区布局。并非所有板级支持包（BSP）都可以使用安装程序。对于这种情况，您需要在目标上手动创建以下分区布局：
> - First partition mounted under `/boot`, labeled \"boot\".
> - The main root filesystem partition where this image gets installed, which is mounted under `/`.
> - Another partition labeled \"testrootfs\" where test images get deployed.

3. *Install image:* Install the image that you just built on the target system.

The final thing you need to do when setting `TEST_TARGET`{.interpreted-text role="term"} to \"SystemdbootTarget\" is to set up the test image:

1. *Set up your local.conf file:* Make sure you have the following statements in your `local.conf` file:

   ```
   IMAGE_FSTYPES += "tar.gz"
   INHERIT += "testimage"
   TEST_TARGET = "SystemdbootTarget"
   TEST_TARGET_IP = "192.168.2.3"
   ```
2. *Build your test image:* Use BitBake to build the image:

   ```
   $ bitbake core-image-sato
   ```

## Power Control

For most hardware targets other than \"simpleremote\", you can control power:

- You can use `TEST_POWERCONTROL_CMD`{.interpreted-text role="term"} together with `TEST_POWERCONTROL_EXTRA_ARGS`{.interpreted-text role="term"} as a command that runs on the host and does power cycling. The test code passes one argument to that command: off, on or cycle (off then on). Here is an example that could appear in your `local.conf` file:

> 你可以使用 `TEST_POWERCONTROL_CMD`{.interpreted-text role="term"}和 `TEST_POWERCONTROL_EXTRA_ARGS`{.interpreted-text role="term"}作为在主机上运行的电源循环命令。测试代码会向该命令传递一个参数：off，on 或者 cycle（先关闭再打开）。以下是可以出现在你的 `local.conf` 文件中的示例：

```
TEST_POWERCONTROL_CMD = "powercontrol.exp test 10.11.12.1 nuc1"
```

In this example, the expect script does the following:

```shell
ssh test@10.11.12.1 "pyctl nuc1 arg"
```

It then runs a Python script that controls power for a label called `nuc1`.

::: note
::: title
Note
:::

You need to customize `TEST_POWERCONTROL_CMD`{.interpreted-text role="term"} and `TEST_POWERCONTROL_EXTRA_ARGS`{.interpreted-text role="term"} for your own setup. The one requirement is that it accepts \"on\", \"off\", and \"cycle\" as the last argument.

> 你需要根据自己的设置定制 `TEST_POWERCONTROL_CMD` 和 `TEST_POWERCONTROL_EXTRA_ARGS`。唯一的要求是它们的最后一个参数可以接受“on”，“off”和“cycle”。
> :::

- When no command is defined, it connects to the device over SSH and uses the classic reboot command to reboot the device. Classic reboot is fine as long as the machine actually reboots (i.e. the SSH test has not failed). It is useful for scenarios where you have a simple setup, typically with a single board, and where some manual interaction is okay from time to time.

> 当没有定义命令时，它会通过 SSH 连接到设备，并使用经典重启命令来重启设备。只要机器实际重启（即 SSH 测试没有失败），经典重启就很好。它对于具有简单设置的场景非常有用，通常只有一块板，并且偶尔需要手动交互。

If you have no hardware to automatically perform power control but still wish to experiment with automated hardware testing, you can use the `dialog-power-control` script that shows a dialog prompting you to perform the required power action. This script requires either KDialog or Zenity to be installed. To use this script, set the `TEST_POWERCONTROL_CMD`{.interpreted-text role="term"} variable as follows:

> 如果您没有硬件可以自动执行电源控制，但仍希望尝试自动化硬件测试，可以使用 `dialog-power-control` 脚本，该脚本显示一个对话框，提示您执行所需的电源操作。此脚本需要安装 KDialog 或 Zenity。要使用此脚本，请将 `TEST_POWERCONTROL_CMD` 变量设置如下：

```
TEST_POWERCONTROL_CMD = "${COREBASE}/scripts/contrib/dialog-power-control"
```

## Serial Console Connection

For test target classes requiring a serial console to interact with the bootloader (e.g. BeagleBoneTarget, EdgeRouterTarget, and GrubTarget), you need to specify a command to use to connect to the serial console of the target machine by using the `TEST_SERIALCONTROL_CMD`{.interpreted-text role="term"} variable and optionally the `TEST_SERIALCONTROL_EXTRA_ARGS`{.interpreted-text role="term"} variable.

> 对于需要与引导程序进行串行控制台交互的测试目标类（例如 BeagleBoneTarget，EdgeRouterTarget 和 GrubTarget），您需要使用 `TEST_SERIALCONTROL_CMD`{.interpreted-text role="term"}变量指定用于连接目标机器的串行控制台的命令，并可选择使用 `TEST_SERIALCONTROL_EXTRA_ARGS`{.interpreted-text role="term"}变量。

These cases could be a serial terminal program if the machine is connected to a local serial port, or a `telnet` or `ssh` command connecting to a remote console server. Regardless of the case, the command simply needs to connect to the serial console and forward that connection to standard input and output as any normal terminal program does. For example, to use the picocom terminal program on serial device `/dev/ttyUSB0` at 115200bps, you would set the variable as follows:

> 如果机器连接到本地串行端口，这些情况可以是一个串行终端程序，或者是连接到远程控制台服务器的 `telnet` 或 `ssh` 命令。无论情况如何，该命令只需要连接到串行控制台，并将该连接转发到标准输入和输出，就像任何正常的终端程序一样。例如，要在串行设备 `/dev/ttyUSB0` 上以 115200bps 的速率使用 picocom 终端程序，您可以将变量设置如下：

```
TEST_SERIALCONTROL_CMD = "picocom /dev/ttyUSB0 -b 115200"
```

For local devices where the serial port device disappears when the device reboots, an additional \"serdevtry\" wrapper script is provided. To use this wrapper, simply prefix the terminal command with `${COREBASE}/scripts/contrib/serdevtry`:

> 对于在设备重启时串口设备消失的本地设备，提供了额外的“serdevtry”包装脚本。要使用此包装器，只需在终端命令前添加 `${COREBASE}/scripts/contrib/serdevtry`：

```
TEST_SERIALCONTROL_CMD = "${COREBASE}/scripts/contrib/serdevtry picocom -b 115200 /dev/ttyUSB0"
```

# Running Tests

You can start the tests automatically or manually:

- *Automatically running tests:* To run the tests automatically after the OpenEmbedded build system successfully creates an image, first set the `TESTIMAGE_AUTO`{.interpreted-text role="term"} variable to \"1\" in your `local.conf` file in the `Build Directory`{.interpreted-text role="term"}:

> - *自动运行测试：*要在 OpenEmbedded 构建系统成功创建映像后自动运行测试，请首先在构建目录中的 `local.conf` 文件中将 `TESTIMAGE_AUTO` 变量设置为“1”：

```
TESTIMAGE_AUTO = "1"
```

Next, build your image. If the image successfully builds, the tests run:

```
bitbake core-image-sato
```

- *Manually running tests:* To manually run the tests, first globally inherit the `ref-classes-testimage`{.interpreted-text role="ref"} class by editing your `local.conf` file:

> 手动运行测试：要手动运行测试，首先通过编辑您的“local.conf”文件来全局继承“ref-classes-testimage”类：

```
INHERIT += "testimage"
```

Next, use BitBake to run the tests:

```
bitbake -c testimage image
```

All test files reside in `meta/lib/oeqa/runtime/cases` in the `Source Directory`{.interpreted-text role="term"}. A test name maps directly to a Python module. Each test module may contain a number of individual tests. Tests are usually grouped together by the area tested (e.g tests for systemd reside in `meta/lib/oeqa/runtime/cases/systemd.py`).

> 所有测试文件都位于源目录中的 `meta/lib/oeqa/runtime/cases` 中。测试名称直接映射到 Python 模块。每个测试模块可能包含许多个别测试。测试通常按照所测试的领域分组（例如 systemd 的测试位于 `meta/lib/oeqa/runtime/cases/systemd.py` 中）。

You can add tests to any layer provided you place them in the proper area and you extend `BBPATH`{.interpreted-text role="term"} in the `local.conf` file as normal. Be sure that tests reside in `layer/lib/oeqa/runtime/cases`.

> 你可以在任何一层中添加测试，只要你把它们放在正确的区域，并且在 `local.conf` 文件中像往常一样扩展 `BBPATH`。确保测试位于 `layer/lib/oeqa/runtime/cases` 中。

::: note
::: title
Note
:::

Be sure that module names do not collide with module names used in the default set of test modules in `meta/lib/oeqa/runtime/cases`.
:::

You can change the set of tests run by appending or overriding `TEST_SUITES`{.interpreted-text role="term"} variable in `local.conf`. Each name in `TEST_SUITES`{.interpreted-text role="term"} represents a required test for the image. Test modules named within `TEST_SUITES`{.interpreted-text role="term"} cannot be skipped even if a test is not suitable for an image (e.g. running the RPM tests on an image without `rpm`). Appending \"auto\" to `TEST_SUITES`{.interpreted-text role="term"} causes the build system to try to run all tests that are suitable for the image (i.e. each test module may elect to skip itself).

> 你可以通过在 `local.conf` 中追加或覆盖 `TEST_SUITES` 变量来更改要运行的测试集合。`TEST_SUITES` 中的每个名称都代表图像所需的测试。即使测试不适合图像（例如在没有 `rpm` 的图像上运行 RPM 测试），`TEST_SUITES` 中指定的测试模块也不能被跳过。将“auto”追加到 `TEST_SUITES` 会导致构建系统尝试运行所有适合图像的测试（即每个测试模块可以选择跳过自身）。

The order you list tests in `TEST_SUITES`{.interpreted-text role="term"} is important and influences test dependencies. Consequently, tests that depend on other tests should be added after the test on which they depend. For example, since the `ssh` test depends on the `ping` test, \"ssh\" needs to come after \"ping\" in the list. The test class provides no re-ordering or dependency handling.

> 顺序列出 `TEST_SUITES` 中的测试很重要，因为它会影响测试的依赖性。因此，依赖其他测试的测试应该放在依赖的测试之后。例如，由于 `ssh` 测试依赖于 `ping` 测试，所以\"ssh\"需要放在\"ping\"之后。测试类不提供重新排序或依赖处理。

::: note
::: title
Note
:::

Each module can have multiple classes with multiple test methods. And, Python `unittest` rules apply.
:::

Here are some things to keep in mind when running tests:

- The default tests for the image are defined as:

  ```
  DEFAULT_TEST_SUITES:pn-image = "ping ssh df connman syslog xorg scp vnc date rpm dnf dmesg"
  ```
- Add your own test to the list of the by using the following:

  ```
  TEST_SUITES:append = " mytest"
  ```
- Run a specific list of tests as follows:

  ```
  TEST_SUITES = "test1 test2 test3"
  ```

  Remember, order is important. Be sure to place a test that is dependent on another test later in the order.

# Exporting Tests

You can export tests so that they can run independently of the build system. Exporting tests is required if you want to be able to hand the test execution off to a scheduler. You can only export tests that are defined in `TEST_SUITES`{.interpreted-text role="term"}.

> 你可以导出测试，使它们可以独立于构建系统运行。 如果你想将测试执行交给调度程序，则需要导出测试。 您只能导出在 `TEST_SUITES`{.interpreted-text role="term"}中定义的测试。

If your image is already built, make sure the following are set in your `local.conf` file:

```
INHERIT += "testexport"
TEST_TARGET_IP = "IP-address-for-the-test-target"
TEST_SERVER_IP = "IP-address-for-the-test-server"
```

You can then export the tests with the following BitBake command form:

```
$ bitbake image -c testexport
```

Exporting the tests places them in the `Build Directory`{.interpreted-text role="term"} in `tmp/testexport/` image, which is controlled by the `TEST_EXPORT_DIR`{.interpreted-text role="term"} variable.

> 将测试导出到 `Build Directory`{.interpreted-text role="term"}中的 `tmp/testexport/` 图像，这受 `TEST_EXPORT_DIR`{.interpreted-text role="term"}变量控制。

You can now run the tests outside of the build environment:

```
$ cd tmp/testexport/image
$ ./runexported.py testdata.json
```

Here is a complete example that shows IP addresses and uses the `core-image-sato` image:

```
INHERIT += "testexport"
TEST_TARGET_IP = "192.168.7.2"
TEST_SERVER_IP = "192.168.7.1"
```

Use BitBake to export the tests:

```
$ bitbake core-image-sato -c testexport
```

Run the tests outside of the build environment using the following:

```
$ cd tmp/testexport/core-image-sato
$ ./runexported.py testdata.json
```

# Writing New Tests

As mentioned previously, all new test files need to be in the proper place for the build system to find them. New tests for additional functionality outside of the core should be added to the layer that adds the functionality, in `layer/lib/oeqa/runtime/cases` (as long as `BBPATH`{.interpreted-text role="term"} is extended in the layer\'s `layer.conf` file as normal). Just remember the following:

> 如前所述，所有新的测试文件都需要放在正确的位置，以便构建系统能够找到它们。 添加除核心之外的其他功能的新测试应添加到添加功能的层中，位于 `layer/lib/oeqa/runtime/cases`（只要在层的 `layer.conf` 文件中按常规方式扩展 `BBPATH`{.interpreted-text role="term"}）。 请记住以下内容：

- Filenames need to map directly to test (module) names.
- Do not use module names that collide with existing core tests.
- Minimally, an empty `__init__.py` file must be present in the runtime directory.

To create a new test, start by copying an existing module (e.g. `syslog.py` or `gcc.py` are good ones to use). Test modules can use code from `meta/lib/oeqa/utils`, which are helper classes.

> 要创建一个新的测试，可以从复制现有模块（例如 `syslog.py` 或 `gcc.py`）开始。测试模块可以使用 `meta/lib/oeqa/utils` 中的代码，这些是帮助类。

::: note
::: title
Note
:::

Structure shell commands such that you rely on them and they return a single code for success. Be aware that sometimes you will need to parse the output. See the `df.py` and `date.py` modules for examples.

> 结构化 shell 命令，使您可以依赖它们，并且它们返回一个成功代码。请注意，有时您需要解析输出。请参阅 `df.py` 和 `date.py` 模块以获取示例。
> :::

You will notice that all test classes inherit `oeRuntimeTest`, which is found in `meta/lib/oetest.py`. This base class offers some helper attributes, which are described in the following sections:

> 你会注意到所有测试类都继承自 `oeRuntimeTest`，它位于 `meta/lib/oetest.py` 中。这个基类提供了一些帮助属性，它们在以下部分有所描述：

## Class Methods

Class methods are as follows:

- *hasPackage(pkg):* Returns \"True\" if `pkg` is in the installed package list of the image, which is based on the manifest file that is generated during the `ref-tasks-rootfs`{.interpreted-text role="ref"} task.

> 返回“True”，如果 `pkg` 在映像的已安装包列表中，该列表基于在 `ref-tasks-rootfs` 任务期间生成的清单文件。

- *hasFeature(feature):* Returns \"True\" if the feature is in `IMAGE_FEATURES`{.interpreted-text role="term"} or `DISTRO_FEATURES`{.interpreted-text role="term"}.

> 返回“True”，如果特征在 IMAGE_FEATURES 或 DISTRO_FEATURES 中。

## Class Attributes

Class attributes are as follows:

- *pscmd:* Equals \"ps -ef\" if `procps` is installed in the image. Otherwise, `pscmd` equals \"ps\" (busybox).
- *tc:* The called test context, which gives access to the following attributes:
  - *d:* The BitBake datastore, which allows you to use stuff such as `oeRuntimeTest.tc.d.getVar("VIRTUAL-RUNTIME_init_manager")`.
  - *testslist and testsrequired:* Used internally. The tests do not need these.
  - *filesdir:* The absolute path to `meta/lib/oeqa/runtime/files`, which contains helper files for tests meant for copying on the target such as small files written in C for compilation.

> -*filesdir*：指向 `meta/lib/oeqa/runtime/files` 的绝对路径，其中包含用于将文件复制到目标上的测试辅助文件，例如用 C 编写的小文件用于编译。

- *target:* The target controller object used to deploy and start an image on a particular target (e.g. Qemu, SimpleRemote, and SystemdbootTarget). Tests usually use the following:

> 目标控制器对象用于在特定目标上部署和启动图像（例如 Qemu、SimpleRemote 和 SystemdbootTarget）。测试通常使用以下内容：
> - *ip:* The target\'s IP address.
> - *server_ip:* The host\'s IP address, which is usually used by the DNF test suite.
> - *run(cmd, timeout=None):* The single, most used method. This command is a wrapper for: `ssh root@host "cmd"`. The command returns a tuple: (status, output), which are what their names imply - the return code of \"cmd\" and whatever output it produces. The optional timeout argument represents the number of seconds the test should wait for \"cmd\" to return. If the argument is \"None\", the test uses the default instance\'s timeout period, which is 300 seconds. If the argument is \"0\", the test runs until the command returns.
> - *copy_to(localpath, remotepath):* `scp localpath root@ip:remotepath`.
> - *copy_from(remotepath, localpath):* `scp root@host:remotepath localpath`.

## Instance Attributes

There is a single instance attribute, which is `target`. The `target` instance attribute is identical to the class attribute of the same name, which is described in the previous section. This attribute exists as both an instance and class attribute so tests can use `self.target.run(cmd)` in instance methods instead of `oeRuntimeTest.tc.target.run(cmd)`.

> 有一个单一的实例属性，即 `target`。该 `target` 实例属性与前一节中描述的同名类属性完全相同。这个属性既存在于实例属性也存在于类属性，因此测试可以在实例方法中使用 `self.target.run(cmd)` 而不是 `oeRuntimeTest.tc.target.run(cmd)`。

# Installing Packages in the DUT Without the Package Manager

When a test requires a package built by BitBake, it is possible to install that package. Installing the package does not require a package manager be installed in the device under test (DUT). It does, however, require an SSH connection and the target must be using the `sshcontrol` class.

> 当测试需要一个由 BitBake 构建的软件包时，可以安装该软件包。安装软件包不需要在测试设备（DUT）中安装软件包管理器。但是，它确实需要一个 SSH 连接，目标必须使用 `sshcontrol` 类。

::: note
::: title
Note
:::

This method uses `scp` to copy files from the host to the target, which causes permissions and special attributes to be lost.
:::

A JSON file is used to define the packages needed by a test. This file must be in the same path as the file used to define the tests. Furthermore, the filename must map directly to the test module name with a `.json` extension.

> 一个 JSON 文件用于定义测试所需的包。此文件必须与用于定义测试的文件处于相同的路径中。此外，文件名必须与带有 `.json` 扩展名的测试模块名直接映射。

The JSON file must include an object with the test name as keys of an object or an array. This object (or array of objects) uses the following data:

- \"pkg\" \-\-- a mandatory string that is the name of the package to be installed.
- \"rm\" \-\-- an optional boolean, which defaults to \"false\", that specifies to remove the package after the test.
- \"extract\" \-\-- an optional boolean, which defaults to \"false\", that specifies if the package must be extracted from the package format. When set to \"true\", the package is not automatically installed into the DUT.

> - “提取”--一个可选的布尔值，默认为“false”，用于指定是否必须从包格式中提取包。设置为“true”时，该包不会自动安装到 DUT 中。

Following is an example JSON file that handles test \"foo\" installing package \"bar\" and test \"foobar\" installing packages \"foo\" and \"bar\". Once the test is complete, the packages are removed from the DUT:

> 以下是一个处理测试“foo”安装包“bar”和测试“foobar”安装包“foo”和“bar”的示例 JSON 文件。一旦测试完成，包将从 DUT 中删除。

```
{
    "foo": {
        "pkg": "bar"
    },
    "foobar": [
        {
            "pkg": "foo",
            "rm": true
        },
        {
            "pkg": "bar",
            "rm": true
        }
    ]
}
```
