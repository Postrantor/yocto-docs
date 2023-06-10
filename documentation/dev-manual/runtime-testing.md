---
tip: translate by baidu@2023-06-07 17:15:58
...
---
title: Performing Automated Runtime Testing
-------------------------------------------

The OpenEmbedded build system makes available a series of automated tests for images to verify runtime functionality. You can run these tests on either QEMU or actual target hardware. Tests are written in Python making use of the `unittest` module, and the majority of them run commands on the target system over SSH. This section describes how you set up the environment to use these tests, run available tests, and write and add your own tests.

> OpenEmbedded 构建系统提供了一系列图像自动测试，以验证运行时功能。您可以在 QEMU 或实际目标硬件上运行这些测试。测试是使用“unittest”模块用 Python 编写的，其中大多数测试通过 SSH 在目标系统上运行命令。本节介绍如何设置环境以使用这些测试、运行可用的测试以及编写和添加自己的测试。

For information on the test and QA infrastructure available within the Yocto Project, see the \"`ref-manual/release-process:testing and quality assurance`{.interpreted-text role="ref"}\" section in the Yocto Project Reference Manual.

> 有关 Yocto 项目中可用的测试和 QA 基础设施的信息，请参阅 Yocto《项目参考手册》中的“`ref manual/release process:testing and quality assurance`｛.explored text role=”ref“｝”一节。

# Enabling Tests

Depending on whether you are planning to run tests using QEMU or on the hardware, you have to take different steps to enable the tests. See the following subsections for information on how to enable both types of tests.

> 根据您是计划使用 QEMU 还是在硬件上运行测试，您必须采取不同的步骤来启用测试。有关如何启用这两种类型的测试的信息，请参阅以下小节。

## Enabling Runtime Tests on QEMU

In order to run tests, you need to do the following:

> 为了运行测试，您需要执行以下操作：

- *Set up to avoid interaction with sudo for networking:* To accomplish this, you must do one of the following:

> -*设置以避免与 sudo 进行网络交互：*要完成此操作，您必须执行以下操作之一：

- Add `NOPASSWD` for your user in `/etc/sudoers` either for all commands or just for `runqemu-ifup`. You must provide the full path as that can change if you are using multiple clones of the source repository.

> -在“/etc/sudoers”中为用户添加“NOPASSWD”，用于所有命令或仅用于“runqemu ifup”。如果使用源存储库的多个克隆，则必须提供完整路径，因为这可能会更改。

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

> -以 root 用户身份运行“scripts/runqemu gen tapdevs”中的脚本，该脚本应生成一个 tap 设备列表。这是通常为 Autobuilder 类型环境选择的选项。

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

> -*设置 DISPLAY 变量：*您需要设置此变量，以便有一个 X 服务器可用（例如，为无头机器启动“vncserver”）。

- *Be sure your host\'s firewall accepts incoming connections from 192.168.7.0/24:* Some of the tests (in particular DNF tests) start an HTTP server on a random high number port, which is used to serve files to the target. The DNF module serves `${WORKDIR}/oe-rootfs-repo` so it can run DNF channel commands. That means your host\'s firewall must accept incoming connections from 192.168.7.0/24, which is the default IP range used for tap devices by `runqemu`.

> -*确保主机的防火墙接受来自 192.168.7.0/24 的传入连接：*一些测试（特别是 DNF 测试）在随机的高数字端口上启动 HTTP 服务器，该端口用于向目标提供文件。DNF 模块提供“${WORKDIR}/oe rootfs repo”，因此它可以运行 DNF 通道命令。这意味着主机的防火墙必须接受来自 192.168.7.0/24 的传入连接，这是“runqemu”用于窃听设备的默认 IP 范围。

- *Be sure your host has the correct packages installed:* Depending your host\'s distribution, you need to have the following packages installed:

> -*请确保您的主机安装了正确的软件包：*根据主机的分发情况，您需要安装以下软件包：

- Ubuntu and Debian: `sysstat` and `iproute2`
- openSUSE: `sysstat` and `iproute2`
- Fedora: `sysstat` and `iproute`
- CentOS: `sysstat` and `iproute`

Once you start running the tests, the following happens:

> 一旦开始运行测试，就会发生以下情况：

1. A copy of the root filesystem is written to `${WORKDIR}/testimage`.

> 1.将根文件系统的副本写入“${WORKDIR}/testimage”。

2. The image is booted under QEMU using the standard `runqemu` script.

> 2.使用标准的“runqemu”脚本在 QEMU 下启动映像。

3. A default timeout of 500 seconds occurs to allow for the boot process to reach the login prompt. You can change the timeout period by setting `TEST_QEMUBOOT_TIMEOUT`{.interpreted-text role="term"} in the `local.conf` file.

