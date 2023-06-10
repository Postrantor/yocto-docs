---
tip: translate by openai@2023-06-07 22:31:33
...
---
title: Introduction
-------------------

This guide provides a list of the backwards-incompatible changes you might need to adapt to in your existing Yocto Project configuration when upgrading to a new release.

> 这本指南提供了一份列表，当您升级到新版本时，您可能需要在现有的 Yocto Project 配置中适应的向后不兼容的变化。

If you are upgrading over multiple releases, you will need to follow the sections from the version following the one you were previously using up to the new version you are upgrading to.

> 如果你要进行多个版本的升级，你需要按照从你之前使用的版本之后的版本，一直到你要升级到的新版本的章节来操作。

# General Migration Considerations

Some considerations are not tied to a specific Yocto Project release. This section presents information you should consider when migrating to any new Yocto Project release.

> 一些考虑不局限于特定的 Yocto 项目发行版。本节介绍当您迁移到任何新的 Yocto 项目发行版时应考虑的信息。

- *Dealing with Customized Recipes*:

  Issues could arise if you take older recipes that contain customizations and simply copy them forward expecting them to work after you migrate to new Yocto Project metadata. For example, suppose you have a recipe in your layer that is a customized version of a core recipe copied from the earlier release, rather than through the use of an append file. When you migrate to a newer version of Yocto Project, the metadata (e.g. perhaps an include file used by the recipe) could have changed in a way that would break the build. Say, for example, a function is removed from an include file and the customized recipe tries to call that function.

> 如果您复制以前版本中包含自定义的旧配方并期望在迁移到新的 Yocto Project 元数据后可以正常工作，则可能会出现问题。例如，假设您的层中有一个配方，它是从先前版本中复制的核心配方的定制版本，而不是通过使用附加文件。当您迁移到新版本的 Yocto Project 时，元数据（例如，配方使用的 include 文件）可能已经以破坏构建的方式更改。例如，从 include 文件中删除了一个函数，定制配方尝试调用该函数。

You could \"forward-port\" all your customizations in your recipe so that everything works for the new release. However, this is not the optimal solution as you would have to repeat this process with each new release if changes occur that give rise to problems.

> 你可以在新版本中“转发端口”你的自定义配方，以便一切正常运行。但是，这不是最佳解决方案，因为如果出现可能导致问题的变化，你每次新发行版本都需要重复此过程。

The better solution (where practical) is to use append files (`*.bbappend`) to capture any customizations you want to make to a recipe. Doing so isolates your changes from the main recipe, making them much more manageable. However, sometimes it is not practical to use an append file. A good example of this is when introducing a newer or older version of a recipe in another layer.

> 最佳解决方案（在实际情况下）是使用附加文件（`*.bbappend`）来捕获您想对配方做出的任何自定义。这样做可以将您的更改与主配方隔离开来，使其更易于管理。但是，有时使用附加文件不太实用。一个很好的例子就是在另一个层中引入一个更新或更旧的配方。

- *Updating Append Files*:

  Since append (`.bbappend`) files generally only contain your customizations, they often do not need to be adjusted for new releases. However, if the append file is specific to a particular version of the recipe (i.e. its name does not use the % wildcard) and the version of the recipe to which it is appending has changed, then you will at a minimum need to rename the append file to match the name of the recipe file. A mismatch between an append file and its corresponding recipe file (`.bb`) will trigger an error during parsing.

> 由于 `.bbappend` 文件通常只包含您的自定义内容，因此通常不需要对新版本进行调整。但是，如果附加文件专门针对某个版本的配方（即其名称不使用 % 通配符），而且配方的版本已经更改，那么您至少需要将附加文件重命名为与配方文件（`.bb`）相匹配。附加文件与其相应的配方文件（`.bb`）之间的不匹配将在解析期间触发错误。

Depending on the type of customization the append file applies, other incompatibilities might occur when you upgrade. For example, if your append file applies a patch and the recipe to which it is appending is updated to a newer version, the patch might no longer apply. If this is the case and assuming the patch is still needed, you must modify the patch file so that it does apply.

> 取决于追加文件所应用的定制类型，当您升级时可能会出现其他不兼容情况。例如，如果您的追加文件应用了一个补丁，而要追加的配方已更新为较新的版本，则此补丁可能不再适用。如果是这种情况，并且假设补丁仍然需要，则必须修改补丁文件，以使其适用。

> ::: tip
> ::: title
> Tip
> :::
>
> You can list all append files used in your configuration by running:
>
>> bitbake-layers show-appends
>> :::
>>

::: {#migration-general-buildhistory}

- *Checking Image / SDK Changes*:

  > The `ref-classes-buildhistory`{.interpreted-text role="ref"} class can be used if you wish to check the impact of changes to images / SDKs across the migration (e.g. added/removed packages, added/removed files, size changes etc.). To do this, follow these steps:
  >

> 使用 `ref-classes-buildhistory`{.interpreted-text role="ref"}类，可以检查迁移中对图像/SDK 的更改所带来的影响（例如，添加/删除的包、添加/删除的文件、大小更改等）。要做到这一点，请按照以下步骤操作：
>
> 1. Enable `ref-classes-buildhistory`{.interpreted-text role="ref"} before the migration
> 2. Run a pre-migration build

> 3. Capture the `ref-classes-buildhistory`{.interpreted-text role="ref"} output (as specified by `BUILDHISTORY_DIR`{.interpreted-text role="term"}) and ensure it is preserved for subsequent builds. How you would do this depends on how you are running your builds - if you are doing this all on one workstation in the same `Build Directory`{.interpreted-text role="term"} you may not need to do anything other than not deleting the `ref-classes-buildhistory`{.interpreted-text role="ref"} output directory. For builds in a pipeline it may be more complicated.

> 捕获 `ref-classes-buildhistory`{.interpreted-text role="ref"}输出（按照 `BUILDHISTORY_DIR`{.interpreted-text role="term"}指定），并确保保留以备后续构建。您将如何做到这一点取决于您如何运行构建 - 如果您在同一个 `Build Directory`{.interpreted-text role="term"}中在一台工作站上完成所有构建，则除了不删除 `ref-classes-buildhistory`{.interpreted-text role="ref"}输出目录之外，可能不需要做任何事情。对于管道中的构建，可能更复杂。

> 4. Set a tag in the `ref-classes-buildhistory`{.interpreted-text role="ref"} output (which is a git repository) before migration, to make the commit from the pre-migration build easy to find as you may end up running multiple builds during the migration.

> 设置 `ref-classes-buildhistory`{.interpreted-text role="ref"}输出（这是一个 git 存储库）中的标签，在迁移之前，以便在迁移期间运行多个构建时，可以轻松找到迁移之前构建的提交。
> 5. Perform the migration
> 6. Run a build

> 7. Check the output changes between the previously set tag and HEAD in the `ref-classes-buildhistory`{.interpreted-text role="ref"} output using `git diff` or `buildhistory-diff`.

> 检查在 `ref-classes-buildhistory` 输出中，之前设置的标签和 HEAD 之间的输出变化，使用 `git diff` 或 `buildhistory-diff`。

> For more information on using `ref-classes-buildhistory`{.interpreted-text role="ref"}, see `dev-manual/build-quality:maintaining build output quality`{.interpreted-text role="ref"}.

> 对于使用 ref-classes-buildhistory 的更多信息，请参见 dev-manual/build-quality：维护构建输出质量。
> :::
