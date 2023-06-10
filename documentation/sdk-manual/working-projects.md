---
tip: translate by openai@2023-06-07 21:39:48
...
---
title: Using the SDK Toolchain Directly
---------------------------------------

You can use the SDK toolchain directly with Makefile and Autotools-based projects.

> 你可以直接使用 SDK 工具链与基于 Makefile 和 Autotools 的项目。

# Autotools-Based Projects

Once you have a suitable `sdk-manual/intro:the cross-development toolchain`{.interpreted-text role="ref"} installed, it is very easy to develop a project using the `GNU Autotools-based <GNU_Build_System>`{.interpreted-text role="wikipedia"} workflow, which is outside of the `OpenEmbedded Build System`{.interpreted-text role="term"}.

> 一旦你安装了合适的“sdk-manual/intro：跨开发工具链”，使用基于“GNU Autotools”的“GNU 构建系统”工作流来开发一个项目就非常容易了，这是在“OpenEmbedded 构建系统”之外的。

The following figure presents a simple Autotools workflow.

> 下图展示了一个简单的 Autotools 工作流程。

![image](figures/sdk-autotools-flow.png){.align-center width="70.0%"}

Follow these steps to create a simple Autotools-based \"Hello World\" project:

> 请按照以下步骤创建一个基于 Autotools 的“Hello World”项目：

::: note
::: title
Note
:::

For more information on the GNU Autotools workflow, see the same example on the GNOME Developer site.

> 有关 GNU Autotools 工作流程的更多信息，请参见 GNOME 开发者网站上的相同示例。
> :::

1. *Create a Working Directory and Populate It:* Create a clean directory for your project and then make that directory your working location:

> 1. 创建工作目录并填充它：为您的项目创建一个干净的目录，然后将该目录设置为工作位置：

```

$ mkdir $HOME/helloworld

> $ mkdir $HOME/你好世界

$ cd $HOME/helloworld

> $ cd $HOME/你好世界
```

After setting up the directory, populate it with files needed for the flow. You need a project source file, a file to help with configuration, and a file to help create the Makefile, and a README file: `hello.c`, `configure.ac`, `Makefile.am`, and `README`, respectively.

> 在设置目录之后，用流程所需的文件来填充它。你需要一个项目源文件、一个帮助配置的文件、一个帮助创建 Makefile 的文件以及一个 README 文件：分别是 `hello.c`、`configure.ac`、`Makefile.am` 和 `README`。

Use the following command to create an empty README file, which is required by GNU Coding Standards:

> 使用以下命令创建一个空的 README 文件，这是 GNU 编码标准所要求的：

```

$ touch README

> $ 触摸README
```

Create the remaining three files as follows:

> 请按照以下方式创建剩余的三个文件：

- `hello.c`:

> 你好。c

```
```

#include <stdio.h>

main()
{
printf("Hello World!\n");
}

```
```

- `configure.ac`:

> - `configure.ac`：

```
```

AC_INIT(hello,0.1)
AM_INIT_AUTOMAKE([foreign])
AC_PROG_CC
AC_CONFIG_FILES(Makefile)
AC_OUTPUT

```
```

- `Makefile.am`:

> 制作文件 AM

```
```

bin_PROGRAMS = hello
hello_SOURCES = hello.c

```
```

2. *Source the Cross-Toolchain Environment Setup File:* As described earlier in the manual, installing the cross-toolchain creates a cross-toolchain environment setup script in the directory that the SDK was installed. Before you can use the tools to develop your project, you must source this setup script. The script begins with the string \"environment-setup\" and contains the machine architecture, which is followed by the string \"poky-linux\". For this example, the command sources a script from the default SDK installation directory that uses the 32-bit Intel x86 Architecture and the &DISTRO; Yocto Project release:

> 请按照本手册之前的说明安装交叉工具链，安装 SDK 会在该目录下创建一个交叉工具链环境设置脚本。在使用这些工具来开发项目之前，你必须源自这个设置脚本。该脚本以“environment-setup”字符串开头，后面跟着机器架构，其次是“poky-linux”字符串。在这个例子中，该命令源自于默认的 SDK 安装目录，使用 32 位 Intel x86 架构和&DISTRO; Yocto 项目发布版本。

