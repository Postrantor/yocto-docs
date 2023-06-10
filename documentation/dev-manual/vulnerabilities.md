---
tip: translate by openai@2023-06-10 12:39:52
...
---
title: Checking for Vulnerabilities
-----------------------------------

# Vulnerabilities in Poky and OE-Core

The Yocto Project has an infrastructure to track and address unfixed known security vulnerabilities, as tracked by the public `Common Vulnerabilities and Exposures (CVE) <Common_Vulnerabilities_and_Exposures>` database.

> 项目 Yocto 拥有一个基础架构来跟踪和解决未修复的已知安全漏洞，这些漏洞由公开的“通用漏洞与暴露(CVE)”数据库跟踪。

The Yocto Project maintains a [list of known vulnerabilities](https://autobuilder.yocto.io/pub/non-release/patchmetrics/) for packages in Poky and OE-Core, tracking the evolution of the number of unpatched CVEs and the status of patches. Such information is available for the current development version and for each supported release.

> 项目 Yocto 维护了一个 Poky 和 OE-Core 中包的已知漏洞列表，跟踪未修补的 CVE 数量的变化以及补丁的状态。这些信息适用于当前的开发版本和每个支持的版本。

Security is a process, not a product, and thus at any time, a number of security issues may be impacting Poky and OE-Core. It is up to the maintainers, users, contributors and anyone interested in the issues to investigate and possibly fix them by updating software components to newer versions or by applying patches to address them. It is recommended to work with Poky and OE-Core upstream maintainers and submit patches to fix them, see \"`dev-manual/changes:submitting a change to the yocto project`\" for details.

> 安全是一个过程，而不是一个产品，因此，随时会有许多安全问题影响 Poky 和 OE-Core。由维护者、用户、贡献者和任何对这些问题感兴趣的人来调查并通过更新软件组件到更新版本或通过应用补丁来解决它们。建议与 Poky 和 OE-Core 上游维护者一起工作，并提交补丁以修复它们，有关详细信息，请参见“dev-manual/changes:submitting a change to the yocto project”。

# Vulnerability check at build time

To enable a check for CVE security vulnerabilities using `ref-classes-cve-check` in the specific image or target you are building, add the following setting to your configuration:

> 要在您正在构建的特定镜像或目标上启用 CVE 安全漏洞检查，请在配置中添加以下设置：ref-classes-cve-check。

```
INHERIT += "cve-check"
```

The CVE database contains some old incomplete entries which have been deemed not to impact Poky or OE-Core. These CVE entries can be excluded from the check using build configuration:

> CVE 数据库包含一些旧的不完整的条目，被认为不会影响 Poky 或 OE-Core。可以使用构建配置来排除这些 CVE 条目：

```
include conf/distro/include/cve-extra-exclusions.inc
```

With this CVE check enabled, BitBake build will try to map each compiled software component recipe name and version information to the CVE database and generate recipe and image specific reports. These reports will contain:

> 启用此 CVE 检查后，BitBake 构建将尝试将每个编译的软件组件配方名称和版本信息映射到 CVE 数据库，并生成特定配方和镜像的报告。这些报告将包括：

- metadata about the software component like names and versions
- metadata about the CVE issue such as description and NVD link
- for each software component, a list of CVEs which are possibly impacting this version
- status of each CVE: `Patched`, `Unpatched` or `Ignored`

The status `Patched` means that a patch file to address the security issue has been applied. `Unpatched` status means that no patches to address the issue have been applied and that the issue needs to be investigated. `Ignored` means that after analysis, it has been deemed to ignore the issue as it for example affects the software component on a different operating system platform.

> 状态“已补丁”意味着已应用补丁文件来解决安全问题。“未补丁”状态意味着尚未应用任何补丁来解决该问题，需要进行调查。“忽略”意味着经过分析，已决定忽略该问题，因为它例如影响不同操作系统平台上的软件组件。

After a build with CVE check enabled, reports for each compiled source recipe will be found in `build/tmp/deploy/cve`.

For example the CVE check report for the `flex-native` recipe looks like:

```
$ cat poky/build/tmp/deploy/cve/flex-native
LAYER: meta
PACKAGE NAME: flex-native
PACKAGE VERSION: 2.6.4
CVE: CVE-2016-6354
CVE STATUS: Patched
CVE SUMMARY: Heap-based buffer overflow in the yy_get_next_buffer function in Flex before 2.6.1 might allow context-dependent attackers to cause a denial of service or possibly execute arbitrary code via vectors involving num_to_read.
CVSS v2 BASE SCORE: 7.5
CVSS v3 BASE SCORE: 9.8
VECTOR: NETWORK
MORE INFORMATION: https://nvd.nist.gov/vuln/detail/CVE-2016-6354

LAYER: meta
PACKAGE NAME: flex-native
PACKAGE VERSION: 2.6.4
CVE: CVE-2019-6293
CVE STATUS: Ignored
CVE SUMMARY: An issue was discovered in the function mark_beginning_as_normal in nfa.c in flex 2.6.4. There is a stack exhaustion problem caused by the mark_beginning_as_normal function making recursive calls to itself in certain scenarios involving lots of '*' characters. Remote attackers could leverage this vulnerability to cause a denial-of-service.
CVSS v2 BASE SCORE: 4.3
CVSS v3 BASE SCORE: 5.5
VECTOR: NETWORK
MORE INFORMATION: https://nvd.nist.gov/vuln/detail/CVE-2019-6293
```

For images, a summary of all recipes included in the image and their CVEs is also generated in textual and JSON formats. These `.cve` and `.json` reports can be found in the `tmp/deploy/images` directory for each compiled image.

> 对于镜像，还会生成以文本和 JSON 格式汇总的镜像中包含的所有配方及其 CVE 的摘要。这些 `.cve` 和 `.json` 报告可以在每个编译镜像的 `tmp/deploy/images` 目录中找到。

At build time CVE check will also throw warnings about `Unpatched` CVEs:

```
WARNING: flex-2.6.4-r0 do_cve_check: Found unpatched CVE (CVE-2019-6293), for more information check /poky/build/tmp/work/core2-64-poky-linux/flex/2.6.4-r0/temp/cve.log
WARNING: libarchive-3.5.1-r0 do_cve_check: Found unpatched CVE (CVE-2021-36976), for more information check /poky/build/tmp/work/core2-64-poky-linux/libarchive/3.5.1-r0/temp/cve.log
```

It is also possible to check the CVE status of individual packages as follows:

```
bitbake -c cve_check flex libarchive
```

# Fixing CVE product name and version mappings

By default, `ref-classes-cve-check` variable inside the recipe. This defines the name of the software component in the upstream [NIST CVE database](https://nvd.nist.gov/).

> 默认情况下，`ref-classes-cve-check` 变量来解决这些映射问题。这定义了上游 [NIST CVE 数据库](https://nvd.nist.gov/)中软件组件的名称。

The variable supports using vendor and product names like this:

```
CVE_PRODUCT = "flex_project:flex"
```

In this example the vendor name used in the CVE database is `flex_project` and the product is `flex`. With this setting the `flex` recipe only maps to this specific product and not products from other vendors with same name `flex`.

> 在这个例子中，CVE 数据库中使用的供应商名称是 `flex_project`，产品是 `flex`。设置为这个的情况下，`flex` 配方只映射到这个特定的产品，而不是其他供应商同名的 `flex` 产品。

Similarly, when the recipe version `PV` variable.

> 同样，当配方版本 `PV` 不兼容上游软件组件发布和 CVE 数据库使用的软件版本时，可以使用 `CVE_VERSION` 变量来修复这些问题。

Note that if the CVE entries in the NVD database contain bugs or have missing or incomplete information, it is recommended to fix the information there directly instead of working around the issues possibly for a long time in Poky and OE-Core side recipes. Feedback to NVD about CVE entries can be provided through the [NVD contact form](https://nvd.nist.gov/info/contact-form).

# Fixing vulnerabilities in recipes

If a CVE security issue impacts a software component, it can be fixed by updating to a newer version of the software component or by applying a patch. For Poky and OE-Core master branches, updating to a newer software component release with fixes is the best option, but patches can be applied if releases are not yet available.

> 如果 CVE 安全问题影响软件组件，可以通过更新到软件组件的新版本或者应用补丁来解决。对于 Poky 和 OE-Core 主分支，更新到具有修复的新软件组件版本是最好的选择，但是如果没有可用的发行版本，也可以应用补丁。

For stable branches, it is preferred to apply patches for the issues. For some software components minor version updates can also be applied if they are backwards compatible.

> 对于稳定分支，建议应用补丁来解决问题。如果某些软件组件的次版本更新是向后兼容的，也可以应用这些更新。

Here is an example of fixing CVE security issues with patch files, an example from the :oe_layerindex:\`ffmpeg recipe\</layerindex/recipe/47350\>\`:

```
SRC_URI = "https://www.ffmpeg.org/releases/$.tar.xz \
           file://0001-libavutil-include-assembly-with-full-path-from-sourc.patch \
           file://fix-CVE-2020-20446.patch \
           file://fix-CVE-2020-20453.patch \
           file://fix-CVE-2020-22015.patch \
           file://fix-CVE-2020-22021.patch \
           file://fix-CVE-2020-22033-CVE-2020-22019.patch \
           file://fix-CVE-2021-33815.patch \
```

A good practice is to include the CVE identifier in both the patch file name and inside the patch file commit message using the format:

```
CVE: CVE-2020-22033
```

CVE checker will then capture this information and change the CVE status to `Patched` in the generated reports.

If analysis shows that the CVE issue does not impact the recipe due to configuration, platform, version or other reasons, the CVE can be marked as `Ignored` using the `CVE_CHECK_IGNORE` variable. As mentioned previously, if data in the CVE database is wrong, it is recommend to fix those issues in the CVE database directly.

> 如果分析表明，由于配置、平台、版本或其他原因，CVE 问题不会影响配方，可以使用 `CVE_CHECK_IGNORE` 变量将 CVE 标记为 `忽略`。正如之前提到的，如果 CVE 数据库中的数据有误，建议直接在 CVE 数据库中修复这些问题。

Recipes can be completely skipped by CVE check by including the recipe name in the `CVE_CHECK_SKIP_RECIPE` variable.

# Implementation details

Here\'s what the `ref-classes-cve-check` class does to find unpatched CVE IDs.

First the code goes through each patch file provided by a recipe. If a valid CVE ID is found in the name of the file, the corresponding CVE is considered as patched. Don\'t forget that if multiple CVE IDs are found in the filename, only the last one is considered. Then, the code looks for `CVE: CVE-ID` lines in the patch file. The found CVE IDs are also considered as patched.

> 首先，代码会遍历由配方提供的每个补丁文件。如果在文件名中发现有效的 CVE ID，则将相应的 CVE 视为已修补。别忘了，如果文件名中发现多个 CVE ID，只有最后一个才被考虑。然后，代码会在补丁文件中寻找“CVE：CVE-ID”行。找到的 CVE ID 也被视为已修补。

Then, the code looks up all the CVE IDs in the NIST database for all the products defined in `CVE_PRODUCT`. Then, for each found CVE:

> 然后，代码会在 NIST 数据库中查找定义在 CVE_PRODUCT 中的所有产品的 CVE ID。然后，对于每个发现的 CVE：

- If the package name (`PN`, it is considered as `Patched`.

> 如果包名(PN)是 CVE_CHECK_SKIP_RECIPE 的一部分，则被视为已修补。

- If the CVE ID is part of `CVE_CHECK_IGNORE`, it is set as `Ignored`.
- If the CVE ID is part of the patched CVE for the recipe, it is already considered as `Patched`.
- Otherwise, the code checks whether the recipe version (`PV`) is within the range of versions impacted by the CVE. If so, the CVE is considered as `Unpatched`.

> 否则，代码检查 recipes 版本(PV)是否在受 CVE 影响的版本范围内。如果是，则将 CVE 视为“未补丁”。

The CVE database is stored in `DL_DIR` and can be inspected using `sqlite3` command as follows:

```
sqlite3 downloads/CVE_CHECK/nvdcve_1.1.db .dump | grep CVE-2021-37462
```

When analyzing CVEs, it is recommended to:

- study the latest information in [CVE database](https://nvd.nist.gov/vuln/search).
- check how upstream developers of the software component addressed the issue, e.g. what patch was applied, which upstream release contains the fix.
- check what other Linux distributions like [Debian](https://security-tracker.debian.org/tracker/) did to analyze and address the issue.
- follow security notices from other Linux distributions.
- follow public [open source security mailing lists](https://oss-security.openwall.org/wiki/mailing-lists) for discussions and advance notifications of CVE bugs and software releases with fixes.

> 跟随公共[开源安全邮件列表](https://oss-security.openwall.org/wiki/mailing-lists)进行讨论，并提前获取 CVE 漏洞和软件发布版本的修复通知。