> 3.默认超时 500 秒，以允许引导进程到达登录提示。您可以通过在 `local.conf` 文件中设置 `TEST_QEMUBOOT_timeout`｛.explored text role=“term”｝来更改超时时间。

4. Once the boot process is reached and the login prompt appears, the tests run. The full boot log is written to `${WORKDIR}/testimage/qemu_boot_log`.

> 4.一旦到达引导过程并出现登录提示，测试就会运行。完整的启动日志被写入“${WORKDIR}/testimage/qemuboot_log”。

5. Each test module loads in the order found in `TEST_SUITES`{.interpreted-text role="term"}. You can find the full output of the commands run over SSH in `${WORKDIR}/testimgage/ssh_target_log`.

> 5.每个测试模块按照 `test_SUITES`｛.explored text role=“term”｝中的顺序加载。您可以在 `${WORKDIR}/testimgage/SSH_target_log` 中找到通过 SSH 运行的命令的完整输出。

6. If no failures occur, the task running the tests ends successfully. You can find the output from the `unittest` in the task log at `${WORKDIR}/temp/log.do_testimage`.

> 6.如果没有发生故障，则运行测试的任务将成功结束。您可以在任务日志“${WORKDIR}/temp/log.do_testimage”中找到“单元测试”的输出。

## Enabling Runtime Tests on Hardware

The OpenEmbedded build system can run tests on real hardware, and for certain devices it can also deploy the image to be tested onto the device beforehand.

> OpenEmbedded 构建系统可以在实际硬件上运行测试，对于某些设备，它还可以预先将要测试的映像部署到设备上。

For automated deployment, a \"controller image\" is installed onto the hardware once as part of setup. Then, each time tests are to be run, the following occurs:

> 对于自动部署，作为安装的一部分，将“控制器映像”安装到硬件上一次。然后，每次运行测试时，都会发生以下情况：

1. The controller image is booted into and used to write the image to be tested to a second partition.

> 1.控制器映像被引导到第二分区中并用于将要测试的映像写入第二分区。

2. The device is then rebooted using an external script that you need to provide.

> 2.然后使用您需要提供的外部脚本重新启动设备。

3. The device boots into the image to be tested.

> 3.设备引导到要测试的图像中。

When running tests (independent of whether the image has been deployed automatically or not), the device is expected to be connected to a network on a pre-determined IP address. You can either use static IP addresses written into the image, or set the image to use DHCP and have your DHCP server on the test network assign a known IP address based on the MAC address of the device.

> 运行测试时（与映像是否已自动部署无关），设备应连接到预定 IP 地址上的网络。您可以使用写入映像中的静态 IP 地址，也可以将映像设置为使用 DHCP，并让测试网络上的 DHCP 服务器根据设备的 MAC 地址分配一个已知的 IP 地址。

In order to run tests on hardware, you need to set `TEST_TARGET`{.interpreted-text role="term"} to an appropriate value. For QEMU, you do not have to change anything, the default value is \"qemu\". For running tests on hardware, the following options are available:

> 为了在硬件上运行测试，您需要将 `TEST_TARGET`｛.explored text role=“term”｝设置为适当的值。对于 QEMU，您不必更改任何内容，默认值为“QEMU”。对于在硬件上运行测试，可以使用以下选项：

- *\"simpleremote\":* Choose \"simpleremote\" if you are going to run tests on a target system that is already running the image to be tested and is available on the network. You can use \"simpleremote\" in conjunction with either real hardware or an image running within a separately started QEMU or any other virtual machine manager.

> -*\“simpleremote”：*如果要在已经运行要测试的映像并可在网络上使用的目标系统上运行测试，请选择\“simpleremote”。您可以将“simpleremote”与实际硬件或在单独启动的 QEMU 或任何其他虚拟机管理器中运行的映像结合使用。

- *\"SystemdbootTarget\":* Choose \"SystemdbootTarget\" if your hardware is an EFI-based machine with `systemd-boot` as bootloader and `core-image-testmaster` (or something similar) is installed. Also, your hardware under test must be in a DHCP-enabled network that gives it the same IP address for each reboot.

> -*\“SystemdbootTarget\”：*如果您的硬件是一台以“systemd boot”作为引导加载程序并安装了“core image testmaster”（或类似的东西）的基于 EFI 的机器，请选择\“Systemdb ootTarget”。此外，测试中的硬件必须位于启用 DHCP 的网络中，该网络在每次重新启动时都为其提供相同的 IP 地址。

If you choose \"SystemdbootTarget\", there are additional requirements and considerations. See the \"`dev-manual/runtime-testing:selecting systemdboottarget`{.interpreted-text role="ref"}\" section, which follows, for more information.