```

$ source /opt/poky/&DISTRO;/environment-setup-i586-poky-linux

> $ 源/opt/poky/&DISTRO;/环境设置-i586-poky-linux
```

Another example is sourcing the environment setup directly in a Yocto build:

> 另一个例子是直接在 Yocto 构建中源环境设置：

```

$ source tmp/deploy/images/qemux86-64/environment-setup-core2-64-poky-linux

> $ 来源tmp/deploy/images/qemux86-64/environment-setup-core2-64-poky-linux
```

3. *Create the configure Script:* Use the `autoreconf` command to generate the `configure` script:

> 3. *创建配置脚本：*使用 `autoreconf` 命令生成 `configure` 脚本：

```

$ autoreconf

> $ autoreconf：重新生成自动化配置文件
```

The `autoreconf` tool takes care of running the other Autotools such as `aclocal`, `autoconf`, and `automake`.

> `autoreconf` 工具可以处理其他的自动化工具，比如 `aclocal`、`autoconf` 和 `automake`。

::: note
::: title

Note

> 注意
> :::

If you get errors from `configure.ac`, which `autoreconf` runs, that indicate missing files, you can use the \"-i\" option, which ensures missing auxiliary files are copied to the build host.

> 如果你从 `configure.ac` 得到的错误表明缺少文件，你可以使用“-i”选项，它可以确保缺少的辅助文件被复制到构建主机上。
> :::

4. *Cross-Compile the Project:* This command compiles the project using the cross-compiler. The `CONFIGURE_FLAGS`{.interpreted-text role="term"} environment variable provides the minimal arguments for GNU configure:

> 4. *交叉编译项目：*此命令使用交叉编译器编译项目。`CONFIGURE_FLAGS`{.interpreted-text role="term"}环境变量提供 GNU 配置的最小参数：

```

$ ./configure ${CONFIGURE_FLAGS}

> $ ./configure ${CONFIGURE_FLAGS}
```

For an Autotools-based project, you can use the cross-toolchain by just passing the appropriate host option to `configure.sh`. The host option you use is derived from the name of the environment setup script found in the directory in which you installed the cross-toolchain. For example, the host option for an ARM-based target that uses the GNU EABI is `armv5te-poky-linux-gnueabi`. You will notice that the name of the script is `environment-setup-armv5te-poky-linux-gnueabi`. Thus, the following command works to update your project and rebuild it using the appropriate cross-toolchain tools:

> 对于一个基于 Autotools 的项目，您可以通过向 configure.sh 传递适当的主机选项来使用跨工具链。您使用的主机选项源自您在安装跨工具链的目录中找到的环境设置脚本的名称。例如，使用 GNU EABI 的 ARM 目标的主机选项是 `armv5te-poky-linux-gnueabi`。您会注意到脚本的名称是 `environment-setup-armv5te-poky-linux-gnueabi`。因此，以下命令可以用来更新您的项目并使用适当的跨工具链工具重新构建它：

```

$ ./configure --host=armv5te-poky-linux-gnueabi --with-libtool-sysroot=sysroot_dir

> $ ./configure --host=armv5te-poky-linux-gnueabi --with-libtool-sysroot=sysroot_dir
```

5. *Make and Install the Project:* These two commands generate and install the project into the destination directory:

> 5. *创建和安装项目：* 使用这两个命令可以生成并安装项目到目标目录：

```

$ make

> $ 生成

$ make install DESTDIR=./tmp

> $ 将安装文件安装到./tmp目录
```

::: note
::: title

Note

> 注意
> :::

To learn about environment variables established when you run the cross-toolchain environment setup script and how they are used or overridden by the Makefile, see the `sdk-manual/working-projects:makefile-based projects`{.interpreted-text role="ref"} section.

> 要了解运行跨工具链环境设置脚本时建立的环境变量以及 Makefile 如何使用或覆盖它们，请参阅“sdk-manual/working-projects：基于 Makefile 的项目”部分。
> :::

This next command is a simple way to verify the installation of your project. Running the command prints the architecture on which the binary file can run. This architecture should be the same architecture that the installed cross-toolchain supports:

> 下一个命令是验证您项目安装的简单方法。运行该命令会打印可运行二进制文件的体系结构。此体系结构应与已安装的交叉工具链支持的体系结构相同：

```

$ file ./tmp/usr/local/bin/hello

> $ 文件 ./tmp/usr/local/bin/hello
```

