---
title: Release 2.3 (pyro)
---
This section provides migration information for moving to the Yocto Project 2.3 Release (codename \"pyro\") from the prior release.

# Recipe-specific Sysroots {#migration-2.3-recipe-specific-sysroots}

The OpenEmbedded build system now uses one sysroot per recipe to resolve long-standing issues with configuration script auto-detection of undeclared dependencies. Consequently, you might find that some of your previously written custom recipes are missing declared dependencies, particularly those dependencies that are incidentally built earlier in a typical build process and thus are already likely to be present in the shared sysroot in previous releases.

Consider the following:

- *Declare Build-Time Dependencies:* Because of this new feature, you must explicitly declare all build-time dependencies for your recipe. If you do not declare these dependencies, they are not populated into the sysroot for the recipe.
- *Specify Pre-Installation and Post-Installation Native Tool Dependencies:* You must specifically specify any special native tool dependencies of `pkg_preinst` and `pkg_postinst` scripts by using the `PACKAGE_WRITE_DEPS`{.interpreted-text role="term"} variable. Specifying these dependencies ensures that these tools are available if these scripts need to be run on the build host during the `ref-tasks-rootfs`{.interpreted-text role="ref"} task.

  As an example, see the `dbus` recipe. You will see that this recipe has a `pkg_postinst` that calls `systemctl` if \"systemd\" is in `DISTRO_FEATURES`{.interpreted-text role="term"}. In the example, `systemd-systemctl-native` is added to `PACKAGE_WRITE_DEPS`{.interpreted-text role="term"}, which is also conditional on \"systemd\" being in `DISTRO_FEATURES`{.interpreted-text role="term"}.
- Examine Recipes that Use `SSTATEPOSTINSTFUNCS`: You need to examine any recipe that uses `SSTATEPOSTINSTFUNCS` and determine steps to take.

  Functions added to `SSTATEPOSTINSTFUNCS` are still called as they were in previous Yocto Project releases. However, since a separate sysroot is now being populated for every recipe and if existing functions being called through `SSTATEPOSTINSTFUNCS` are doing relocation, then you will need to change these to use a post-installation script that is installed by a function added to `SYSROOT_PREPROCESS_FUNCS`{.interpreted-text role="term"}.

  For an example, see the `ref-classes-pixbufcache`{.interpreted-text role="ref"} class in `meta/classes/` in the `overview-manual/development-environment:yocto project source repositories`{.interpreted-text role="ref"}.

  ::: note
  ::: title
  Note
  :::

  The SSTATEPOSTINSTFUNCS variable itself is now deprecated in favor of the do_populate_sysroot\[postfuncs\] task. Consequently, if you do still have any function or functions that need to be called after the sysroot component is created for a recipe, then you would be well advised to take steps to use a post installation script as described previously. Taking these steps prepares your code for when SSTATEPOSTINSTFUNCS is removed in a future Yocto Project release.
  :::
- *Specify the Sysroot when Using Certain External Scripts:* Because the shared sysroot is now gone, the scripts `oe-find-native-sysroot` and `oe-run-native` have been changed such that you need to specify which recipe\'s `STAGING_DIR_NATIVE`{.interpreted-text role="term"} is used.

::: note
::: title
Note
:::

You can find more information on how recipe-specific sysroots work in the \"`ref-classes-staging`{.interpreted-text role="ref"}\" section.
:::

# `PATH` Variable {#migration-2.3-path-variable}

Within the environment used to run build tasks, the environment variable `PATH` is now sanitized such that the normal native binary paths (`/bin`, `/sbin`, `/usr/bin` and so forth) are removed and a directory containing symbolic links linking only to the binaries from the host mentioned in the `HOSTTOOLS`{.interpreted-text role="term"} and `HOSTTOOLS_NONFATAL`{.interpreted-text role="term"} variables is added to `PATH`.

Consequently, any native binaries provided by the host that you need to call needs to be in one of these two variables at the configuration level.

Alternatively, you can add a native recipe (i.e. `-native`) that provides the binary to the recipe\'s `DEPENDS`{.interpreted-text role="term"} value.

::: note
::: title
Note
:::

PATH is not sanitized in the same way within `devshell`. If it were, you would have difficulty running host tools for development and debugging within the shell.
:::

# Changes to Scripts {#migration-2.3-scripts}

The following changes to scripts took place:

- `oe-find-native-sysroot`: The usage for the `oe-find-native-sysroot` script has changed to the following:

  ```
  $ . oe-find-native-sysroot recipe
  ```

  You must now supply a recipe for recipe as part of the command. Prior to the Yocto Project 2.3 release, it was not necessary to provide the script with the command.
- `oe-run-native`: The usage for the `oe-run-native` script has changed to the following:

  ```
  $ oe-run-native native_recipe tool
  ```

  You must supply the name of the native recipe and the tool you want to run as part of the command. Prior to the Yocto Project 2.3 release, it was not necessary to provide the native recipe with the command.
- `cleanup-workdir`: The `cleanup-workdir` script has been removed because the script was found to be deleting files it should not have, which lead to broken build trees. Rather than trying to delete portions of `TMPDIR`{.interpreted-text role="term"} and getting it wrong, it is recommended that you delete `TMPDIR`{.interpreted-text role="term"} and have it restored from shared state (sstate) on subsequent builds.
- `wipe-sysroot`: The `wipe-sysroot` script has been removed as it is no longer needed with recipe-specific sysroots.

# Changes to Functions {#migration-2.3-functions}

The previously deprecated `bb.data.getVar()`, `bb.data.setVar()`, and related functions have been removed in favor of `d.getVar()`, `d.setVar()`, and so forth.

You need to fix any references to these old functions.

# BitBake Changes {#migration-2.3-bitbake-changes}

The following changes took place for BitBake:

- *BitBake\'s Graphical Dependency Explorer UI Replaced:* BitBake\'s graphical dependency explorer UI `depexp` was replaced by `taskexp` (\"Task Explorer\"), which provides a graphical way of exploring the `task-depends.dot` file. The data presented by Task Explorer is much more accurate than the data that was presented by `depexp`. Being able to visualize the data is an often requested feature as standard `*.dot` file viewers cannot usual cope with the size of the `task-depends.dot` file.
- *BitBake \"-g\" Output Changes:* The `package-depends.dot` and `pn-depends.dot` files as previously generated using the `bitbake -g` command have been removed. A `recipe-depends.dot` file is now generated as a collapsed version of `task-depends.dot` instead.

  The reason for this change is because `package-depends.dot` and `pn-depends.dot` largely date back to a time before task-based execution and do not take into account task-level dependencies between recipes, which could be misleading.
- *Mirror Variable Splitting Changes:* Mirror variables including `MIRRORS`{.interpreted-text role="term"}, `PREMIRRORS`{.interpreted-text role="term"}, and `SSTATE_MIRRORS`{.interpreted-text role="term"} can now separate values entirely with spaces. Consequently, you no longer need \"\\n\". BitBake looks for pairs of values, which simplifies usage. There should be no change required to existing mirror variable values themselves.
- *The Subversion (SVN) Fetcher Uses an \"ssh\" Parameter and Not an \"rsh\" Parameter:* The SVN fetcher now takes an \"ssh\" parameter instead of an \"rsh\" parameter. This new optional parameter is used when the \"protocol\" parameter is set to \"svn+ssh\". You can only use the new parameter to specify the `ssh` program used by SVN. The SVN fetcher passes the new parameter through the `SVN_SSH` environment variable during the `ref-tasks-fetch`{.interpreted-text role="ref"} task.

  See the \"``bitbake-user-manual/bitbake-user-manual-fetching:subversion (svn) fetcher (\`\`svn://\`\`)``{.interpreted-text role="ref"}\" section in the BitBake User Manual for additional information.
- `BB_SETSCENE_VERIFY_FUNCTION` and `BB_SETSCENE_VERIFY_FUNCTION2` Removed: Because the mechanism they were part of is no longer necessary with recipe-specific sysroots, the `BB_SETSCENE_VERIFY_FUNCTION` and `BB_SETSCENE_VERIFY_FUNCTION2` variables have been removed.

# Absolute Symbolic Links {#migration-2.3-absolute-symlinks}

Absolute symbolic links (symlinks) within staged files are no longer permitted and now trigger an error. Any explicit creation of symlinks can use the `lnr` script, which is a replacement for `ln -r`.

If the build scripts in the software that the recipe is building are creating a number of absolute symlinks that need to be corrected, you can inherit `relative_symlinks` within the recipe to turn those absolute symlinks into relative symlinks.

# GPLv2 Versions of GPLv3 Recipes Moved {#migration-2.3-gplv2-and-gplv3-moves}

Older GPLv2 versions of GPLv3 recipes have moved to a separate `meta-gplv2` layer.

If you use `INCOMPATIBLE_LICENSE`{.interpreted-text role="term"} to exclude GPLv3 or set `PREFERRED_VERSION`{.interpreted-text role="term"} to substitute a GPLv2 version of a GPLv3 recipe, then you must add the `meta-gplv2` layer to your configuration.

::: note
::: title
Note
:::

You can `find meta-gplv2` layer in the OpenEmbedded layer index at :oe_layer:[/meta-gplv2]{.title-ref}.
:::

These relocated GPLv2 recipes do not receive the same level of maintenance as other core recipes. The recipes do not get security fixes and upstream no longer maintains them. In fact, the upstream community is actively hostile towards people that use the old versions of the recipes. Moving these recipes into a separate layer both makes the different needs of the recipes clearer and clearly identifies the number of these recipes.

::: note
::: title
Note
:::

The long-term solution might be to move to BSD-licensed replacements of the GPLv3 components for those that need to exclude GPLv3-licensed components from the target system. This solution will be investigated for future Yocto Project releases.
:::

# Package Management Changes {#migration-2.3-package-management-changes}

The following package management changes took place:

- Smart package manager is replaced by DNF package manager. Smart has become unmaintained upstream, is not ported to Python 3.x. Consequently, Smart needed to be replaced. DNF is the only feasible candidate.

  The change in functionality is that the on-target runtime package management from remote package feeds is now done with a different tool that has a different set of command-line options. If you have scripts that call the tool directly, or use its API, they need to be fixed.

  For more information, see the [DNF Documentation](https://dnf.readthedocs.io/en/latest/).
- Rpm 5.x is replaced with Rpm 4.x. This is done for two major reasons:

  - DNF is API-incompatible with Rpm 5.x and porting it and maintaining the port is non-trivial.
  - Rpm 5.x itself has limited maintenance upstream, and the Yocto Project is one of the very few remaining users.
- Berkeley DB 6.x is removed and Berkeley DB 5.x becomes the default:

  - Version 6.x of Berkeley DB has largely been rejected by the open source community due to its AGPLv3 license. As a result, most mainstream open source projects that require DB are still developed and tested with DB 5.x.
  - In OE-core, the only thing that was requiring DB 6.x was Rpm 5.x. Thus, no reason exists to continue carrying DB 6.x in OE-core.
- `createrepo` is replaced with `createrepo_c`.

  `createrepo_c` is the current incarnation of the tool that generates remote repository metadata. It is written in C as compared to `createrepo`, which is written in Python. `createrepo_c` is faster and is maintained.
- Architecture-independent RPM packages are \"noarch\" instead of \"all\".

  This change was made because too many places in DNF/RPM4 stack already make that assumption. Only the filenames and the architecture tag has changed. Nothing else has changed in OE-core system, particularly in the `ref-classes-allarch`{.interpreted-text role="ref"} class.
- Signing of remote package feeds using `PACKAGE_FEED_SIGN` is not currently supported. This issue will be fully addressed in a future Yocto Project release. See :yocto_bugs:[defect 11209 \</show_bug.cgi?id=11209\>]{.title-ref} for more information on a solution to package feed signing with RPM in the Yocto Project 2.3 release.
- OPKG now uses the libsolv backend for resolving package dependencies by default. This is vastly superior to OPKG\'s internal ad-hoc solver that was previously used. This change does have a small impact on disk (around 500 KB) and memory footprint.

  ::: note
  ::: title
  Note
  :::

  For further details on this change, see the :yocto\_[git:%60commit](git:%60commit) message \</poky/commit/?id=f4d4f99cfbc2396e49c1613a7d237b9e57f06f81\>\`.
  :::

# Removed Recipes {#migration-2.3-removed-recipes}

The following recipes have been removed:

- `linux-yocto 4.8`: Version 4.8 has been removed. Versions 4.1 (LTSI), 4.4 (LTS), 4.9 (LTS/LTSI) and 4.10 are now present.
- `python-smartpm`: Functionally replaced by `dnf`.
- `createrepo`: Replaced by the `createrepo-c` recipe.
- `rpmresolve`: No longer needed with the move to RPM 4 as RPM itself is used instead.
- `gstreamer`: Removed the GStreamer Git version recipes as they have been stale. `1.10.` x recipes are still present.
- `alsa-conf-base`: Merged into `alsa-conf` since `libasound` depended on both. Essentially, no way existed to install only one of these.
- `tremor`: Moved to `meta-multimedia`. Fixed-integer Vorbis decoding is not needed by current hardware. Thus, GStreamer\'s ivorbis plugin has been disabled by default eliminating the need for the `tremor` recipe in `OpenEmbedded-Core (OE-Core)`{.interpreted-text role="term"}.
- `gummiboot`: Replaced by `systemd-boot`.

# Wic Changes {#migration-2.3-wic-changes}

The following changes have been made to Wic:

::: note
::: title
Note
:::

For more information on Wic, see the \"`dev-manual/wic:creating partitioned images using wic`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.
:::

- *Default Output Directory Changed:* Wic\'s default output directory is now the current directory by default instead of the unusual `/var/tmp/wic`.

  The `-o` and `--outdir` options remain unchanged and are used to specify your preferred output directory if you do not want to use the default directory.
- *fsimage Plug-in Removed:* The Wic fsimage plugin has been removed as it duplicates functionality of the rawcopy plugin.

# QA Changes {#migration-2.3-qa-changes}

The following QA checks have changed:

- `unsafe-references-in-binaries`: The `unsafe-references-in-binaries` QA check, which was disabled by default, has now been removed. This check was intended to detect binaries in `/bin` that link to libraries in `/usr/lib` and have the case where the user has `/usr` on a separate filesystem to `/`.

  The removed QA check was buggy. Additionally, `/usr` residing on a separate partition from `/` is now a rare configuration. Consequently, `unsafe-references-in-binaries` was removed.
- `file-rdeps`: The `file-rdeps` QA check is now an error by default instead of a warning. Because it is an error instead of a warning, you need to address missing runtime dependencies.

  For additional information, see the `ref-classes-insane`{.interpreted-text role="ref"} class and the \"`ref-manual/qa-checks:errors and warnings`{.interpreted-text role="ref"}\" section.

# Miscellaneous Changes {#migration-2.3-miscellaneous-changes}

The following miscellaneous changes have occurred:

- In this release, a number of recipes have been changed to ignore the `largefile` `DISTRO_FEATURES`{.interpreted-text role="term"} item, enabling large file support unconditionally. This feature has always been enabled by default. Disabling the feature has not been widely tested.

  ::: note
  ::: title
  Note
  :::

  Future releases of the Yocto Project will remove entirely the ability to disable the largefile feature, which would make it unconditionally enabled everywhere.
  :::
- If the `DISTRO_VERSION`{.interpreted-text role="term"} value contains the value of the `DATE`{.interpreted-text role="term"} variable, which is the default between Poky releases, the `DATE`{.interpreted-text role="term"} value is explicitly excluded from `/etc/issue` and `/etc/issue.net`, which is displayed at the login prompt, in order to avoid conflicts with Multilib enabled. Regardless, the `DATE`{.interpreted-text role="term"} value is inaccurate if the `base-files` recipe is restored from shared state (sstate) rather than rebuilt.

  If you need the build date recorded in `/etc/issue*` or anywhere else in your image, a better method is to define a post-processing function to do it and have the function called from `ROOTFS_POSTPROCESS_COMMAND`{.interpreted-text role="term"}. Doing so ensures the value is always up-to-date with the created image.
- Dropbear\'s `init` script now disables DSA host keys by default. This change is in line with the systemd service file, which supports RSA keys only, and with recent versions of OpenSSH, which deprecates DSA host keys.
- The `ref-classes-buildhistory`{.interpreted-text role="ref"} class now correctly uses tabs as separators between all columns in `installed-package-sizes.txt` in order to aid import into other tools.
- The `USE_LDCONFIG` variable has been replaced with the \"ldconfig\" `DISTRO_FEATURES`{.interpreted-text role="term"} feature. Distributions that previously set:

  ```
  USE_LDCONFIG = "0"
  ```

  should now instead use the following:

  ```
  DISTRO_FEATURES_BACKFILL_CONSIDERED_append = " ldconfig"
  ```
- The default value of `COPYLEFT_LICENSE_INCLUDE`{.interpreted-text role="term"} now includes all versions of AGPL licenses in addition to GPL and LGPL.

  ::: note
  ::: title
  Note
  :::

  The default list is not intended to be guaranteed as a complete safe list. You should seek legal advice based on what you are distributing if you are unsure.
  :::
- Kernel module packages are now suffixed with the kernel version in order to allow module packages from multiple kernel versions to co-exist on a target system. If you wish to return to the previous naming scheme that does not include the version suffix, use the following:

  ```
  KERNEL_MODULE_PACKAGE_SUFFIX = ""
  ```
- Removal of `libtool` `*.la` files is now enabled by default. The `*.la` files are not actually needed on Linux and relocating them is an unnecessary burden.

  If you need to preserve these `.la` files (e.g. in a custom distribution), you must change `INHERIT_DISTRO`{.interpreted-text role="term"} such that \"`ref-classes-remove-libtool`{.interpreted-text role="ref"}\" is not included in the value.
- Extensible SDKs built for GCC 5+ now refuse to install on a distribution where the host GCC version is 4.8 or 4.9. This change resulted from the fact that the installation is known to fail due to the way the `uninative` shared state (sstate) package is built. See the `ref-classes-uninative`{.interpreted-text role="ref"} class for additional information.
- All `ref-classes-native`{.interpreted-text role="ref"} and `ref-classes-nativesdk`{.interpreted-text role="ref"} recipes now use a separate `DISTRO_FEATURES`{.interpreted-text role="term"} value instead of sharing the value used by recipes for the target, in order to avoid unnecessary rebuilds.

  The `DISTRO_FEATURES`{.interpreted-text role="term"} for `ref-classes-native`{.interpreted-text role="ref"} recipes is `DISTRO_FEATURES_NATIVE`{.interpreted-text role="term"} added to an intersection of `DISTRO_FEATURES`{.interpreted-text role="term"} and `DISTRO_FEATURES_FILTER_NATIVE`{.interpreted-text role="term"}.

  For `ref-classes-nativesdk`{.interpreted-text role="ref"} recipes, the corresponding variables are `DISTRO_FEATURES_NATIVESDK`{.interpreted-text role="term"} and `DISTRO_FEATURES_FILTER_NATIVESDK`{.interpreted-text role="term"}.
- The `FILESDIR` variable, which was previously deprecated and rarely used, has now been removed. You should change any recipes that set `FILESDIR` to set `FILESPATH`{.interpreted-text role="term"} instead.
- The `MULTIMACH_HOST_SYS` variable has been removed as it is no longer needed with recipe-specific sysroots.