> 如果选择“SystemdbootTarget\”，则会有其他要求和注意事项。有关详细信息，请参阅下面的\“`dev manual/runtime tests:selection systemdboottarget`｛.explored text role=“ref”｝\”一节。

- *\"BeagleBoneTarget\":* Choose \"BeagleBoneTarget\" if you are deploying images and running tests on the BeagleBone \"Black\" or original \"White\" hardware. For information on how to use these tests, see the comments at the top of the BeagleBoneTarget `meta-yocto-bsp/lib/oeqa/controllers/beaglebonetarget.py` file.

> -*\“BeagleBoneTarget\”：*如果要在 BeagleBone\“Black\”或原始\“White\”硬件上部署映像并运行测试，请选择\“BeableBoneTarget”。有关如何使用这些测试的信息，请参阅 BeagleBoneTarget `meta yocto nbsp/lib/oeqa/controllers/BeagleBoneTarget.py` 文件顶部的注释。

- *\"EdgeRouterTarget\":* Choose \"EdgeRouterTarget\" if you are deploying images and running tests on the Ubiquiti Networks EdgeRouter Lite. For information on how to use these tests, see the comments at the top of the EdgeRouterTarget `meta-yocto-bsp/lib/oeqa/controllers/edgeroutertarget.py` file.

> -*\“EdgeRouterTarget\”：*如果您正在 Ubiquiti Networks EdgeRouter Lite 上部署映像并运行测试，请选择\“EdgeRouterTarget\”。有关如何使用这些测试的信息，请参阅 EdgeRouterTarget `meta yocto nbsp/lib/oeqa/controllers/edgerroutertarget.py ` 文件顶部的注释。

- *\"GrubTarget\":* Choose \"GrubTarget\" if you are deploying images and running tests on any generic PC that boots using GRUB. For information on how to use these tests, see the comments at the top of the GrubTarget `meta-yocto-bsp/lib/oeqa/controllers/grubtarget.py` file.

> -*\“GrubTarget\”：*如果您在任何使用 GRUB 启动的通用 PC 上部署映像并运行测试，请选择\“GRUB Target\”。有关如何使用这些测试的信息，请参阅 GrubTarget `meta yocto nbsp/lib/oeqa/controllers/GrubTarget.py` 文件顶部的注释。

- *\"your-target\":* Create your own custom target if you want to run tests when you are deploying images and running tests on a custom machine within your BSP layer. To do this, you need to add a Python unit that defines the target class under `lib/oeqa/controllers/` within your layer. You must also provide an empty `__init__.py`. For examples, see files in `meta-yocto-bsp/lib/oeqa/controllers/`.

> -*\“您的目标”：*如果您想在 BSP 层内的自定义机器上部署映像和运行测试，请创建自己的自定义目标。要做到这一点，您需要添加一个 Python 单元，该单元在层中的“lib/oeqa/controllers/”下定义目标类。您还必须提供一个空的 `__init__.py`。例如，请参阅 `meta yocto nbsp/lib/oeqa/controllers/` 中的文件。

## Selecting SystemdbootTarget

If you did not set `TEST_TARGET`{.interpreted-text role="term"} to \"SystemdbootTarget\", then you do not need any information in this section. You can skip down to the \"`dev-manual/runtime-testing:running tests`{.interpreted-text role="ref"}\" section.

> 如果您没有将 `TEST_TARGET`｛.explored text role=“term”｝设置为\“SystemdbootTarget\”，则不需要本节中的任何信息。您可以跳到\“`dev manual/runtime testing:running tests`｛.depreted text role=“ref”｝\”部分。

If you did set `TEST_TARGET`{.interpreted-text role="term"} to \"SystemdbootTarget\", you also need to perform a one-time setup of your controller image by doing the following:

> 如果您确实将 `TEST_TARGET`｛.explored text role=“term”｝设置为\“SystemdbootTarget\”，您还需要通过执行以下操作一次性设置控制器映像：

1. *Set EFI_PROVIDER:* Be sure that `EFI_PROVIDER`{.interpreted-text role="term"} is as follows:

> 1.*设置 EFI_PROVIDER:*确保 `EFI_PROVIDER`｛.explored text role=“term”｝如下：

```

> ```

EFI_PROVIDER = "systemd-boot"

> EFI_PROVIDER=“系统引导”