6. *Execute Your Project:* To execute the project, you would need to run it on your target hardware. If your target hardware happens to be your build host, you could run the project as follows:

> 6. *执行项目：* 要执行项目，您需要在目标硬件上运行它。 如果您的目标硬件正好是构建主机，则可以按照以下方式运行项目：

```

$ ./tmp/usr/local/bin/hello

> $ ./tmp/usr/local/bin/hello 
你好！
```

As expected, the project displays the \"Hello World!\" message.

> 预料之中，项目显示出“你好，世界！”的信息。

# Makefile-Based Projects

Simple Makefile-based projects use and interact with the cross-toolchain environment variables established when you run the cross-toolchain environment setup script. The environment variables are subject to general `make` rules.

> 简单的基于 Makefile 的项目使用并与在运行跨工具链环境设置脚本时建立的环境变量进行交互。这些环境变量受到一般的“make”规则的约束。

This section presents a simple Makefile development flow and provides an example that lets you see how you can use cross-toolchain environment variables and Makefile variables during development.

> 这一节介绍了一个简单的 Makefile 开发流程，并提供了一个例子，让你可以看到如何在开发过程中使用跨工具链环境变量和 Makefile 变量。

![image](figures/sdk-makefile-flow.png){.align-center width="70.0%"}

The main point of this section is to explain the following three cases regarding variable behavior:

> 本节的主要要点是解释关于变量行为的以下三种情况：

- *Case 1 \-\-- No Variables Set in the Makefile Map to Equivalent Environment Variables Set in the SDK Setup Script:* Because matching variables are not specifically set in the `Makefile`, the variables retain their values based on the environment setup script.

> 情况 1：Makefile 中没有设置变量映射到 SDK 设置脚本中相应的环境变量：由于 Makefile 中没有特别设置匹配的变量，这些变量将保留它们在环境设置脚本中的值。

- *Case 2 \-\-- Variables Are Set in the Makefile that Map to Equivalent Environment Variables from the SDK Setup Script:* Specifically setting matching variables in the `Makefile` during the build results in the environment settings of the variables being overwritten. In this case, the variables you set in the `Makefile` are used.

> 在构建期间在 `Makefile` 中设置匹配变量会导致环境变量被覆盖。在这种情况下，您在 `Makefile` 中设置的变量将被使用。

- *Case 3 \-\-- Variables Are Set Using the Command Line that Map to Equivalent Environment Variables from the SDK Setup Script:* Executing the `Makefile` from the command line results in the environment variables being overwritten. In this case, the command-line content is used.

> 第三种情况：使用命令行设置变量，这些变量与 SDK 设置脚本中的环境变量相对应：从命令行执行 `Makefile` 会导致环境变量被覆盖。在这种情况下，使用的是命令行内容。

::: note
::: title
Note
:::

Regardless of how you set your variables, if you use the \"-e\" option with `make`, the variables from the SDK setup script take precedence:

> 无论你如何设置变量，如果使用 SDK 设置脚本中的“-e”选项使用“make”，变量将被优先考虑。

```
$ make -e target
```

:::

The remainder of this section presents a simple Makefile example that demonstrates these variable behaviors.

> 本节的剩余部分提供了一个简单的 Makefile 示例，用于演示这些变量的行为。

In a new shell environment variables are not established for the SDK until you run the setup script. For example, the following commands show a null value for the compiler variable (i.e. `CC`{.interpreted-text role="term"}):

> 在新的 Shell 环境中，除非您运行设置脚本，否则不会为 SDK 建立环境变量。例如，以下命令显示编译器变量（即 `CC`{.interpreted-text role="term"}）的空值：

```
$ echo ${CC}

$
```

Running the SDK setup script for a 64-bit build host and an i586-tuned target architecture for a `core-image-sato` image using the current &DISTRO; Yocto Project release and then echoing that variable shows the value established through the script:

> 在当前&DISTRO; Yocto Project 发行版上，为 `core-image-sato` 图像使用 64 位构建主机和 i586 调优目标架构运行 SDK 设置脚本，然后回显该变量显示通过脚本建立的值：

```
$ source /opt/poky/&DISTRO;/environment-setup-i586-poky-linux
$ echo ${CC}
i586-poky-linux-gcc -m32 -march=i586 --sysroot=/opt/poky/&DISTRO;/sysroots/i586-poky-linux
```

