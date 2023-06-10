---
tip: translate by openai@2023-06-10 10:23:38
...
---
title: Creating a Custom Template Configuration Directory
---------------------------------------------------------

If you are producing your own customized version of the build system for use by other users, you might want to provide a custom build configuration that includes all the necessary settings and layers (i.e. `local.conf` and `bblayers.conf` that are created in a new `Build Directory`) and a custom message that is shown when setting up the build. This can be done by creating one or more template configuration directories in your custom distribution layer.

> 如果您正在为其他用户制作自定义版本的构建系统，您可能希望提供一个自定义构建配置，其中包括所有必要的设置和层(即在新的“构建目录”中创建的 `local.conf` 和 `bblayers.conf`)以及在设置构建时显示的自定义消息。这可以通过在您的自定义分发层中创建一个或多个模板配置目录来实现。

This can be done by using `bitbake-layers save-build-conf`:

```
$ bitbake-layers save-build-conf ../../meta-alex/ test-1
NOTE: Starting bitbake server...
NOTE: Configuration template placed into /srv/work/alex/meta-alex/conf/templates/test-1
Please review the files in there, and particularly provide a configuration description in /srv/work/alex/meta-alex/conf/templates/test-1/conf-notes.txt
You can try out the configuration with
TEMPLATECONF=/srv/work/alex/meta-alex/conf/templates/test-1 . /srv/work/alex/poky/oe-init-build-env build-try-test-1
```

The above command takes the config files from the currently active `Build Directory` under `conf`, replaces site-specific paths in `bblayers.conf` with `##OECORE##`-relative paths, and copies the config files into a specified layer under a specified template name.

> 上述命令从当前活动的“构建目录”下的“conf”中获取配置文件，将“bblayers.conf”中的站点特定路径替换为“##OECORE##”相对路径，并将配置文件复制到指定的层中，指定的模板名称。

To use those saved templates as a starting point for a build, users should point to one of them with `TEMPLATECONF` environment variable:

> 使用这些保存的模板作为构建的起点，用户应该使用 `TEMPLATECONF` 环境变量指向其中一个：

```
TEMPLATECONF=/srv/work/alex/meta-alex/conf/templates/test-1 . /srv/work/alex/poky/oe-init-build-env build-try-test-1
```

The OpenEmbedded build system uses the environment variable `TEMPLATECONF` `conf` directory.

> 系统 OpenEmbedded 使用环境变量 TEMPLATECONF 来定位收集配置信息的目录，最终这些信息会被放到构建目录的 conf 目录中。

If `TEMPLATECONF` is not set, the default value is obtained from `.templateconf` file that is read from the same directory as `oe-init-build-env` script. For the Poky reference distribution this would be:

> 如果没有设置 TEMPLATECONF，则会从与 oe-init-build-env 脚本位于同一目录中的.templateconf 文件中获取默认值。对于 Poky 参考发行版，这将是：

```
TEMPLATECONF=$
```

If you look at a configuration template directory, you will see the `bblayers.conf.sample`, `local.conf.sample`, and `conf-notes.txt` files. The build system uses these files to form the respective `bblayers.conf` file, `local.conf` file, and show users a note about the build they\'re setting up when running the `oe-init-build-env` setup script. These can be edited further if needed to improve or change the build configurations available to the users.

> 如果您查看配置模板目录，您将会看到 `bblayers.conf.sample`、`local.conf.sample` 和 `conf-notes.txt` 文件。构建系统使用这些文件来形成相应的 `bblayers.conf` 文件、`local.conf` 文件，并在运行 `oe-init-build-env` 设置脚本时向用户显示有关他们正在设置的构建的注释。如果需要，这些文件可以进一步编辑以改善或更改用户可用的构建配置。
