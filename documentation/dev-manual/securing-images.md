---
tip: translate by baidu@2023-06-07 17:16:27
...
---
title: Making Images More Secure
--------------------------------

Security is of increasing concern for embedded devices. Consider the issues and problems discussed in just this sampling of work found across the Internet:

> 嵌入式设备越来越关注安全性。考虑一下在互联网上发现的这一工作样本中讨论的问题：

- *\"*[Security Risks of Embedded Systems](https://www.schneier.com/blog/archives/2014/01/security_risks_9.html)*\"* by Bruce Schneier

> -*\“*[嵌入式系统的安全风险](https://www.schneier.com/blog/archives/2014/01/security_risks_9.html)*\“*布鲁斯·施奈尔

- *\"*[Internet Census 2012](http://census2012.sourceforge.net/paper.html)*\"* by Carna Botnet
- *\"*[Security Issues for Embedded Devices](https://elinux.org/images/6/6f/Security-issues.pdf)*\"* by Jake Edge

> -*\“*[嵌入式设备的安全问题](https://elinux.org/images/6/6f/Security-issues.pdf)*\“*作者：Jake Edge

When securing your image is of concern, there are steps, tools, and variables that you can consider to help you reach the security goals you need for your particular device. Not all situations are identical when it comes to making an image secure. Consequently, this section provides some guidance and suggestions for consideration when you want to make your image more secure.

> 当需要保护您的图像时，您可以考虑一些步骤、工具和变量来帮助您实现特定设备所需的安全目标。在确保图像安全方面，并非所有情况都相同。因此，本节提供了一些指导和建议，供您在想要使您的图像更安全时考虑。

::: note
::: title
Note
:::

Because the security requirements and risks are different for every type of device, this section cannot provide a complete reference on securing your custom OS. It is strongly recommended that you also consult other sources of information on embedded Linux system hardening and on security.

> 由于每种类型的设备的安全要求和风险都不同，因此本节无法提供有关保护自定义操作系统的完整参考。强烈建议您还可以查阅有关嵌入式 Linux 系统强化和安全性的其他信息来源。
> :::

# General Considerations

There are general considerations that help you create more secure images. You should consider the following suggestions to make your device more secure:

> 有一些一般注意事项可以帮助您创建更安全的图像。为了使您的设备更加安全，您应该考虑以下建议：

- Scan additional code you are adding to the system (e.g. application code) by using static analysis tools. Look for buffer overflows and other potential security problems.

> -使用静态分析工具扫描要添加到系统中的其他代码（例如应用程序代码）。查找缓冲区溢出和其他潜在的安全问题。

- Pay particular attention to the security for any web-based administration interface.

  Web interfaces typically need to perform administrative functions and tend to need to run with elevated privileges. Thus, the consequences resulting from the interface\'s security becoming compromised can be serious. Look for common web vulnerabilities such as cross-site-scripting (XSS), unvalidated inputs, and so forth.

> Web 接口通常需要执行管理功能，并且往往需要以提升的权限运行。因此，接口的安全性受到损害可能会带来严重后果。查找常见的 web 漏洞，如跨站点脚本（XSS）、未验证的输入等。

As with system passwords, the default credentials for accessing a web-based interface should not be the same across all devices. This is particularly true if the interface is enabled by default as it can be assumed that many end-users will not change the credentials.

> 与系统密码一样，访问基于 web 的界面的默认凭据不应在所有设备上都相同。如果默认情况下启用接口，则情况尤其如此，因为可以假设许多最终用户不会更改凭据。

- Ensure you can update the software on the device to mitigate vulnerabilities discovered in the future. This consideration especially applies when your device is network-enabled.

> -确保您可以更新设备上的软件，以减少将来发现的漏洞。当您的设备启用了网络时，这种考虑尤其适用。

- Regularly scan and apply fixes for CVE security issues affecting all software components in the product, see \"`dev-manual/vulnerabilities:checking for vulnerabilities`{.interpreted-text role="ref"}\".

> -定期扫描并应用影响产品中所有软件组件的 CVE 安全问题的修复程序，请参阅“`dev manual/漏洞：检查漏洞`{.depredicted text role=“ref”}\”。

- Regularly update your version of Poky and OE-Core from their upstream developers, e.g. to apply updates and security fixes from stable and `LTS`{.interpreted-text role="term"} branches.

> -定期从上游开发人员那里更新您的 Poky 和 OE Core 版本，例如从稳定和 `TTS`{.depredicted text role=“term”}分支应用更新和安全修复。

- Ensure you remove or disable debugging functionality before producing the final image. For information on how to do this, see the \"`dev-manual/securing-images:considerations specific to the openembedded build system`{.interpreted-text role="ref"}\" section.

> -在生成最终映像之前，请确保删除或禁用调试功能。有关如何做到这一点的信息，请参阅\“`dev manual/securing images:特定于开放式构建系统的注意事项`{.depreted text role=“ref”}\”一节。

- Ensure you have no network services listening that are not needed.
- Remove any software from the image that is not needed.
- Enable hardware support for secure boot functionality when your device supports this functionality.

> -当您的设备支持安全引导功能时，启用硬件支持。

# Security Flags

The Yocto Project has security flags that you can enable that help make your build output more secure. The security flags are in the `meta/conf/distro/include/security_flags.inc` file in your `Source Directory`{.interpreted-text role="term"} (e.g. `poky`).

> Yocto 项目具有可以启用的安全标志，这些标志有助于使构建输出更加安全。安全标志位于“源目录”中的 `meta/conf/distro/include/security_flags.inc` 文件中（例如 `poky`）。

::: note
::: title
Note
:::

Depending on the recipe, certain security flags are enabled and disabled by default.

> 根据配方的不同，默认情况下会启用和禁用某些安全标志。
> :::

Use the following line in your `local.conf` file or in your custom distribution configuration file to enable the security compiler and linker flags for your build:

> 在“local.conf”文件或自定义分发配置文件中使用以下行为您的构建启用安全编译器和链接器标志：

```
require conf/distro/include/security_flags.inc
```

# Considerations Specific to the OpenEmbedded Build System

You can take some steps that are specific to the OpenEmbedded build system to make your images more secure:

> 您可以采取一些特定于 OpenEmbedded 构建系统的步骤，以使您的图像更加安全：

- Ensure \"debug-tweaks\" is not one of your selected `IMAGE_FEATURES`{.interpreted-text role="term"}. When creating a new project, the default is to provide you with an initial `local.conf` file that enables this feature using the `EXTRA_IMAGE_FEATURES`{.interpreted-text role="term"} variable with the line:

> -请确保“调试调整”不是您选择的 `IMAGE_FATURE`｛.depreted text role=“term”｝之一。创建新项目时，默认情况是使用 `EXTRA_IMAGE_feature`｛.depreted text role=“term”｝变量为您提供一个初始的 `local.conf` 文件，该文件启用此功能，行为：

```
EXTRA_IMAGE_FEATURES = "debug-tweaks"
```

To disable that feature, simply comment out that line in your `local.conf` file, or make sure `IMAGE_FEATURES`{.interpreted-text role="term"} does not contain \"debug-tweaks\" before producing your final image. Among other things, leaving this in place sets the root password as blank, which makes logging in for debugging or inspection easy during development but also means anyone can easily log in during production.

> 要禁用该功能，只需在 `local.conf` 文件中注释掉该行，或者在生成最终图像之前，确保 `IMAGE_FATURE`｛.explored text role=“term”｝不包含\“调试调整”。除其他外，保留此选项会将根密码设置为空，这使得在开发过程中可以轻松登录进行调试或检查，但也意味着任何人都可以在生产过程中轻松登录。

- It is possible to set a root password for the image and also to set passwords for any extra users you might add (e.g. administrative or service type users). When you set up passwords for multiple images or users, you should not duplicate passwords.

> -可以为映像设置根密码，也可以为您可能添加的任何额外用户（例如管理或服务类型用户）设置密码。为多个图像或用户设置密码时，不应重复密码。

To set up passwords, use the `ref-classes-extrausers`{.interpreted-text role="ref"} class, which is the preferred method. For an example on how to set up both root and user passwords, see the \"`ref-classes-extrausers`{.interpreted-text role="ref"}\" section.

> 要设置密码，请使用“ref classes extrausers”｛.explored text role=“ref”｝类，这是首选方法。有关如何设置根密码和用户密码的示例，请参阅\“`ref classes extrausers`｛.depreted text role=“ref”｝\”一节。

::: note
::: title

Note

> 笔记
> :::

When adding extra user accounts or setting a root password, be cautious about setting the same password on every device. If you do this, and the password you have set is exposed, then every device is now potentially compromised. If you need this access but want to ensure security, consider setting a different, random password for each device. Typically, you do this as a separate step after you deploy the image onto the device.

> 当添加额外的用户帐户或设置根密码时，请注意不要在每个设备上设置相同的密码。如果你这样做，并且你设置的密码被暴露，那么现在每个设备都有可能被泄露。如果您需要此访问权限，但希望确保安全，请考虑为每个设备设置不同的随机密码。通常，在将映像部署到设备上之后，您可以将此操作作为单独的步骤来执行。
> :::

- Consider enabling a Mandatory Access Control (MAC) framework such as SMACK or SELinux and tuning it appropriately for your device\'s usage. You can find more information in the :yocto\_[git:%60meta-selinux](git:%60meta-selinux) \</meta-selinux/\>\` layer.

> -考虑启用强制访问控制（MAC）框架，如 SMACK 或 SELinux，并根据设备的使用情况对其进行适当调整。您可以在：yocto\_[git:%60meta selinux]（git:%60meta selinux）\</meta selinux/\>\`层中找到更多信息。

# Tools for Hardening Your Image

The Yocto Project provides tools for making your image more secure. You can find these tools in the `meta-security` layer of the :yocto\_[git:%60Yocto](git:%60Yocto) Project Source Repositories \<\>\`.

> Yocto 项目提供了使您的图像更加安全的工具。您可以在：yocto\_[git:%60Yocto]（git:%60Yocto）项目源存储库\<\>\`的“元安全”层中找到这些工具。