To illustrate variable use, work through this simple \"Hello World!\" example:

> 为了说明变量的使用，来看一个简单的“你好，世界！”的例子：

1. *Create a Working Directory and Populate It:* Create a clean directory for your project and then make that directory your working location:

> 1. *创建工作目录并填充它：* 为您的项目创建一个干净的目录，然后将该目录设置为您的工作位置：

```

$ mkdir $HOME/helloworld

> $ 在家目录下创建文件夹 helloworld：mkdir $HOME/helloworld

$ cd $HOME/helloworld

> $ cd $HOME/helloworld
移动到 $HOME/helloworld 目录
```

After setting up the directory, populate it with files needed for the flow. You need a `main.c` file from which you call your function, a `module.h` file to contain headers, and a `module.c` that defines your function.

> 在设置完目录之后，用流程所需的文件填充它。你需要一个 `main.c` 文件，从中调用你的函数，一个 `module.h` 文件来包含头文件，以及一个 `module.c` 文件来定义你的函数。

Create the three files as follows:

> 创建以下三个文件：

- `main.c`:

> - `main.c`：

```
```

#include "module.h"
void sample_func();
int main()
{
sample_func();
return 0;
}

```
```

- `module.h`:

> - `模块.h`:

```
```

#include <stdio.h>
void sample_func();

```
```

- `module.c`:

> - `module.c`：

```
```

#include "module.h"
void sample_func()
{
printf("Hello World!");
printf("\n");
}

```
```

2. *Source the Cross-Toolchain Environment Setup File:* As described earlier in the manual, installing the cross-toolchain creates a cross-toolchain environment setup script in the directory that the SDK was installed. Before you can use the tools to develop your project, you must source this setup script. The script begins with the string \"environment-setup\" and contains the machine architecture, which is followed by the string \"poky-linux\". For this example, the command sources a script from the default SDK installation directory that uses the 32-bit Intel x86 Architecture and the &DISTRO_NAME; Yocto Project release:

> 按照手册中的说明，安装交叉工具链会在 SDK 安装的目录下创建一个交叉工具链环境设置脚本。在使用工具开发项目之前，必须源自这个设置脚本。该脚本以“environment-setup”开头，其后跟机器架构，之后是“poky-linux”字符串。在这个例子中，命令源自使用 32 位 Intel x86 架构和&DISTRO_NAME; Yocto Project 发行版的默认 SDK 安装目录的脚本。

```

$ source /opt/poky/&DISTRO;/environment-setup-i586-poky-linux

> $ 源 /opt/poky/&DISTRO;/environment-setup-i586-poky-linux
```

Another example is sourcing the environment setup directly in a Yocto build:

> 另一个例子是直接在 Yocto 构建中源环境设置：

```

$ source tmp/deploy/images/qemux86-64/environment-setup-core2-64-poky-linux

> $ 来源 tmp/部署/图像/qemux86-64/environment-setup-core2-64-poky-linux
```

3. *Create the Makefile:* For this example, the Makefile contains two lines that can be used to set the `CC`{.interpreted-text role="term"} variable. One line is identical to the value that is set when you run the SDK environment setup script, and the other line sets `CC`{.interpreted-text role="term"} to \"gcc\", the default GNU compiler on the build host:

> 创建 Makefile：对于这个示例，Makefile 包含两行，可用于设置 `CC` 变量。一行与运行 SDK 环境设置脚本时设置的值相同，另一行将 `CC` 设置为构建主机上的默认 GNU 编译器“gcc”：

```

# CC=i586-poky-linux-gcc -m32 -march=i586 --sysroot=/opt/poky/2.5/sysroots/i586-poky-linux

> CC=i586-poky-linux-gcc -m32 -march=i586 --sysroot=/opt/poky/2.5/sysroots/i586-poky-linux

# CC="gcc"

> CC="gcc" 
CC="GCC"

all: main.o module.o

> 所有：main.o模块。o
  ${CC} main.o module.o -o target_bin

main.o: main.c module.h

> main.o：main.c和module.h
  ${CC} -I . -c main.c

module.o: module.c module.h

> module.o：module.c 和 module.h
  ${CC} -I . -c module.c

clean:

> 清洁：
  rm -rf *.o
  rm target_bin
```

