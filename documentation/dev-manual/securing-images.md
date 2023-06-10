---
tip: translate by openai@2023-06-10 12:19:28
...
---
title: Making Images More Secure
--------------------------------

Security is of increasing concern for embedded devices. Consider the issues and problems discussed in just this sampling of work found across the Internet:

> 安全性对嵌入式设备越来越受到关注。考虑一下在互联网上发现的这些工作中讨论的问题和问题：

- *\"*[Security Risks of Embedded Systems](https://www.schneier.com/blog/archives/2014/01/security_risks_9.html)*\"* by Bruce Schneier
- *\"*[Internet Census 2012](http://census2012.sourceforge.net/paper.html)*\"* by Carna Botnet
- *\"*[Security Issues for Embedded Devices](https://elinux.org/images/6/6f/Security-issues.pdf)*\"* by Jake Edge

When securing your image is of concern, there are steps, tools, and variables that you can consider to help you reach the security goals you need for your particular device. Not all situations are identical when it comes to making an image secure. Consequently, this section provides some guidance and suggestions for consideration when you want to make your image more secure.

> 当您关心镜像安全时，您可以考虑使用一些步骤、工具和变量来帮助您实现特定设备所需的安全目标。在使镜像更安全时，并不是所有情况都完全相同。因此，本节提供了一些指导和建议，以供您在想要使镜像更安全时参考。

::: note
::: title
Note
:::

Because the security requirements and risks are different for every type of device, this section cannot provide a complete reference on securing your custom OS. It is strongly recommended that you also consult other sources of information on embedded Linux system hardening and on security.

> 由于每种设备的安全要求和风险都不同，本节无法提供关于安全性的完整参考。强烈建议您也参考其他有关嵌入式 Linux 系统加固和安全性的信息来源。
> :::

# General Considerations

There are general considerations that help you create more secure images. You should consider the following suggestions to make your device more secure:

> 有一些常规考虑可以帮助您创建更安全的镜像。您应该考虑以下建议，以使您的设备更安全：

- Scan additional code you are adding to the system (e.g. application code) by using static analysis tools. Look for buffer overflows and other potential security problems.

> 对要添加到系统的附加代码(例如应用程序代码)使用静态分析工具进行扫描。查找缓冲区溢出和其他潜在的安全问题。

- Pay particular attention to the security for any web-based administration interface.

  Web interfaces typically need to perform administrative functions and tend to need to run with elevated privileges. Thus, the consequences resulting from the interface\'s security becoming compromised can be serious. Look for common web vulnerabilities such as cross-site-scripting (XSS), unvalidated inputs, and so forth.

> 网络界面通常需要执行管理功能，并且往往需要以提升权限运行。因此，如果界面的安全性受到损害，其后果可能是严重的。请寻找常见的网络漏洞，如跨站脚本(XSS)，未经验证的输入等。

As with system passwords, the default credentials for accessing a web-based interface should not be the same across all devices. This is particularly true if the interface is enabled by default as it can be assumed that many end-users will not change the credentials.

> 就像系统密码一样，访问基于 Web 的界面的默认凭据不应该在所有设备上相同。如果界面默认启用，这尤其重要，因为可以假设许多最终用户不会更改凭据。

- Ensure you can update the software on the device to mitigate vulnerabilities discovered in the future. This consideration especially applies when your device is network-enabled.

> 确保您可以更新设备上的软件，以缓解未来发现的漏洞。当您的设备具有网络功能时，这一考虑尤为重要。

- Regularly scan and apply fixes for CVE security issues affecting all software components in the product, see \"`dev-manual/vulnerabilities:checking for vulnerabilities`\".

> 定期扫描并应用修复产品中所有软件组件受影响的 CVE 安全问题，请参阅“dev-manual/vulnerabilities:checking for vulnerabilities”。

- Regularly update your version of Poky and OE-Core from their upstream developers, e.g. to apply updates and security fixes from stable and `LTS` branches.

> 定期从上游开发者更新 Poky 和 OE-Core 的版本，例如从稳定和长期支持(LTS)分支应用更新和安全修复。

- Ensure you remove or disable debugging functionality before producing the final image. For information on how to do this, see the \"`dev-manual/securing-images:considerations specific to the openembedded build system`\" section.

