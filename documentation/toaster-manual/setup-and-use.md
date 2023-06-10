---
tip: translate by openai@2023-06-07 20:59:34
...
---
title: Setting Up and Using Toaster
-----------------------------------

# Starting Toaster for Local Development

Once you have set up the Yocto Project and installed the Toaster system dependencies as described in the \"`toaster-manual/start:Preparing to Use

> 一旦您已经设置了 Yocto 项目并按照“toaster-manual / start：准备使用”中的说明安装了 Toaster 系统依赖项，

Toaster`{.interpreted-text role="ref"}\" chapter, you are ready to start Toaster.

> 准备好了，你可以开始使用 Toaster 了。

Navigate to the root of your `Source Directory`{.interpreted-text role="term"} (e.g. `poky`):

> 请前往您的源目录（例如 `poky`）的根目录：

```shell
$ cd poky
```

Once in that directory, source the build environment script:

> 进入该目录后，运行构建环境脚本：

```shell
$ source oe-init-build-env
```

Next, from the `Build Directory`{.interpreted-text role="term"} (e.g. `poky/build`), start Toaster using this command:

> 下一步，从“构建目录”（例如“poky/build”）开始，使用以下命令启动 Toaster：

```shell
$ source toaster start
```

You can now run your builds from the command line, or with Toaster as explained in section \"`toaster-manual/setup-and-use:using the toaster web interface`{.interpreted-text role="ref"}\".

> 现在你可以从命令行或使用 Toaster 运行构建，如“使用 Toaster Web 界面”部分所述。

To access the Toaster web interface, open your favorite browser and enter the following:

> 打开你喜欢的浏览器，输入以下内容以访问烤面包机的网页接口：

```shell
http://127.0.0.1:8000
```

# Setting a Different Port

By default, Toaster starts on port 8000. You can use the `WEBPORT` parameter to set a different port. For example, the following command sets the port to \"8400\":

> 默认情况下，Toaster 使用端口 8000 启动。您可以使用 `WEBPORT` 参数来设置不同的端口。例如，以下命令将端口设置为“8400”：

```shell
$ source toaster start webport=8400
```

# Setting Up Toaster Without a Web Server

You can start a Toaster environment without starting its web server. This is useful for the following:

> 你可以在不启动其 Web 服务器的情况下启动 Toaster 环境。这对以下情况很有用：

- Capturing a command-line build\'s statistics into the Toaster database for examination later.
- Capturing a command-line build\'s statistics when the Toaster server is already running.
- Having one instance of the Toaster web server track and capture multiple command-line builds, where each build is started in its own \"noweb\" Toaster environment.

> 在一个 Toaster 网络服务器实例中跟踪和捕获多个命令行构建，每个构建都在其自己的“noweb”Toaster 环境中启动。

The following commands show how to start a Toaster environment without starting its web server, perform BitBake operations, and then shut down the Toaster environment. Once the build is complete, you can close the Toaster environment. Before closing the environment, however, you should allow a few minutes to ensure the complete transfer of its BitBake build statistics to the Toaster database. If you have a separate Toaster web server instance running, you can watch this command-line build\'s progress and examine the results as soon as they are posted:

> 以下命令显示如何启动 Toaster 环境而不启动其 Web 服务器，执行 BitBake 操作，然后关闭 Toaster 环境。构建完成后，您可以关闭环境。但是，在关闭环境之前，您应该花几分钟的时间来确保 BitBake 构建统计信息完全传输到 Toaster 数据库中。如果您运行了单独的 Toaster Web 服务器实例，您可以观察此命令行构建的进度，并在发布结果后立即查看结果。

```shell
$ source toaster start noweb
$ bitbake target
$ source toaster stop
```

# Setting Up Toaster Without a Build Server

You can start a Toaster environment with the \"New Projects\" feature disabled. Doing so is useful for the following:

> 你可以使用“新项目”功能禁用启动 Toaster 环境。这样做有以下用处：

- Sharing your build results over the web server while blocking others from starting builds on your host.

> 共享您的构建结果到网络服务器，同时阻止其他人在您的主机上启动构建。

- Allowing only local command-line builds to be captured into the Toaster database.

Use the following command to set up Toaster without a build server:

> 使用以下命令在没有构建服务器的情况下设置 Toaster：

```shell
$ source toaster start nobuild webport=port
```

# Setting up External Access

By default, Toaster binds to the loop back address (i.e. `localhost`), which does not allow access from external hosts. To allow external access, use the `WEBPORT` parameter to open an address that connects to the network, specifically the IP address that your NIC uses to connect to the network. You can also bind to all IP addresses the computer supports by using the shortcut \"0.0.0.0:port\".

> 默认情况下，Toaster 绑定到回环地址（即 `localhost`），这不允许从外部主机访问。要允许外部访问，请使用 `WEBPORT` 参数打开连接到网络的地址，特别是 NIC 用于连接网络的 IP 地址。您还可以通过使用快捷方式“0.0.0.0：port”来绑定到计算机支持的所有 IP 地址。

The following example binds to all IP addresses on the host:

> 以下示例绑定到主机上的所有 IP 地址：

```shell
$ source toaster start webport=0.0.0.0:8400
```

This example binds to a specific IP address on the host\'s NIC:

> 这个例子绑定到主机网卡上的特定 IP 地址：

```shell
$ source toaster start webport=192.168.1.1:8400
```

# The Directory for Cloning Layers

Toaster creates a `_toaster_clones` directory inside your Source Directory (i.e. `poky`) to clone any layers needed for your builds.

> Toaster 将会在源目录（即 `poky`）内创建一个 `_toaster_clones` 目录，用于克隆构建所需的任何图层。

Alternatively, if you would like all of your Toaster related files and directories to be in a particular location other than the default, you can set the `TOASTER_DIR` environment variable, which takes precedence over your current working directory. Setting this environment variable causes Toaster to create and use `$TOASTER_DIR./_toaster_clones`.

> 如果您希望所有与 Toaster 相关的文件和目录位于默认位置以外的特定位置，您可以设置 `TOASTER_DIR` 环境变量，它优先于您当前的工作目录。设置此环境变量会导致 Toaster 创建和使用 `$TOASTER_DIR./_toaster_clones`。

# The Build Directory

Toaster creates a `Build Directory`{.interpreted-text role="term"} within your Source Directory (e.g. `poky`) to execute the builds.

> 烤箱会在您的源目录（例如 `poky`）中创建一个 `构建目录` 来执行构建。

Alternatively, if you would like all of your Toaster related files and directories to be in a particular location, you can set the `TOASTER_DIR` environment variable, which takes precedence over your current working directory. Setting this environment variable causes Toaster to use `$TOASTER_DIR/build` as the `Build Directory`{.interpreted-text role="term"}.

> 如果您希望所有与 Toaster 相关的文件和目录位于特定位置，您可以设置 `TOASTER_DIR` 环境变量，它优先于您当前的工作目录。设置此环境变量会导致 Toaster 使用 `$TOASTER_DIR/build` 作为 `Build Directory`。

# Creating a Django Superuser

Toaster is built on the [Django framework](https://www.djangoproject.com/). Django provides an administration interface you can use to edit Toaster configuration parameters.

> 吐司器基于 [Django 框架](https://www.djangoproject.com/)构建。 Django 提供了一个管理界面，您可以使用它来编辑 Toaster 配置参数。

To access the Django administration interface, you must create a superuser by following these steps:

> 要访问 Django 管理界面，您必须按照以下步骤创建超级用户：

1. If you used `pip3`, which is recommended, to set up the Toaster system dependencies, you need be sure the local user path is in your `PATH` list. To append the pip3 local user path, use the following command:

> 如果您推荐使用 `pip3` 来设置 Toaster 系统依赖项，则需要确保本地用户路径位于您的 `PATH` 列表中。要附加 pip3 本地用户路径，请使用以下命令：

```
``` shell
$ export PATH=$PATH:$HOME/.local/bin
```