```

> ```
> ```

2. *Build the controller image:* Build the `core-image-testmaster` image. The `core-image-testmaster` recipe is provided as an example for a \"controller\" image and you can customize the image recipe as you would any other recipe.

> 2.*构建控制器镜像：*构建“核心镜像 testmaster”镜像。“核心图像测试大师”配方是作为“控制器”图像的示例提供的，您可以像自定义任何其他配方一样自定义图像配方。

Here are the image recipe requirements:

> 以下是图像配方要求：

- Inherits `core-image` so that kernel modules are installed.

> -继承“核心映像”，以便安装内核模块。

- Installs normal linux utilities not BusyBox ones (e.g. `bash`, `coreutils`, `tar`, `gzip`, and `kmod`).

> -安装普通的 linux 实用程序，而不是 BusyBox 实用程序（例如“bash”、“coreutils”、“tar”、“gzip”和“kmod”）。

- Uses a custom `Initramfs`{.interpreted-text role="term"} image with a custom installer. A normal image that you can install usually creates a single root filesystem partition. This image uses another installer that creates a specific partition layout. Not all Board Support Packages (BSPs) can use an installer. For such cases, you need to manually create the following partition layout on the target:

> -将自定义 `Initramfs`｛.explored text role=“term”｝图像与自定义安装程序一起使用。可以安装的普通映像通常会创建一个根文件系统分区。此映像使用另一个创建特定分区布局的安装程序。并非所有板支持包（BSP）都可以使用安装程序。对于这种情况，您需要在目标上手动创建以下分区布局：
>
> - First partition mounted under `/boot`, labeled \"boot\".
> - The main root filesystem partition where this image gets installed, which is mounted under `/`.
> - Another partition labeled \"testrootfs\" where test images get deployed.

3. *Install image:* Install the image that you just built on the target system.

> 3.*安装镜像：*在目标系统上安装刚才构建的镜像。

The final thing you need to do when setting `TEST_TARGET`{.interpreted-text role="term"} to \"SystemdbootTarget\" is to set up the test image:

> 将 `TEST_TARGET`｛.explored text role=“term”｝设置为\“SystemdbootTarget\”时需要做的最后一件事是设置测试映像：

1. *Set up your local.conf file:* Make sure you have the following statements in your `local.conf` file:

> 1.*设置 local.conf 文件：*确保“local.conf”文件中有以下语句：

```

> ```

IMAGE_FSTYPES += "tar.gz"

> IMAGE_FSTYPES+=“tar.gz”

INHERIT += "testimage"

> INHERIT+=“测试图像”

TEST_TARGET = "SystemdbootTarget"

> TEST_TARGET=“系统根目标”

TEST_TARGET_IP = "192.168.2.3"

> TEST_TARGET_IP=“192.168.2.3”

```

> ```
> ```

2. *Build your test image:* Use BitBake to build the image:

> 2.*构建测试图像：*使用 BitBake 构建图像：

```

> ```

$ bitbake core-image-sato

> $bitbake核心图像sato