4. *Make the Project:* Use the `make` command to create the binary output file. Because variables are commented out in the Makefile, the value used for `CC`{.interpreted-text role="term"} is the value set when the SDK environment setup file was run:

> 4. *创建项目：*使用 `make` 命令创建二进制输出文件。由于 Makefile 中的变量被注释掉了，因此 `CC`{.interpreted-text role="term"}的值是在运行 SDK 环境设置文件时设置的值。

```

$ make

> $ 生成

i586-poky-linux-gcc -m32 -march=i586 --sysroot=/opt/poky/2.5/sysroots/i586-poky-linux -I . -c main.c

> i586-poky-linux-gcc -m32 -march=i586 --sysroot=/opt/poky/2.5/sysroots/i586-poky-linux -I . -c main.c 
编译器i586-poky-linux-gcc以32位模式运行，指令集使用i586，系统根目录为/opt/poky/2.5/sysroots/i586-poky-linux，以当前目录为包含目录，编译main.c文件。

i586-poky-linux-gcc -m32 -march=i586 --sysroot=/opt/poky/2.5/sysroots/i586-poky-linux -I . -c module.c

> i586-poky-linux-gcc -m32 -march=i586 --sysroot=/opt/poky/2.5/sysroots/i586-poky-linux -I . -c 模块.c

i586-poky-linux-gcc -m32 -march=i586 --sysroot=/opt/poky/2.5/sysroots/i586-poky-linux main.o module.o -o target_bin

> i586-poky-linux-gcc -m32 -march=i586 --sysroot=/opt/poky/2.5/sysroots/i586-poky-linux 主要模块。o -o 目标_bin
```

From the results of the previous command, you can see that the compiler used was the compiler established through the `CC`{.interpreted-text role="term"} variable defined in the setup script.

> 从之前的命令的结果可以看出，使用的编译器是在设置脚本中定义的 `CC` 变量中建立的编译器。

You can override the `CC`{.interpreted-text role="term"} environment variable with the same variable as set from the Makefile by uncommenting the line in the Makefile and running `make` again:

> 你可以通过取消 Makefile 中的注释并再次运行'make'来使用 Makefile 中设置的相同变量覆盖'CC'环境变量：

```

$ make clean

> $ 清理

rm -rf *.o

> rm -rf *.o 
移除所有以.o结尾的文件

rm target_bin

> 删除target_bin

#

> 请帮助我翻译，

# Edit the Makefile by uncommenting the line that sets CC to "gcc"

> 请编辑Makefile，取消对CC设置"gcc"的注释。

#

> 请帮助我翻译：

$ make

> $ 生成

gcc -I . -c main.c

> gcc -I . -c main.c（简体中文）

gcc -I . -c module.c

> gcc -I . -c 模块.c

gcc main.o module.o -o target_bin

> gcc main.o module.o -o 目标可执行文件
```

As shown in the previous example, the cross-toolchain compiler is not used. Rather, the default compiler is used.

> 如前面的例子所示，不使用跨工具链编译器，而是使用默认编译器。

This next case shows how to override a variable by providing the variable as part of the command line. Go into the Makefile and re-insert the comment character so that running `make` uses the established SDK compiler. However, when you run `make`, use a command-line argument to set `CC`{.interpreted-text role="term"} to \"gcc\":

> 这个下一个案例展示了如何通过提供命令行变量来覆盖变量。进入 Makefile，重新插入注释字符，以便运行 `make` 使用建立的 SDK 编译器。但是，当你运行 `make` 时，使用命令行参数将 `CC` 设置为“gcc”：