```

2. From the directory containing the Toaster database, which by default is the `Build Directory`{.interpreted-text role="term"}, invoke the `createsuperuser` command from `manage.py`:

> 从包含 Toaster 数据库的目录（默认为 Build Directory）中，从 manage.py 中调用 createsuperuser 命令：

```

```shell
$ cd poky/build
$ ../bitbake/lib/toaster/manage.py createsuperuser
```

```

3. Django prompts you for the username, which you need to provide.

> Django 会提示你输入用户名，你需要提供。

4. Django prompts you for an email address, which is optional.

> Django 会提示您输入一个可选的电子邮件地址。

5. Django prompts you for a password, which you must provide.

> Django 会提示你输入密码，你必须提供密码。

6. Django prompts you to re-enter your password for verification.

> 请您重新输入密码以进行验证。

After completing these steps, the following confirmation message appears:

> 完成以上步骤后，会出现以下确认信息：

```shell
Superuser created successfully.
```

Creating a superuser allows you to access the Django administration interface through a browser. The URL for this interface is the same as the URL used for the Toaster instance with \"/admin\" on the end. For example, if you are running Toaster locally, use the following URL:

> 创建超级用户可以让您通过浏览器访问 Django 管理界面。此界面的 URL 与 Toaster 实例使用的 URL 相同，末尾处有“/admin”。例如，如果您正在本地运行 Toaster，请使用以下 URL：

```shell
http://127.0.0.1:8000/admin
```

You can use the Django administration interface to set Toaster configuration parameters such as the `Build Directory`{.interpreted-text role="term"}, layer sources, default variable values, and BitBake versions.

> 你可以使用 Django 管理界面来设置烤箱配置参数，如 `构建目录`、层源、默认变量值和 BitBake 版本。

# Setting Up a Production Instance of Toaster

You can use a production instance of Toaster to share the Toaster instance with remote users, multiple users, or both. The production instance is also the setup that can handle heavier loads on the web service. Use the instructions in the following sections to set up Toaster to run builds through the Toaster web interface.

> 您可以使用 Toaster 的生产实例来与远程用户、多个用户或两者兼有共享 Toaster 实例。生产实例也是可以处理网络服务负载更重的设置。使用以下部分中的说明，可以设置 Toaster 以通过 Toaster Web 界面运行构建。

## Requirements

Be sure you meet the following requirements:

> 请确保您满足以下要求：

::: note
::: title
Note
:::

You must comply with all Apache, `mod-wsgi`, and Mysql requirements.

> 你必须遵守所有 Apache、mod-wsgi 和 MySQL 的要求。
> :::

- Have all the build requirements as described in the \"`toaster-manual/start:Preparing to Use Toaster`{.interpreted-text role="ref"}\" chapter.
- Have an Apache webserver.
- Have `mod-wsgi` for the Apache webserver.
- Use the Mysql database server.
- If you are using Ubuntu, run the following:

  ```shell
  $ sudo apt install apache2 libapache2-mod-wsgi-py3 mysql-server python3-pip libmysqlclient-dev
  ```
- If you are using Fedora or a RedHat distribution, run the following:

  ```shell
  $ sudo dnf install httpd python3-mod_wsgi python3-pip mariadb-server mariadb-devel python3-devel
  ```
- If you are using openSUSE, run the following:

  ```shell
  $ sudo zypper install apache2 apache2-mod_wsgi-python3 python3-pip mariadb mariadb-client python3-devel
  ```

## Installation

Perform the following steps to install Toaster:

> 执行以下步骤安装 Toaster：

1. Create toaster user and set its home directory to `/var/www/toaster`:

> 创建吐司用户并将其主目录设置为 '/var/www/toaster'：

```
``` shell
$ sudo /usr/sbin/useradd toaster -md /var/www/toaster -s /bin/false
$ sudo su - toaster -s /bin/bash
```