```

> ```
> ```

## Power Control

For most hardware targets other than \"simpleremote\", you can control power:

> 对于“simpleremote”以外的大多数硬件目标，您可以控制电源：

- You can use `TEST_POWERCONTROL_CMD`{.interpreted-text role="term"} together with `TEST_POWERCONTROL_EXTRA_ARGS`{.interpreted-text role="term"} as a command that runs on the host and does power cycling. The test code passes one argument to that command: off, on or cycle (off then on). Here is an example that could appear in your `local.conf` file:

> -您可以将 `TEST_POWERCONTROL_CMD`｛.depreted text role=“term”｝与 `TEST_PoweERCONTROL_EXTRA_ARGS`｛.repreted text role=“term“｝一起用作在主机上运行并执行电源循环的命令。测试代码将一个参数传递给该命令：off、on 或 cycle（off 然后 on）。下面是一个可能出现在“local.conf”文件中的示例：

```
TEST_POWERCONTROL_CMD = "powercontrol.exp test 10.11.12.1 nuc1"
```

In this example, the expect script does the following:

> 在本例中，预期脚本执行以下操作：

```shell
ssh test@10.11.12.1 "pyctl nuc1 arg"
```

It then runs a Python script that controls power for a label called `nuc1`.

> 然后，它运行一个 Python 脚本，控制一个名为“nuc1”的标签的电源。

::: note
::: title

Note

> 笔记
> :::

You need to customize `TEST_POWERCONTROL_CMD`{.interpreted-text role="term"} and `TEST_POWERCONTROL_EXTRA_ARGS`{.interpreted-text role="term"} for your own setup. The one requirement is that it accepts \"on\", \"off\", and \"cycle\" as the last argument.

> 您需要为自己的设置自定义 `TEST_POWERCONTROL_CMD`｛.depreted text role=“term”｝和 `TEST_PoweERCONTROL_EXTRA_ARGS`｛.repreted text role=“term“｝。一个要求是它接受“on”、“off”和“cycle”作为最后一个参数。
> :::

- When no command is defined, it connects to the device over SSH and uses the classic reboot command to reboot the device. Classic reboot is fine as long as the machine actually reboots (i.e. the SSH test has not failed). It is useful for scenarios where you have a simple setup, typically with a single board, and where some manual interaction is okay from time to time.

> -如果未定义任何命令，它将通过 SSH 连接到设备，并使用经典的重新启动命令重新启动设备。只要机器真正重新启动（即 SSH 测试没有失败），经典的重新启动就可以了。它适用于设置简单的场景，通常使用单个板，并且可以不时进行一些手动交互。

If you have no hardware to automatically perform power control but still wish to experiment with automated hardware testing, you can use the `dialog-power-control` script that shows a dialog prompting you to perform the required power action. This script requires either KDialog or Zenity to be installed. To use this script, set the `TEST_POWERCONTROL_CMD`{.interpreted-text role="term"} variable as follows:

> 如果您没有自动执行电源控制的硬件，但仍希望尝试自动硬件测试，则可以使用“对话框电源控制”脚本，该脚本显示一个对话框，提示您执行所需的电源操作。此脚本要求安装 KDialog 或 Zenity。要使用此脚本，请按如下方式设置 `TEST_POWERCONTROL_CMD`｛.depreted text role=“term”｝变量：

```
TEST_POWERCONTROL_CMD = "${COREBASE}/scripts/contrib/dialog-power-control"
```

## Serial Console Connection

For test target classes requiring a serial console to interact with the bootloader (e.g. BeagleBoneTarget, EdgeRouterTarget, and GrubTarget), you need to specify a command to use to connect to the serial console of the target machine by using the `TEST_SERIALCONTROL_CMD`{.interpreted-text role="term"} variable and optionally the `TEST_SERIALCONTROL_EXTRA_ARGS`{.interpreted-text role="term"} variable.

> 对于需要串行控制台与引导加载程序交互的测试目标类（例如 BeagleBoneTarget、EdgeRouterTarget 和 GrubTarget），您需要指定一个命令，以便使用 `TEST_SERIALCONTROL_CMD`｛.explored text role=“term”｝变量和 `TEST_SERIALCONTROL_EXTRA_ARGS`{.explered text rol=”term“｝变量连接到目标计算机的串行控制台。

These cases could be a serial terminal program if the machine is connected to a local serial port, or a `telnet` or `ssh` command connecting to a remote console server. Regardless of the case, the command simply needs to connect to the serial console and forward that connection to standard input and output as any normal terminal program does. For example, to use the picocom terminal program on serial device `/dev/ttyUSB0` at 115200bps, you would set the variable as follows:

> 如果机器连接到本地串行端口，这些情况可能是串行终端程序，或者是连接到远程控制台服务器的“telnet”或“ssh”命令。无论何种情况，该命令只需连接到串行控制台，并像任何普通终端程序一样将该连接转发到标准输入和输出。例如，要在 115200bps 的串行设备“/dev/ttyUSB0”上使用 picocom 终端程序，您可以按如下方式设置变量：

```
TEST_SERIALCONTROL_CMD = "picocom /dev/ttyUSB0 -b 115200"
```

For local devices where the serial port device disappears when the device reboots, an additional \"serdevtry\" wrapper script is provided. To use this wrapper, simply prefix the terminal command with `${COREBASE}/scripts/contrib/serdevtry`:

> 对于当设备重新启动时串行端口设备消失的本地设备，提供了一个额外的“serdevtry”包装脚本。要使用这个包装器，只需在终端命令前面加上“$｛COREBASE｝/scripts/contrib/serdevtry”：

```
TEST_SERIALCONTROL_CMD = "${COREBASE}/scripts/contrib/serdevtry picocom -b 115200 /dev/ttyUSB0"
```

# Running Tests

You can start the tests automatically or manually:

> 您可以自动或手动启动测试：

- *Automatically running tests:* To run the tests automatically after the OpenEmbedded build system successfully creates an image, first set the `TESTIMAGE_AUTO`{.interpreted-text role="term"} variable to \"1\" in your `local.conf` file in the `Build Directory`{.interpreted-text role="term"}:

> -*自动运行测试：*若要在 OpenEmbedded 构建系统成功创建映像后自动运行测试，请首先在“构建目录”中的“local.conf”文件中将 `TESTIMAGE_AUTO`｛.depreted text role=“term”｝变量设置为\“1\

```
TESTIMAGE_AUTO = "1"
```

Next, build your image. If the image successfully builds, the tests run:

> 接下来，塑造你的形象。如果成功构建映像，则运行测试：

```
bitbake core-image-sato
```

- *Manually running tests:* To manually run the tests, first globally inherit the `ref-classes-testimage`{.interpreted-text role="ref"} class by editing your `local.conf` file:

> -*手动运行测试：*要手动运行测试，请首先通过编辑 `local.conf` 文件全局继承 `ref classes testimage`｛.depreted text role=“ref”｝类：

```
INHERIT += "testimage"
```

Next, use BitBake to run the tests:

> 接下来，使用 BitBake 运行测试：

```
bitbake -c testimage image
```

All test files reside in `meta/lib/oeqa/runtime/cases` in the `Source Directory`{.interpreted-text role="term"}. A test name maps directly to a Python module. Each test module may contain a number of individual tests. Tests are usually grouped together by the area tested (e.g tests for systemd reside in `meta/lib/oeqa/runtime/cases/systemd.py`).

> 所有测试文件都位于“源目录”中的“meta/lib/oeqa/runtime/cases”｛.depreted text role=“term”｝中。测试名称直接映射到 Python 模块。每个测试模块可能包含许多单独的测试。测试通常按测试区域分组（例如，systemd 的测试位于“meta/lib/oeqa/runtime/cases/systemd.py”中）。

You can add tests to any layer provided you place them in the proper area and you extend `BBPATH`{.interpreted-text role="term"} in the `local.conf` file as normal. Be sure that tests reside in `layer/lib/oeqa/runtime/cases`.

> 您可以将测试添加到任何层，前提是将它们放置在适当的区域中，并像正常情况一样在 `local.conf` 文件中扩展 `BBPATH`{.depreted text role=“term”}。请确保测试位于“layer/lib/oeqa/runtime/cases”中。

::: note
::: title
Note
:::

Be sure that module names do not collide with module names used in the default set of test modules in `meta/lib/oeqa/runtime/cases`.

> 请确保模块名称不会与“meta/lib/oeqa/runtime/cases”中的默认测试模块集中使用的模块名称冲突。
> :::

You can change the set of tests run by appending or overriding `TEST_SUITES`{.interpreted-text role="term"} variable in `local.conf`. Each name in `TEST_SUITES`{.interpreted-text role="term"} represents a required test for the image. Test modules named within `TEST_SUITES`{.interpreted-text role="term"} cannot be skipped even if a test is not suitable for an image (e.g. running the RPM tests on an image without `rpm`). Appending \"auto\" to `TEST_SUITES`{.interpreted-text role="term"} causes the build system to try to run all tests that are suitable for the image (i.e. each test module may elect to skip itself).

> 您可以通过在 `local.conf` 中附加或重写 `TEST_SUITES`｛.explored text role=“term”｝变量来更改运行的测试集。`TEST_SUITES`｛.explored textrole=”term“｝中的每个名称表示映像所需的测试。即使测试不适用于图像，也不能跳过在 `Test_SUITES`｛.explored text role=“term”｝中命名的测试模块（例如，在没有 `RPM'的图像上运行RPM测试）。将\“auto\”附加到` TEST_SUITES`｛.explored text role=“term”｝会导致构建系统尝试运行适用于映像的所有测试（即，每个测试模块可以选择跳过自己）。

