---
tip: translate by openai@2023-06-07 22:55:31
...
---
title: Variable Context
-----------------------

While you can use most variables in almost any context such as `.conf`, `.bbclass`, `.inc`, and `.bb` files, some variables are often associated with a particular locality or context. This chapter describes some common associations.

> 虽然您可以在几乎任何上下文中使用大多数变量，例如 `.conf`，`.bbclass`，`.inc` 和 `.bb` 文件，但某些变量通常与特定的局部或上下文相关联。本章介绍一些常见的关联。

# Configuration {#ref-varlocality-configuration}

The following subsections provide lists of variables whose context is configuration: distribution, machine, and local.

## Distribution (Distro) {#ref-varlocality-config-distro}

This section lists variables whose configuration context is the distribution, or distro.

- `DISTRO`{.interpreted-text role="term"}
- `DISTRO_NAME`{.interpreted-text role="term"}
- `DISTRO_VERSION`{.interpreted-text role="term"}
- `MAINTAINER`{.interpreted-text role="term"}
- `PACKAGE_CLASSES`{.interpreted-text role="term"}
- `TARGET_OS`{.interpreted-text role="term"}
- `TARGET_FPU`{.interpreted-text role="term"}
- `TCMODE`{.interpreted-text role="term"}
- `TCLIBC`{.interpreted-text role="term"}

## Machine {#ref-varlocality-config-machine}

This section lists variables whose configuration context is the machine.

- `TARGET_ARCH`{.interpreted-text role="term"}
- `SERIAL_CONSOLES`{.interpreted-text role="term"}
- `PACKAGE_EXTRA_ARCHS`{.interpreted-text role="term"}
- `IMAGE_FSTYPES`{.interpreted-text role="term"}
- `MACHINE_FEATURES`{.interpreted-text role="term"}
- `MACHINE_EXTRA_RDEPENDS`{.interpreted-text role="term"}
- `MACHINE_EXTRA_RRECOMMENDS`{.interpreted-text role="term"}
- `MACHINE_ESSENTIAL_EXTRA_RDEPENDS`{.interpreted-text role="term"}
- `MACHINE_ESSENTIAL_EXTRA_RRECOMMENDS`{.interpreted-text role="term"}

## Local {#ref-varlocality-config-local}

This section lists variables whose configuration context is the local configuration through the `local.conf` file.

- `DISTRO`{.interpreted-text role="term"}
- `MACHINE`{.interpreted-text role="term"}
- `DL_DIR`{.interpreted-text role="term"}
- `BBFILES`{.interpreted-text role="term"}
- `EXTRA_IMAGE_FEATURES`{.interpreted-text role="term"}
- `PACKAGE_CLASSES`{.interpreted-text role="term"}
- `BB_NUMBER_THREADS`{.interpreted-text role="term"}
- `BBINCLUDELOGS`{.interpreted-text role="term"}
- `ENABLE_BINARY_LOCALE_GENERATION`{.interpreted-text role="term"}

# Recipes {#ref-varlocality-recipes}

The following subsections provide lists of variables whose context is recipes: required, dependencies, path, and extra build information.

## Required {#ref-varlocality-recipe-required}

This section lists variables that are required for recipes.

- `LICENSE`{.interpreted-text role="term"}
- `LIC_FILES_CHKSUM`{.interpreted-text role="term"}
- `SRC_URI`{.interpreted-text role="term"} \-\-- used in recipes that fetch local or remote files.

## Dependencies {#ref-varlocality-recipe-dependencies}

This section lists variables that define recipe dependencies.

- `DEPENDS`{.interpreted-text role="term"}
- `RDEPENDS`{.interpreted-text role="term"}
- `RRECOMMENDS`{.interpreted-text role="term"}
- `RCONFLICTS`{.interpreted-text role="term"}
- `RREPLACES`{.interpreted-text role="term"}

## Paths {#ref-varlocality-recipe-paths}

This section lists variables that define recipe paths.

- `WORKDIR`{.interpreted-text role="term"}
- `S`{.interpreted-text role="term"}
- `FILES`{.interpreted-text role="term"}

## Extra Build Information {#ref-varlocality-recipe-build}

This section lists variables that define extra build information for recipes.

- `DEFAULT_PREFERENCE`{.interpreted-text role="term"}
- `EXTRA_OECMAKE`{.interpreted-text role="term"}
- `EXTRA_OECONF`{.interpreted-text role="term"}
- `EXTRA_OEMAKE`{.interpreted-text role="term"}
- `PACKAGECONFIG_CONFARGS`{.interpreted-text role="term"}
- `PACKAGES`{.interpreted-text role="term"}