> 确保在生成最终镜像之前移除或禁用调试功能。有关如何做到这一点的信息，请参阅“dev-manual/securing-images：针对 OpenEmbedded 构建系统的考虑”部分。

- Ensure you have no network services listening that are not needed.
- Remove any software from the image that is not needed.
- Enable hardware support for secure boot functionality when your device supports this functionality.

# Security Flags

The Yocto Project has security flags that you can enable that help make your build output more secure. The security flags are in the `meta/conf/distro/include/security_flags.inc` file in your `Source Directory` (e.g. `poky`).

> 项目 Yocto 有可以开启的安全标志，可以帮助您提高构建输出的安全性。安全标志位于您的源目录(例如 `poky`)中的 `meta/conf/distro/include/security_flags.inc` 文件中。

::: note
::: title
Note
:::

Depending on the recipe, certain security flags are enabled and disabled by default.
:::

Use the following line in your `local.conf` file or in your custom distribution configuration file to enable the security compiler and linker flags for your build:

> 在您的 `local.conf` 文件或自定义发行版配置文件中使用以下行以启用构建的安全编译器和链接器标志：

```
require conf/distro/include/security_flags.inc
```

# Considerations Specific to the OpenEmbedded Build System

You can take some steps that are specific to the OpenEmbedded build system to make your images more secure:

- Ensure \"debug-tweaks\" is not one of your selected `IMAGE_FEATURES` variable with the line:

> 确保“debug-tweaks”不是您选择的 `IMAGE_FEATURES` 变量来启用此功能，行如下：

```
EXTRA_IMAGE_FEATURES = "debug-tweaks"
```

To disable that feature, simply comment out that line in your `local.conf` file, or make sure `IMAGE_FEATURES` does not contain \"debug-tweaks\" before producing your final image. Among other things, leaving this in place sets the root password as blank, which makes logging in for debugging or inspection easy during development but also means anyone can easily log in during production.

> 要禁用该功能，只需在您的 `local.conf` 文件中注释掉该行，或者在生成最终镜像之前确保 `IMAGE_FEATURES` 不包含“debug-tweaks”。除其他外，保留此功能会将根密码设置为空，这在开发期间可以轻松调试或检查登录，但也意味着在生产环境中任何人都可以轻松登录。

- It is possible to set a root password for the image and also to set passwords for any extra users you might add (e.g. administrative or service type users). When you set up passwords for multiple images or users, you should not duplicate passwords.

> 可以为镜像设置根密码，并且可以为您添加的任何额外用户(例如管理或服务类型用户)设置密码。当您为多个镜像或用户设置密码时，不应重复使用密码。

To set up passwords, use the `ref-classes-extrausers`\" section.

> 若要設定密碼，請使用「ref-classes-extrausers」部分。

::: note
::: title
Note
:::

When adding extra user accounts or setting a root password, be cautious about setting the same password on every device. If you do this, and the password you have set is exposed, then every device is now potentially compromised. If you need this access but want to ensure security, consider setting a different, random password for each device. Typically, you do this as a separate step after you deploy the image onto the device.

> 当添加额外的用户帐户或设置根密码时，要谨慎地设置每台设备上的相同密码。如果这样做，并且暴露了您设置的密码，那么每个设备现在都有可能受到攻击。如果您需要此访问权限，但又想确保安全，请考虑为每台设备设置不同的随机密码。通常，您在将映像部署到设备后作为单独的步骤来完成此操作。
> :::

- Consider enabling a Mandatory Access Control (MAC) framework such as SMACK or SELinux and tuning it appropriately for your device\'s usage. You can find more information in the :yocto_[git:%60meta-selinux](git:%60meta-selinux) \</meta-selinux/\>\` layer.

> 考虑启用强制性访问控制(MAC)框架，例如 SMACK 或 SELinux，并适当调整其适用于设备的使用。您可以在:yocto_[git:`meta-selinux`](git:%60meta-selinux%60)</meta-selinux/>层中找到更多信息。

# Tools for Hardening Your Image

The Yocto Project provides tools for making your image more secure. You can find these tools in the `meta-security` layer of the :yocto_[git:%60Yocto](git:%60Yocto) Project Source Repositories \<\>\`.

> 项目 Yocto 提供了工具，可以使您的镜像更安全。您可以在 Yocto 项目源存储库的 meta-security 层中找到这些工具。