```

$ make clean

> $ 清理

rm -rf *.o

> rm -rf *.o 
删除所有.o文件

rm target_bin

> 删除target_bin

#

> 请帮助我翻译：

# Edit the Makefile to comment out the line setting CC to "gcc"

> 编辑Makefile，注释掉设置CC为"gcc"的行

#

> 请帮助我翻译：

$ make

> $ 生成

i586-poky-linux-gcc  -m32 -march=i586 --sysroot=/opt/poky/2.5/sysroots/i586-poky-linux -I . -c main.c

> i586-poky-linux-gcc -m32 -march=i586 --sysroot=/opt/poky/2.5/sysroots/i586-poky-linux -I . -c main.c（简体中文）

i586-poky-linux-gcc  -m32 -march=i586 --sysroot=/opt/poky/2.5/sysroots/i586-poky-linux -I . -c module.c

> i586-poky-linux-gcc -m32 -march=i586 --sysroot=/opt/poky/2.5/sysroots/i586-poky-linux -I . -c 模块.c

i586-poky-linux-gcc  -m32 -march=i586 --sysroot=/opt/poky/2.5/sysroots/i586-poky-linux main.o module.o -o target_bin

> i586-poky-linux-gcc -m32 -march=i586--sysroot=/opt/poky/2.5/sysroots/i586-poky-linux主要。o模块。o-o目标_bin

$ make clean

> $ 清理

rm -rf *.o

> rm -rf *.o（删除所有以.o结尾的文件）

rm target_bin

> 删除target_bin

$ make CC="gcc"

> $ 使用GCC编译

gcc -I . -c main.c

> gcc -I . -c main.c（简体中文）

gcc -I . -c module.c

> gcc -I . -c 模块.c

gcc main.o module.o -o target_bin

> gcc main.o module.o -o 目标二进制
```

In the previous case, the command-line argument overrides the SDK environment variable.

> 在之前的情况下，命令行参数会覆盖 SDK 环境变量。

In this last case, edit Makefile again to use the \"gcc\" compiler but then use the \"-e\" option on the `make` command line:

> 在这种情况下，再次编辑 Makefile 以使用"gcc"编译器，但是在"make"命令行上使用"-e"选项：

```

$ make clean

> $ 清理

rm -rf *.o

> rm -rf *.o 
删除所有.o文件

rm target_bin

> 删除target_bin

#

> 请帮助我翻译：

# Edit the Makefile to use "gcc"

> 请编辑 Makefile 以使用 "gcc"

#

> 请帮助我翻译，

$ make

> $ 生成

gcc -I . -c main.c

> gcc -I . -c 主程序.c

gcc -I . -c module.c

> gcc -I . -c 模块.c

gcc main.o module.o -o target_bin

> gcc main.o module.o -o 目标可执行文件

$ make clean

> $ 清理

rm -rf *.o

> rm -rf *.o 
移除所有以.o结尾的文件

rm target_bin

> 删除target_bin

$ make -e

> $ 做 -e

i586-poky-linux-gcc  -m32 -march=i586 --sysroot=/opt/poky/2.5/sysroots/i586-poky-linux -I . -c main.c

> i586-poky-linux-gcc -m32 -march=i586 --sysroot=/opt/poky/2.5/sysroots/i586-poky-linux -I . -c main.c（简体中文）

i586-poky-linux-gcc  -m32 -march=i586 --sysroot=/opt/poky/2.5/sysroots/i586-poky-linux -I . -c module.c

> i586-poky-linux-gcc -m32 -march=i586 --sysroot=/opt/poky/2.5/sysroots/i586-poky-linux -I . -c module.c（简体中文）

i586-poky-linux-gcc  -m32 -march=i586 --sysroot=/opt/poky/2.5/sysroots/i586-poky-linux main.o module.o -o target_bin

> i586-poky-linux-gcc -m32 -march=i586 --sysroot=/opt/poky/2.5/sysroots/i586-poky-linux 主要的。o 模块。o -o 目标_bin
```

In the previous case, the \"-e\" option forces `make` to use the SDK environment variables regardless of the values in the Makefile.

> 在之前的情况下，“-e”选项强制 make 使用 SDK 环境变量，而不管 Makefile 中的值。

5. *Execute Your Project:* To execute the project (i.e. `target_bin`), use the following command:

> 执行项目：要执行项目（即 `target_bin`），请使用以下命令：

```

$ ./target_bin

> $ ./目标二进制文件

Hello World!

> 你好世界！
```

::: note
::: title

Note

> 注意
> :::

If you used the cross-toolchain compiler to build target_bin and your build host differs in architecture from that of the target machine, you need to run your project on the target device.

> 如果您使用跨工具链编译器构建 target_bin，并且构建主机的架构与目标机器的架构不同，则需要在目标设备上运行您的项目。
> :::

As expected, the project displays the \"Hello World!\" message.

> 预期的，项目显示了“你好世界！”的消息。