The order you list tests in `TEST_SUITES`{.interpreted-text role="term"} is important and influences test dependencies. Consequently, tests that depend on other tests should be added after the test on which they depend. For example, since the `ssh` test depends on the `ping` test, \"ssh\" needs to come after \"ping\" in the list. The test class provides no re-ordering or dependency handling.

> 在 `TEST_SUITES`｛.explored text role=“term”｝中列出测试的顺序很重要，会影响测试依赖关系。因此，依赖于其他测试的测试应该添加到它们所依赖的测试之后。例如，由于“ssh”测试依赖于“ping”测试，因此“ssh”需要位于列表中的“ping”之后。测试类不提供重新排序或依赖关系处理。

::: note
::: title
Note
:::

Each module can have multiple classes with multiple test methods. And, Python `unittest` rules apply.

> 每个模块可以有多个类和多个测试方法。而且，Python 的“统一测试”规则适用。
> :::

Here are some things to keep in mind when running tests:

> 运行测试时需要记住以下几点：

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

> 记住，秩序很重要。请确保稍后在订单中放置一个依赖于另一个测试的测试。

# Exporting Tests

You can export tests so that they can run independently of the build system. Exporting tests is required if you want to be able to hand the test execution off to a scheduler. You can only export tests that are defined in `TEST_SUITES`{.interpreted-text role="term"}.

> 您可以导出测试，以便它们可以独立于生成系统运行。如果您希望能够将测试执行交给调度程序，则需要导出测试。您只能导出在 `TEST_SUITES`｛.explored text role=“term”｝中定义的测试。

If your image is already built, make sure the following are set in your `local.conf` file:

> 如果已经构建了映像，请确保在“local.conf”文件中设置了以下内容：

```
INHERIT += "testexport"
TEST_TARGET_IP = "IP-address-for-the-test-target"
TEST_SERVER_IP = "IP-address-for-the-test-server"
```

You can then export the tests with the following BitBake command form:

> 然后，您可以使用以下 BitBake 命令表单导出测试：

```
$ bitbake image -c testexport
```

Exporting the tests places them in the `Build Directory`{.interpreted-text role="term"} in `tmp/testexport/` image, which is controlled by the `TEST_EXPORT_DIR`{.interpreted-text role="term"} variable.

> 导出测试会将它们放置在 `tmp/testexport/` image 中的 `Build Directory`｛.depredicted text role=“term”｝中，该目录由 `TEST_EXPORT_DIR`｛.epredicted textrole=”term“｝变量控制。

You can now run the tests outside of the build environment:

> 现在，您可以在构建环境之外运行测试：

```
$ cd tmp/testexport/image
$ ./runexported.py testdata.json
```

Here is a complete example that shows IP addresses and uses the `core-image-sato` image:

> 以下是一个完整的示例，显示 IP 地址并使用“核心图像 sato”图像：

```
INHERIT += "testexport"
TEST_TARGET_IP = "192.168.7.2"
TEST_SERVER_IP = "192.168.7.1"
```

Use BitBake to export the tests:

> 使用 BitBake 导出测试：

```
$ bitbake core-image-sato -c testexport
```

Run the tests outside of the build environment using the following:

> 使用以下方法在生成环境之外运行测试：

```
$ cd tmp/testexport/core-image-sato
$ ./runexported.py testdata.json
```

# Writing New Tests