```

2. Checkout a copy of `poky` into the web server directory. You will be using `/var/www/toaster`:

> 2. 将 `poky` 复制到 Web 服务器目录中。您将使用 `/var/www/toaster`：

```

```shell
$ git clone git://git.yoctoproject.org/poky
$ git checkout &DISTRO_NAME_NO_CAP;
```

```

3. Install Toaster dependencies using the `--user` flag which keeps the Python packages isolated from your system-provided packages:

> 使用 `--user` 标志安装烤面包机的依赖，这将使 Python 包与您的系统提供的包隔离开来。

```

```shell
$ cd /var/www/toaster/
$ pip3 install --user -r ./poky/bitbake/toaster-requirements.txt
$ pip3 install --user mysqlclient
```

::: note
::: title
Note
:::

Isolating these packages is not required but is recommended. Alternatively, you can use your operating system\'s package manager to install the packages.
:::

```

4. Configure Toaster by editing `/var/www/toaster/poky/bitbake/lib/toaster/toastermain/settings.py` as follows:

> 配置 Toaster，通过编辑 `/var/www/toaster/poky/bitbake/lib/toaster/toastermain/settings.py` 如下：

```

- Edit the [DATABASES](https://docs.djangoproject.com/en/2.2/ref/settings/#databases) settings:

  ```python
  DATABASES = {
     'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'toaster_data',
        'USER': 'toaster',
        'PASSWORD': 'yourpasswordhere',
        'HOST': 'localhost',
        'PORT': '3306',
     }
  }
  ```
- Edit the [SECRET_KEY](https://docs.djangoproject.com/en/2.2/ref/settings/#std:setting-SECRET_KEY):

  ```python
  SECRET_KEY = 'your_secret_key'
  ```
- Edit the [STATIC_ROOT](https://docs.djangoproject.com/en/2.2/ref/settings/#std:setting-STATIC_ROOT):

> ```python
> STATIC_ROOT = '/var/www/toaster/static_files/'
> ```

```

5. Add the database and user to the `mysql` server defined earlier:

> 5. 将数据库和用户添加到先前定义的 `mysql` 服务器中：

```

```shell
$ mysql -u root -p
mysql> CREATE DATABASE toaster_data;
mysql> CREATE USER 'toaster'@'localhost' identified by 'yourpasswordhere';
mysql> GRANT all on toaster_data.\* to 'toaster'@'localhost';
mysql> quit
```

```

6. Get Toaster to create the database schema, default data, and gather the statically-served files:

> 6. 使用 Toaster 创建数据库架构，默认数据和收集静态服务文件：

```

```shell
$ cd /var/www/toaster/poky/
$ ./bitbake/lib/toaster/manage.py migrate
$ TOASTER_DIR=`pwd\` TEMPLATECONF='poky' \
   ./bitbake/lib/toaster/manage.py checksettings
$ ./bitbake/lib/toaster/manage.py collectstatic
```

In the previous example, from the `poky` directory, the `migrate` command ensures the database schema changes have propagated correctly (i.e. migrations). The next line sets the Toaster root directory `TOASTER_DIR` and the location of the Toaster configuration file `TOASTER_CONF`, which is relative to `TOASTER_DIR`. The `TEMPLATECONF`{.interpreted-text role="term"} value reflects the contents of `poky/.templateconf`, and by default, should include the string \"poky\". For more information on the Toaster configuration file, see the \"`toaster-manual/reference:Configuring Toaster`{.interpreted-text role="ref"}\" section.

This line also runs the `checksettings` command, which configures the location of the Toaster `Build Directory`{.interpreted-text role="term"}. The Toaster root directory `TOASTER_DIR` determines where the Toaster build directory is created on the file system. In the example above, `TOASTER_DIR` is set as follows:

```shell
/var/www/toaster/poky
```

This setting causes the Toaster `Build Directory`{.interpreted-text role="term"} to be:

```shell
/var/www/toaster/poky/build
```

Finally, the `collectstatic` command is a Django framework command that collects all the statically served files into a designated directory to be served up by the Apache web server as defined by `STATIC_ROOT`.

```

7. Test and/or use the Mysql integration with Toaster\'s Django web server. At this point, you can start up the normal Toaster Django web server with the Toaster database in Mysql. You can use this web server to confirm that the database migration and data population from the Layer Index is complete.

> 测试并/或使用 Toaster 的 Django Web 服务器与 Mysql 的集成。此时，您可以使用 Toaster 数据库在 Mysql 中启动正常的 Toaster Django Web 服务器。您可以使用此 Web 服务器来确认从图层索引迁移和数据填充是否完成。

```

To start the default Toaster Django web server with the Toaster database now in Mysql, use the standard start commands:

```shell
$ source oe-init-build-env
$ source toaster start
```

Additionally, if Django is sufficient for your requirements, you can use it for your release system and migrate later to Apache as your requirements change.

```

8. Add an Apache configuration file for Toaster to your Apache web server\'s configuration directory. If you are using Ubuntu or Debian, put the file here:

> 在 Apache 网络服务器的配置目录中为 Toaster 添加一个 Apache 配置文件。如果你正在使用 Ubuntu 或 Debian，请将文件放在这里：

```

```shell
/etc/apache2/conf-available/toaster.conf
```

If you are using Fedora or RedHat, put it here:

```shell
/etc/httpd/conf.d/toaster.conf
```

If you are using openSUSE, put it here:

```shell
/etc/apache2/conf.d/toaster.conf
```

Following is a sample Apache configuration for Toaster you can follow:

```apache
Alias /static /var/www/toaster/static_files
<Directory /var/www/toaster/static_files>
   <IfModule mod_access_compat.c>
      Order allow,deny
      Allow from all
   </IfModule>
   <IfModule !mod_access_compat.c>
      Require all granted
   </IfModule>
</Directory>

<Directory /var/www/toaster/poky/bitbake/lib/toaster/toastermain>
   <Files "wsgi.py">
      Require all granted
   </Files>
</Directory>

WSGIDaemonProcess toaster_wsgi python-path=/var/www/toaster/poky/bitbake/lib/toaster:/var/www/toaster/.local/lib/python3.4/site-packages
WSGIScriptAlias / "/var/www/toaster/poky/bitbake/lib/toaster/toastermain/wsgi.py"
<Location />
   WSGIProcessGroup toaster_wsgi
</Location>
```

If you are using Ubuntu or Debian, you will need to enable the config and module for Apache:

```shell
$ sudo a2enmod wsgi
$ sudo a2enconf toaster
$ chmod +x bitbake/lib/toaster/toastermain/wsgi.py
```

Finally, restart Apache to make sure all new configuration is loaded. For Ubuntu, Debian, and openSUSE use:

```shell
$ sudo service apache2 restart
```

For Fedora and RedHat use:

```shell
$ sudo service httpd restart
```

```

9. Prepare the systemd service to run Toaster builds. Here is a sample configuration file for the service:

> 9. 为 Toaster 构建准备 systemd 服务。这里有一个服务的示例配置文件：

```

```ini
[Unit]
Description=Toaster runbuilds

[Service]
Type=forking User=toaster
ExecStart=/usr/bin/screen -d -m -S runbuilds /var/www/toaster/poky/bitbake/lib/toaster/runbuilds-service.sh start
ExecStop=/usr/bin/screen -S runbuilds -X quit
WorkingDirectory=/var/www/toaster/poky

[Install]
WantedBy=multi-user.target
```

Prepare the `runbuilds-service.sh` script that you need to place in the `/var/www/toaster/poky/bitbake/lib/toaster/` directory by setting up executable permissions:

```shell
#!/bin/bash

#export http_proxy=http://proxy.host.com:8080
#export https_proxy=http://proxy.host.com:8080
#export GIT_PROXY_COMMAND=$HOME/bin/gitproxy
cd poky/
source ./oe-init-build-env build
source ../bitbake/bin/toaster $1 noweb
[ "$1" == 'start' ] && /bin/bash
```

```

10. Run the service:

> 运行服务：

```

```shell
$ sudo service runbuilds start
```

Since the service is running in a detached screen session, you can attach to it using this command:

```shell
$ sudo su - toaster
$ screen -rS runbuilds
```

You can detach from the service again using \"Ctrl-a\" followed by \"d\" key combination.

```

You can now open up a browser and start using Toaster.

> 现在你可以打开浏览器并开始使用 Toaster 了。

# Using the Toaster Web Interface

The Toaster web interface allows you to do the following:

> 界面可以让您完成以下操作：

- Browse published layers in the :oe_layerindex:[OpenEmbedded Layer Index \<\>]{.title-ref} that are available for your selected version of the build system.

> 浏览在:oe_layerindex:[OpenEmbedded Layer Index \<\>]{.title-ref}中为您所选择的构建系统版本可用的已发布的图层。

- Import your own layers for building.
- Add and remove layers from your configuration.
- Set configuration variables.
- Select a target or multiple targets to build.
- Start your builds.
- See what was built (recipes and packages) and what packages were installed into your final image.

> 查看建立了什么（食谱和软件包），以及哪些软件包被安装到最终镜像中。

- Browse the directory structure of your image.
- See the value of all variables in your build configuration, and which files set each value.
- Examine error, warning and trace messages to aid in debugging.
- See information about the BitBake tasks executed and reused during your build, including those that used shared state.

> 查看有关在构建过程中执行和重复使用的 BitBake 任务的信息，包括使用共享状态的任务。

- See dependency relationships between recipes, packages and tasks.
- See performance information such as build time, task time, CPU usage, and disk I/O.

## Toaster Web Interface Videos

Following are several videos that show how to use the Toaster GUI:

> 以下是几个视频，演示如何使用 Toaster GUI：

- *Build Configuration:* This [video](https://www.youtube.com/watch?v=qYgDZ8YzV6w) overviews and demonstrates build configuration for Toaster.

> *构建配置：*这个视频（[https://www.youtube.com/watch?v=qYgDZ8YzV6w](https://www.youtube.com/watch?v=qYgDZ8YzV6w)）概述并演示了烤面包机的构建配置。

- *Build Custom Layers:* This [video](https://www.youtube.com/watch?v=QJzaE_XjX5c) shows you how to build custom layers that are used with Toaster.

> *构建自定义层：* 这个视频（[https://www.youtube.com/watch?v=QJzaE_XjX5c](https://www.youtube.com/watch?v=QJzaE_XjX5c)）向您展示了如何构建与 Toaster 一起使用的自定义层。

- *Toaster Homepage and Table Controls:* This [video](https://www.youtube.com/watch?v=QEARDnrR1Xw) goes over the Toaster entry page, and provides an overview of the data manipulation capabilities of Toaster, which include search, sorting and filtering by different criteria.

> 这个视频主要介绍 Toaster 的主页和表格控制，它提供了搜索、排序和按不同标准进行过滤的数据操作功能。

- *Build Dashboard:* This [video](https://www.youtube.com/watch?v=KKqHYcnp2gE) shows you the build dashboard, a page providing an overview of the information available for a selected build.

> *构建仪表板：* 这个[视频](https://www.youtube.com/watch?v=KKqHYcnp2gE)向您展示了构建仪表板，这是一个为所选构建提供可用信息概览的页面。

- *Image Information:* This [video](https://www.youtube.com/watch?v=XqYGFsmA0Rw) walks through the information Toaster provides about images: packages installed and root file system.

> *图像信息：本视频（[https://www.youtube.com/watch?v=XqYGFsmA0Rw](https://www.youtube.com/watch?v=XqYGFsmA0Rw)）介绍了 Toaster 提供的关于图像的信息：安装的软件包和根文件系统。

- *Configuration:* This [video](https://www.youtube.com/watch?v=UW-j-T2TzIg) provides Toaster build configuration information.

> *配置：* 这个视频（[https://www.youtube.com/watch?v=UW-j-T2TzIg](https://www.youtube.com/watch?v=UW-j-T2TzIg)）提供烤箱构建配置信息。

- *Tasks:* This [video](https://www.youtube.com/watch?v=D4-9vGSxQtw) shows the information Toaster provides about the tasks run by the build system.

> *任务：此[视频](https://www.youtube.com/watch?v=D4-9vGSxQtw)展示了 Toaster 提供的有关构建系统运行的任务的信息。*

- *Recipes and Packages Built:* This [video](https://www.youtube.com/watch?v=x-6dx4huNnw) shows the information Toaster provides about recipes and packages built.

> 这个视频展示了 Toaster 提供的关于配方和打包的信息。

- *Performance Data:* This [video](https://www.youtube.com/watch?v=qWGMrJoqusQ) shows the build performance data provided by Toaster.

> 这个视频展示了 Toaster 提供的性能数据。

## Additional Information About the Local Yocto Project Release

This section only applies if you have set up Toaster for local development, as explained in the \"`toaster-manual/setup-and-use:starting toaster for local development`{.interpreted-text role="ref"}\" section.

> 如果您已经按照“toaster-manual/setup-and-use：开始本地开发 Toaster”部分中的说明设置了 Toaster，则此部分才适用。

When you create a project in Toaster, you will be asked to provide a name and to select a Yocto Project release. One of the release options you will find is called \"Local Yocto Project\".

> 当您在 Toaster 中创建一个项目时，您将被要求提供一个名称并选择 Yocto Project 发行版。您将找到的发行版之一称为“本地 Yocto Project”。

![image](figures/new-project.png){.align-center}

When you select the \"Local Yocto Project\" release, Toaster will run your builds using the local Yocto Project clone you have in your computer: the same clone you are using to run Toaster. Unless you manually update this clone, your builds will always use the same Git revision.

> 当你选择“本地 Yocto 项目”发布版本时，Toaster 将使用你在计算机中的本地 Yocto 项目克隆来运行你的构建：这个克隆就是你用来运行 Toaster 的克隆。除非你手动更新这个克隆，否则你的构建将始终使用相同的 Git 版本。

If you select any of the other release options, Toaster will fetch the tip of your selected release from the upstream :yocto\_[git:%60Yocto](git:%60Yocto) Project repository \<\>\` every time you run a build. Fetching this tip effectively means that if your selected release is updated upstream, the Git revision you are using for your builds will change. If you are doing development locally, you might not want this change to happen. In that case, the \"Local Yocto Project\" release might be the right choice.

> 如果你选择其他发布选项，每次运行构建时，Toaster 会从上游 Yocto [git:%60Yocto](git:%60Yocto) 项目存储库中获取你选择的发布的最新版本。获取这个版本有效意味着，如果你选择的发布版本在上游更新，你用于构建的 Git 版本也会改变。如果你本地进行开发，可能不希望这种改变发生。在这种情况下，“本地 Yocto 项目”发布可能是正确的选择。

However, the \"Local Yocto Project\" release will not provide you with any compatible layers, other than the three core layers that come with the Yocto Project:

> 然而，“本地 Yocto 项目”发布不会提供任何与 Yocto 项目一起提供的三个核心层兼容的层。

- :oe_layer:[openembedded-core \</openembedded-core\>]{.title-ref}
- :oe_layer:[meta-poky \</meta-poky\>]{.title-ref}
- :oe_layer:[meta-yocto-bsp \</meta-yocto-bsp\>]{.title-ref}

![image](figures/compatible-layers.png){.align-center}

If you want to build any other layers, you will need to manually import them into your Toaster project, using the \"Import layer\" page.

> 如果你想构建其他层，你需要使用“导入层”页面，手动将它们导入到你的 Toaster 项目中。

![image](figures/import-layer.png){.align-center}

## Building a Specific Recipe Given Multiple Versions

Occasionally, a layer might provide more than one version of the same recipe. For example, the `openembedded-core` layer provides two versions of the `bash` recipe (i.e. 3.2.48 and 4.3.30-r0) and two versions of the `which` recipe (i.e. 2.21 and 2.18). The following figure shows this exact scenario:

> 偶尔，一个层可能提供多个版本的相同的配方。例如，`openembedded-core` 层提供了两个版本的 `bash` 配方（即 3.2.48 和 4.3.30-r0）以及两个版本的 `which` 配方（即 2.21 和 2.18）。下图显示了这种情况：

![image](figures/bash-oecore.png){.align-center}

By default, the OpenEmbedded build system builds one of the two recipes. For the `bash` case, version 4.3.30-r0 is built by default. Unfortunately, Toaster as it exists, is not able to override the default recipe version. If you would like to build bash 3.2.48, you need to set the `PREFERRED_VERSION`{.interpreted-text role="term"} variable. You can do so from Toaster, using the \"Add variable\" form, which is available in the \"BitBake variables\" page of the project configuration section as shown in the following screen:

> 默认情况下，OpenEmbedded 构建系统构建其中的两个配方之一。对于 `bash` 的情况，默认情况下构建的是 4.3.30-r0 版本。不幸的是，现有的 Toaster 无法覆盖默认的配方版本。如果您想要构建 bash 3.2.48，您需要设置 `PREFERRED_VERSION`{.interpreted-text role="term"}变量。您可以从 Toaster 使用“添加变量”表单来完成，该表单位于项目配置部分的“BitBake 变量”页面中，如下图所示：

![image](figures/add-variable.png){.align-center}

To specify `bash` 3.2.48 as the version to build, enter \"PREFERRED_VERSION_bash\" in the \"Variable\" field, and \"3.2.48\" in the \"Value\" field. Next, click the \"Add variable\" button:

> 要指定要构建的 bash 3.2.48 版本，请在“变量”字段中输入“PREFERRED_VERSION_bash”，在“值”字段中输入“3.2.48”。然后点击“添加变量”按钮：

![image](figures/set-variable.png){.align-center}

After clicking the \"Add variable\" button, the settings for `PREFERRED_VERSION`{.interpreted-text role="term"} are added to the bottom of the BitBake variables list. With these settings, the OpenEmbedded build system builds the desired version of the recipe rather than the default version:

> 点击“添加变量”按钮后，`PREFERRED_VERSION` 的设置将添加到 BitBake 变量列表的底部。使用这些设置，OpenEmbedded 构建系统将构建所需的版本的配方，而不是默认版本。

![image](figures/variable-added.png){.align-center}
```
