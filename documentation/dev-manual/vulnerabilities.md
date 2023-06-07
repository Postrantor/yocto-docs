---
tip: translate by baidu@2023-06-07 17:18:25
...
---
title: Checking for Vulnerabilities
-----------------------------------

# Vulnerabilities in Poky and OE-Core

The Yocto Project has an infrastructure to track and address unfixed known security vulnerabilities, as tracked by the public `Common Vulnerabilities and Exposures (CVE) <Common_Vulnerabilities_and_Exposures>`{.interpreted-text role="wikipedia"} database.

> Yocto 项目有一个基础设施来跟踪和解决未修复的已知安全漏洞，如公共 `Common vulnerabilities and Exposures（CVE）<Common_Vornerabilities _and_Exposures>`{.depreted text role=“wikipedia”}数据库所跟踪的。

The Yocto Project maintains a [list of known vulnerabilities](https://autobuilder.yocto.io/pub/non-release/patchmetrics/) for packages in Poky and OE-Core, tracking the evolution of the number of unpatched CVEs and the status of patches. Such information is available for the current development version and for each supported release.

> Yocto 项目维护[已知漏洞列表](https://autobuilder.yocto.io/pub/non-release/patchmetrics/)对于 Poky 和 OE Core 中的包，跟踪未修补 CVE 数量的演变和修补的状态。这些信息可用于当前开发版本和每个支持的发行版。

Security is a process, not a product, and thus at any time, a number of security issues may be impacting Poky and OE-Core. It is up to the maintainers, users, contributors and anyone interested in the issues to investigate and possibly fix them by updating software components to newer versions or by applying patches to address them. It is recommended to work with Poky and OE-Core upstream maintainers and submit patches to fix them, see \"`dev-manual/changes:submitting a change to the yocto project`{.interpreted-text role="ref"}\" for details.

> 安全是一个过程，而不是一个产品，因此在任何时候，许多安全问题都可能影响 Poky 和 OE Core。由维护人员、用户、贡献者和任何对这些问题感兴趣的人来调查并可能通过将软件组件更新到新版本或应用补丁来解决这些问题。建议与 Poky 和 OE Core 上游维护人员合作，并提交修补程序来修复它们，有关详细信息，请参阅\“`dev manual/changes:submit a change to the yocto project`{.depreted text role=“ref”}\”。

# Vulnerability check at build time

To enable a check for CVE security vulnerabilities using `ref-classes-cve-check`{.interpreted-text role="ref"} in the specific image or target you are building, add the following setting to your configuration:

> 要在您正在构建的特定映像或目标中使用 `ref classes CVE check`｛.depreted text role=“ref”｝检查 CVE 安全漏洞，请在配置中添加以下设置：

```
INHERIT += "cve-check"
```

The CVE database contains some old incomplete entries which have been deemed not to impact Poky or OE-Core. These CVE entries can be excluded from the check using build configuration:

> CVE 数据库包含一些旧的不完整条目，这些条目被认为不会影响 Poky 或 OE Core。可以使用构建配置将这些 CVE 条目排除在检查之外：

```
include conf/distro/include/cve-extra-exclusions.inc
```

With this CVE check enabled, BitBake build will try to map each compiled software component recipe name and version information to the CVE database and generate recipe and image specific reports. These reports will contain:

> 启用此 CVE 检查后，BitBake 构建将尝试将每个编译的软件组件配方名称和版本信息映射到 CVE 数据库，并生成配方和图像特定报告。这些报告将包含：

- metadata about the software component like names and versions
- metadata about the CVE issue such as description and NVD link
- for each software component, a list of CVEs which are possibly impacting this version
- status of each CVE: `Patched`, `Unpatched` or `Ignored`

The status `Patched` means that a patch file to address the security issue has been applied. `Unpatched` status means that no patches to address the issue have been applied and that the issue needs to be investigated. `Ignored` means that after analysis, it has been deemed to ignore the issue as it for example affects the software component on a different operating system platform.

> 状态“已修补”表示已应用用于解决安全问题的修补文件 `未修补状态意味着尚未应用任何修补程序来解决该问题，需要对该问题进行调查` 忽略”是指经过分析后，它被视为忽略了该问题，因为它例如影响了不同操作系统平台上的软件组件。

After a build with CVE check enabled, reports for each compiled source recipe will be found in `build/tmp/deploy/cve`.

> 在启用 CVE 检查的构建之后，将在“build/tmp/deploy/CVE”中找到每个编译源配方的报告。

For example the CVE check report for the `flex-native` recipe looks like:

> 例如，“flex native”配方的 CVE 检查报告如下所示：

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

> 对于图像，还以文本和 JSON 格式生成图像中包含的所有配方及其 CVE 的摘要。这些“.cve”和“.json”报告可以在每个编译的映像的“tmp/deploy/images”目录中找到。

At build time CVE check will also throw warnings about `Unpatched` CVEs:

> 在构建时，CVE 检查还将引发有关“未匹配”CVE 的警告：

```
WARNING: flex-2.6.4-r0 do_cve_check: Found unpatched CVE (CVE-2019-6293), for more information check /poky/build/tmp/work/core2-64-poky-linux/flex/2.6.4-r0/temp/cve.log
WARNING: libarchive-3.5.1-r0 do_cve_check: Found unpatched CVE (CVE-2021-36976), for more information check /poky/build/tmp/work/core2-64-poky-linux/libarchive/3.5.1-r0/temp/cve.log
```

It is also possible to check the CVE status of individual packages as follows:

> 还可以检查单个包裹的 CVE 状态，如下所示：

```
bitbake -c cve_check flex libarchive
```

# Fixing CVE product name and version mappings

By default, `ref-classes-cve-check`{.interpreted-text role="ref"} uses the recipe name `BPN`{.interpreted-text role="term"} as CVE product name when querying the CVE database. If this mapping contains false positives, e.g. some reported CVEs are not for the software component in question, or false negatives like some CVEs are not found to impact the recipe when they should, then the problems can be in the recipe name to CVE product mapping. These mapping issues can be fixed by setting the `CVE_PRODUCT`{.interpreted-text role="term"} variable inside the recipe. This defines the name of the software component in the upstream [NIST CVE database](https://nvd.nist.gov/).

> 默认情况下，在查询 cve 数据库时，`ref classes cve check`｛.depreted text role=“ref”｝使用配方名称 `BPN`｛.repreted text role=“term”｝作为 cve 产品名称。如果该映射包含假阳性，例如，一些报告的 CVE 不适用于有问题的软件组件，或者假阴性，如一些 CVE 在应该影响配方时没有被发现，那么问题可能出现在配方名称到 CVE 产品映射中。这些映射问题可以通过在配方中设置 `CVE_PRODUCT`｛.explored text role=“term”｝变量来解决。这定义了上游[NST CVE 数据库]中软件组件的名称([https://nvd.nist.gov/](https://nvd.nist.gov/))。

The variable supports using vendor and product names like this:

> 该变量支持使用供应商和产品名称，如下所示：

```
CVE_PRODUCT = "flex_project:flex"
```

In this example the vendor name used in the CVE database is `flex_project` and the product is `flex`. With this setting the `flex` recipe only maps to this specific product and not products from other vendors with same name `flex`.

> 在这个例子中，CVE 数据库中使用的供应商名称是“flex_project”，产品是“flex”。通过此设置，“flex”配方仅映射到此特定产品，而不映射来自同名“flex”的其他供应商的产品。

Similarly, when the recipe version `PV`{.interpreted-text role="term"} is not compatible with software versions used by the upstream software component releases and the CVE database, these can be fixed using the `CVE_VERSION`{.interpreted-text role="term"} variable.

> 类似地，当配方版本 `PV`｛.depredicted text role=“term”｝与上游软件组件版本和 CVE 数据库使用的软件版本不兼容时，可以使用 `CVE_version`｛.epredicted text role=”term“｝变量来修复这些问题。

Note that if the CVE entries in the NVD database contain bugs or have missing or incomplete information, it is recommended to fix the information there directly instead of working around the issues possibly for a long time in Poky and OE-Core side recipes. Feedback to NVD about CVE entries can be provided through the [NVD contact form](https://nvd.nist.gov/info/contact-form).

# Fixing vulnerabilities in recipes

If a CVE security issue impacts a software component, it can be fixed by updating to a newer version of the software component or by applying a patch. For Poky and OE-Core master branches, updating to a newer software component release with fixes is the best option, but patches can be applied if releases are not yet available.

> 如果 CVE 安全问题影响软件组件，可以通过更新到软件组件的新版本或应用补丁来解决。对于 Poky 和 OE Core 主分支，更新到更新的软件组件版本并进行修复是最好的选择，但如果版本尚不可用，则可以应用补丁。

For stable branches, it is preferred to apply patches for the issues. For some software components minor version updates can also be applied if they are backwards compatible.

> 对于稳定的分支，最好为这些问题应用修补程序。对于某些软件组件，如果它们向后兼容，也可以应用次要版本更新。

Here is an example of fixing CVE security issues with patch files, an example from the :oe_layerindex:\`ffmpeg recipe\</layerindex/recipe/47350\>\`:

> 以下是修复补丁文件中 CVE 安全问题的示例，示例来自：oe_layeindex:\`ffmpeg recipe\</layerndex/recipe/47350\>\`：

```
SRC_URI = "https://www.ffmpeg.org/releases/${BP}.tar.xz \
           file://0001-libavutil-include-assembly-with-full-path-from-sourc.patch \
           file://fix-CVE-2020-20446.patch \
           file://fix-CVE-2020-20453.patch \
           file://fix-CVE-2020-22015.patch \
           file://fix-CVE-2020-22021.patch \
           file://fix-CVE-2020-22033-CVE-2020-22019.patch \
           file://fix-CVE-2021-33815.patch \
```

A good practice is to include the CVE identifier in both the patch file name and inside the patch file commit message using the format:

> 一个好的做法是使用以下格式将 CVE 标识符包含在补丁文件名和补丁文件提交消息中：

```
CVE: CVE-2020-22033
```

CVE checker will then capture this information and change the CVE status to `Patched` in the generated reports.

> CVE 检查器随后将捕获此信息，并在生成的报告中将 CVE 状态更改为“已修补”。

If analysis shows that the CVE issue does not impact the recipe due to configuration, platform, version or other reasons, the CVE can be marked as `Ignored` using the `CVE_CHECK_IGNORE`{.interpreted-text role="term"} variable. As mentioned previously, if data in the CVE database is wrong, it is recommend to fix those issues in the CVE database directly.

> 如果分析表明，由于配置、平台、版本或其他原因，CVE 问题不会影响配方，则可以使用 `CVE_CHECK_IGNORE`｛.explored text role=“term”｝变量将 CVE 标记为 `Ignored'。如前所述，如果 CVE 数据库中的数据错误，建议直接修复 CVE 数据库的这些问题。

Recipes can be completely skipped by CVE check by including the recipe name in the `CVE_CHECK_SKIP_RECIPE`{.interpreted-text role="term"} variable.

> CVE 检查可以完全跳过配方，方法是将配方名称包含在 `CVE_check_SKIP_recipe`｛.explored text role=“term”｝变量中。

# Implementation details

Here\'s what the `ref-classes-cve-check`{.interpreted-text role="ref"} class does to find unpatched CVE IDs.

> 以下是 `ref classes cve check`｛.explored text role=“ref”｝类查找未匹配的 cve ID 所做的操作。

First the code goes through each patch file provided by a recipe. If a valid CVE ID is found in the name of the file, the corresponding CVE is considered as patched. Don\'t forget that if multiple CVE IDs are found in the filename, only the last one is considered. Then, the code looks for `CVE: CVE-ID` lines in the patch file. The found CVE IDs are also considered as patched.

> 首先，代码遍历由配方提供的每个补丁文件。如果在文件名中找到有效的 CVE ID，则相应的 CVE 被视为已修补。不要忘记，如果在文件名中找到多个 CVE ID，则只考虑最后一个。然后，代码在补丁文件中查找“CVE:CVE-ID”行。发现的 CVE ID 也被视为已修补。

Then, the code looks up all the CVE IDs in the NIST database for all the products defined in `CVE_PRODUCT`{.interpreted-text role="term"}. Then, for each found CVE:

> 然后，代码在 NIST 数据库中查找“CVE_PRODUCT”｛.explored text role=“term”｝中定义的所有产品的所有 CVE ID。然后，对于每个发现的 CVE：

- If the package name (`PN`{.interpreted-text role="term"}) is part of `CVE_CHECK_SKIP_RECIPE`{.interpreted-text role="term"}, it is considered as `Patched`.

> -如果程序包名称（`PN`｛.depreced text role=“term”｝）是 `CVE_CHECK_SKIP_RECIPE`｛.epreced textrole=”term“｝的一部分，则将其视为 `Patched'。

- If the CVE ID is part of `CVE_CHECK_IGNORE`{.interpreted-text role="term"}, it is set as `Ignored`.

> -如果 CVE ID 是 `CVE_CHECK_IGNORE`｛.explored text role=“term”｝的一部分，则将其设置为 `Ignored'。

- If the CVE ID is part of the patched CVE for the recipe, it is already considered as `Patched`.
- Otherwise, the code checks whether the recipe version (`PV`{.interpreted-text role="term"}) is within the range of versions impacted by the CVE. If so, the CVE is considered as `Unpatched`.

> -否则，代码将检查配方版本（`PV`｛.explored text role=“term”｝）是否在受 CVE 影响的版本范围内。如果是，则 CVE 被视为“未匹配”。

The CVE database is stored in `DL_DIR`{.interpreted-text role="term"} and can be inspected using `sqlite3` command as follows:

> CVE 数据库存储在 `DL_DIR`｛.explored text role=“term”｝中，可以使用 `sqlite3` 命令进行检查，如下所示：

```
sqlite3 downloads/CVE_CHECK/nvdcve_1.1.db .dump | grep CVE-2021-37462
```

When analyzing CVEs, it is recommended to:

> 在分析 CVE 时，建议：

- study the latest information in [CVE database](https://nvd.nist.gov/vuln/search).
- check how upstream developers of the software component addressed the issue, e.g. what patch was applied, which upstream release contains the fix.

> -检查软件组件的上游开发人员是如何解决这个问题的，例如应用了什么补丁，哪个上游版本包含修复程序。

- check what other Linux distributions like [Debian](https://security-tracker.debian.org/tracker/) did to analyze and address the issue.

> -查看像[Debian]这样的其他 Linux 发行版([https://security-tracker.debian.org/tracker/](https://security-tracker.debian.org/tracker/))做了分析并解决了这个问题。

- follow security notices from other Linux distributions.
- follow public [open source security mailing lists](https://oss-security.openwall.org/wiki/mailing-lists) for discussions and advance notifications of CVE bugs and software releases with fixes.

> -关注公共[开源安全邮件列表](https://oss-security.openwall.org/wiki/mailing-lists)用于 CVE 错误和带有修复程序的软件发布的讨论和提前通知。