As mentioned previously, all new test files need to be in the proper place for the build system to find them. New tests for additional functionality outside of the core should be added to the layer that adds the functionality, in `layer/lib/oeqa/runtime/cases` (as long as `BBPATH`{.interpreted-text role="term"} is extended in the layer\'s `layer.conf` file as normal). Just remember the following:

> 如前所述，所有新的测试文件都需要放在适当的位置，以便构建系统找到它们。核心之外的附加功能的新测试应添加到添加功能的层中，在“layer/lib/oeqa/runtime/cases”中（只要在层的“layer.conf”文件中正常扩展了“BBPATH`｛.explored text role=“term”｝）。只需记住以下几点：

- Filenames need to map directly to test (module) names.
- Do not use module names that collide with existing core tests.
- Minimally, an empty `__init__.py` file must be present in the runtime directory.

To create a new test, start by copying an existing module (e.g. `syslog.py` or `gcc.py` are good ones to use). Test modules can use code from `meta/lib/oeqa/utils`, which are helper classes.

> 要创建新的测试，请从复制现有模块开始（例如“syslog.py”或“gcc.py”是很好的使用方法）。测试模块可以使用“meta/lib/oeqa/utils”中的代码，这些代码是辅助类。

::: note
::: title
Note
:::

Structure shell commands such that you rely on them and they return a single code for success. Be aware that sometimes you will need to parse the output. See the `df.py` and `date.py` modules for examples.

> 构造 shell 命令，这样您就可以依赖它们，并且它们只返回一个成功的代码。请注意，有时您需要解析输出。有关示例，请参见“df.py”和“date.py”模块。
> :::

You will notice that all test classes inherit `oeRuntimeTest`, which is found in `meta/lib/oetest.py`. This base class offers some helper attributes, which are described in the following sections:

> 您会注意到，所有测试类都继承了“oeRuntimeTest”，它位于“meta/lib/oetest.py”中。这个基类提供了一些辅助属性，在以下部分中进行了描述：

## Class Methods

Class methods are as follows:

> 类方法如下：

- *hasPackage(pkg):* Returns \"True\" if `pkg` is in the installed package list of the image, which is based on the manifest file that is generated during the `ref-tasks-rootfs`{.interpreted-text role="ref"} task.

> -*hasPackage（pkg）：*如果 `pkg ` 位于映像的已安装程序包列表中，则返回\“True\”，该映像基于 `ref tasks rootfs`｛.depreted text role=“ref”｝任务期间生成的清单文件。

- *hasFeature(feature):* Returns \"True\" if the feature is in `IMAGE_FEATURES`{.interpreted-text role="term"} or `DISTRO_FEATURES`{.interpreted-text role="term"}.

> -*hasFeature（feature）：*如果该功能位于 `IMAGE_FATURE`｛.explored text role=“term”｝或 `DISTRO_feature`{.explered text rol=“term“｝中，则返回\“True\”。

## Class Attributes

Class attributes are as follows:

> 类属性如下：

- *pscmd:* Equals \"ps -ef\" if `procps` is installed in the image. Otherwise, `pscmd` equals \"ps\" (busybox).

> -*pscmd:*如果映像中安装了“procps”，则等于“ps-ef\”。否则，“pscmd”等于“ps\”（busybox）。

- *tc:* The called test context, which gives access to the following attributes:

  - *d:* The BitBake datastore, which allows you to use stuff such as `oeRuntimeTest.tc.d.getVar("VIRTUAL-RUNTIME_init_manager")`.

> -*d:*BitBake 数据存储，它允许您使用诸如 `oeRuntimeTest.tc.d.getVar（“VIRTUAL-RUNTIME_init_manager”）` 之类的东西。

- *testslist and testsrequired:* Used internally. The tests do not need these.
- *filesdir:* The absolute path to `meta/lib/oeqa/runtime/files`, which contains helper files for tests meant for copying on the target such as small files written in C for compilation.

> -*filesdir:*“meta/lib/oeqa/runtime/files”的绝对路径，其中包含用于在目标上复制测试的辅助文件，例如用 C 编写的用于编译的小文件。

- *target:* The target controller object used to deploy and start an image on a particular target (e.g. Qemu, SimpleRemote, and SystemdbootTarget). Tests usually use the following:

> -*target：*用于在特定目标（例如 Qemu、SimpleRemote 和 SystemdbootTarget）上部署和启动映像的目标控制器对象。测试通常使用以下内容：
>
> - *ip:* The target\'s IP address.
> - *server_ip:* The host\'s IP address, which is usually used by the DNF test suite.
> - *run(cmd, timeout=None):* The single, most used method. This command is a wrapper for: `ssh root@host "cmd"`. The command returns a tuple: (status, output), which are what their names imply - the return code of \"cmd\" and whatever output it produces. The optional timeout argument represents the number of seconds the test should wait for \"cmd\" to return. If the argument is \"None\", the test uses the default instance\'s timeout period, which is 300 seconds. If the argument is \"0\", the test runs until the command returns.
> - *copy_to(localpath, remotepath):* `scp localpath root@ip:remotepath`.
> - *copy_from(remotepath, localpath):* `scp root@host:remotepath localpath`.

## Instance Attributes

There is a single instance attribute, which is `target`. The `target` instance attribute is identical to the class attribute of the same name, which is described in the previous section. This attribute exists as both an instance and class attribute so tests can use `self.target.run(cmd)` in instance methods instead of `oeRuntimeTest.tc.target.run(cmd)`.

> 有一个实例属性，即“target”。“target”实例属性与上一节中描述的具有相同名称的类属性相同。此属性同时作为实例和类属性存在，因此测试可以在实例方法中使用“self.target.run（cmd）”，而不是“oeRuntimeTest.tc.target.run”。

# Installing Packages in the DUT Without the Package Manager

When a test requires a package built by BitBake, it is possible to install that package. Installing the package does not require a package manager be installed in the device under test (DUT). It does, however, require an SSH connection and the target must be using the `sshcontrol` class.

> 当测试需要 BitBake 构建的包时，可以安装该包。安装封装不需要在被测器件（DUT）中安装封装管理器。但是，它确实需要 SSH 连接，并且目标必须使用“sshcontrol”类。

::: note
::: title
Note
:::

This method uses `scp` to copy files from the host to the target, which causes permissions and special attributes to be lost.

> 此方法使用“scp”将文件从主机复制到目标，这会导致权限和特殊属性丢失。
> :::

A JSON file is used to define the packages needed by a test. This file must be in the same path as the file used to define the tests. Furthermore, the filename must map directly to the test module name with a `.json` extension.

> JSON 文件用于定义测试所需的包。此文件必须与用于定义测试的文件位于同一路径中。此外，文件名必须直接映射到扩展名为“.json”的测试模块名称。

The JSON file must include an object with the test name as keys of an object or an array. This object (or array of objects) uses the following data:

> JSON 文件必须包含一个具有测试名称的对象作为对象或数组的键。此对象（或对象阵列）使用以下数据：

- \"pkg\" \-\-- a mandatory string that is the name of the package to be installed.
- \"rm\" \-\-- an optional boolean, which defaults to \"false\", that specifies to remove the package after the test.

> -\“rm\”\-一个可选的布尔值，默认为\“false\”，用于指定在测试后删除包。

- \"extract\" \-\-- an optional boolean, which defaults to \"false\", that specifies if the package must be extracted from the package format. When set to \"true\", the package is not automatically installed into the DUT.

> -\“extract\”\-一个可选的布尔值，默认为\“false”，用于指定是否必须从包格式中提取包。当设置为“true”时，软件包不会自动安装到 DUT 中。

Following is an example JSON file that handles test \"foo\" installing package \"bar\" and test \"foobar\" installing packages \"foo\" and \"bar\". Once the test is complete, the packages are removed from the DUT:

> 下面是一个示例 JSON 文件，用于处理 test\“foo\”安装包\“bar\”和 test\“foobar\”安装程序包\“foo”和\“bar\”。测试完成后，从 DUT 上取下封装：

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
