---
tip: translate by baidu@2023-06-07 17:10:22
...
---
title: Creating a Custom Template Configuration Directory
---------------------------------------------------------

If you are producing your own customized version of the build system for use by other users, you might want to provide a custom build configuration that includes all the necessary settings and layers (i.e. `local.conf` and `bblayers.conf` that are created in a new `Build Directory`{.interpreted-text role="term"}) and a custom message that is shown when setting up the build. This can be done by creating one or more template configuration directories in your custom distribution layer.

> 如果您正在生成自己的自定义版本的生成系统以供其他用户使用，则可能需要提供一个自定义生成配置，该配置包括所有必要的设置和层（即在新的“生成目录”中创建的“local.conf”和“bblayers.conf”｛.explored text role=“term”｝），以及设置生成时显示的自定义消息。这可以通过在自定义分发层中创建一个或多个模板配置目录来完成。

This can be done by using `bitbake-layers save-build-conf`:

> 这可以通过使用“bitbake layers save build conf”来完成：

```
$ bitbake-layers save-build-conf ../../meta-alex/ test-1
NOTE: Starting bitbake server...
NOTE: Configuration template placed into /srv/work/alex/meta-alex/conf/templates/test-1
Please review the files in there, and particularly provide a configuration description in /srv/work/alex/meta-alex/conf/templates/test-1/conf-notes.txt
You can try out the configuration with
TEMPLATECONF=/srv/work/alex/meta-alex/conf/templates/test-1 . /srv/work/alex/poky/oe-init-build-env build-try-test-1
```

The above command takes the config files from the currently active `Build Directory`{.interpreted-text role="term"} under `conf`, replaces site-specific paths in `bblayers.conf` with `##OECORE##`-relative paths, and copies the config files into a specified layer under a specified template name.

> 上面的命令从当前活动的 `conf` 下的 `Build Directory`｛.explored text role=“term”｝中获取配置文件，将 `bblayers.conf` 中的特定站点路径替换为 `##OECORE##`-相对路径，并将配置文件复制到指定模板名称下的指定层中。

To use those saved templates as a starting point for a build, users should point to one of them with `TEMPLATECONF`{.interpreted-text role="term"} environment variable:

> 要使用这些保存的模板作为构建的起点，用户应指向其中一个带有 `TEMPLATECOF`{.depredicted text role=“term”}环境变量的模板：

```
TEMPLATECONF=/srv/work/alex/meta-alex/conf/templates/test-1 . /srv/work/alex/poky/oe-init-build-env build-try-test-1
```

The OpenEmbedded build system uses the environment variable `TEMPLATECONF`{.interpreted-text role="term"} to locate the directory from which it gathers configuration information that ultimately ends up in the `Build Directory`{.interpreted-text role="term"} `conf` directory.

> OpenEmbedded 构建系统使用环境变量 `TEMPLATECOF`｛.depreced text role=“term”｝来定位它从中收集配置信息的目录，这些配置信息最终会进入 `build directory`｛.epreced text role=”term“｝`conf` 目录。

If `TEMPLATECONF`{.interpreted-text role="term"} is not set, the default value is obtained from `.templateconf` file that is read from the same directory as `oe-init-build-env` script. For the Poky reference distribution this would be:

> 如果未设置 `TEMPLATECOF`｛.explored text role=“term”｝，则从 `.TEMPLATECONF` 文件中获取默认值，该文件与 `oe-init build-env` 脚本从同一目录中读取。对于 Poky 参考分布，这将是：

```
TEMPLATECONF=${TEMPLATECONF:-meta-poky/conf/templates/default}
```

If you look at a configuration template directory, you will see the `bblayers.conf.sample`, `local.conf.sample`, and `conf-notes.txt` files. The build system uses these files to form the respective `bblayers.conf` file, `local.conf` file, and show users a note about the build they\'re setting up when running the `oe-init-build-env` setup script. These can be edited further if needed to improve or change the build configurations available to the users.

> 如果查看配置模板目录，您将看到“bblayers.conf.sample”、“local.conf.ssample”和“conf-notes.txt”文件。构建系统使用这些文件形成各自的“bblayers.conf”文件和“local.conf”文件，并在运行“oe-init-build-env”设置脚本时向用户显示有关他们正在设置的构建的说明。如果需要改进或更改用户可用的构建配置，可以进一步编辑这些配置。
