---
tip: translate by openai@2023-06-07 22:30:53
...
---
subtitle: Migration notes for 4.3 (nanbield)
title: Release 4.3 (nanbield)
-----------------------------

This section provides migration information for moving to the Yocto Project 4.3 Release (codename \"nanbield\") from the prior release.

# Supported kernel versions {#migration-4.3-supported-kernel-versions}

The `OLDEST_KERNEL`{.interpreted-text role="term"} setting has been changed to \"5.15\" in this release, meaning that out the box, older kernels are not supported. There were two reasons for this. Firstly it allows glibc optimisations that improve the performance of the system by removing compatibility code and using modern kernel APIs exclusively. The second issue was this allows 64 bit time support even on 32 bit platforms and resolves Y2038 issues.

> 在此版本中，`OLDEST_KERNEL`{.interpreted-text role="term"}设置已更改为“5.15”，这意味着除了盒子外，不再支持较旧的内核。有两个原因。首先，它允许 glibc 优化，通过删除兼容性代码并专门使用现代内核 API 来提高系统性能。第二个问题是，这允许即使在 32 位平台上也支持 64 位时间，并解决 Y2038 问题。

It is still possible to override this value and build for older kernels, this is just no longer the default supported configuration. This setting does not affect which kernel versions SDKs will run against and does not affect which versions of the kernel can be used to run builds.

> 依然可以覆盖这个值并且为旧内核构建，但这不再是默认支持的配置。 这个设置不会影响 SDK 将运行的内核版本，也不会影响可用于运行构建的内核版本。

# Supported distributions {#migration-4.3-supported-distributions}

This release supports running BitBake on new GNU/Linux distributions:

On the other hand, some earlier distributions are no longer supported:

See `all supported distributions <system-requirements-supported-distros>`{.interpreted-text role="ref"}.

# Go language changes {#migration-4.3-go-changes}

- Support for the Glide package manager has been removed, as `go mod` has become the standard.

# Recipe changes {#migration-4.3-recipe-changes}

- Runtime testing of ptest now fails if no test results are returned by any given ptest.

# Class changes {#migration-4.3-class-changes}

- The `perl-version` class no longer provides the `PERLVERSION` and `PERLARCH` variables as there were no users in any core layer. The functions for this functionality are still available.

> 現在，'perl-version' 類別不再提供 'PERLVERSION' 和 'PERLARCH' 變數，因為核心層沒有用戶。該功能的函數仍然可用。

# Removed variables {#migration-4.3-removed-variables}

The following variables have been removed:

- `PERLARCH`
- `PERLVERSION`

# Removed recipes {#migration-4.3-removed-recipes}

The following recipes have been removed in this release:

- `glide`, as explained in `migration-4.3-go-changes`{.interpreted-text role="ref"}.

# Removed classes {#migration-4.3-removed-classes}

The following classes have been removed in this release:

# Miscellaneous changes {#migration-4.3-misc-changes}

- The `-crosssdk` suffix and any `MLPREFIX`{.interpreted-text role="term"} were removed from `virtual/XXX` provider/dependencies where a `PREFIX` was used as well, as we don\'t need both and it made automated dependency rewriting unnecessarily complex. In general this only affects internal toolchain dependencies so isn\'t end user visible.

> - 在使用 `PREFIX` 的 `virtual/XXX` 提供者/依赖项中，已移除 `-crosssdk` 后缀和任何 `MLPREFIX`，因为我们不需要两者，而且这会使自动依赖性重写变得复杂。通常，这只影响内部工具链依赖项，因此不会对最终用户可见。
