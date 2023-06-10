---
tip: translate by openai@2023-06-07 20:48:53
...
---
title: Introduction
-------------------

Toaster is a web interface to the Yocto Project\'s `OpenEmbedded Build System`{.interpreted-text role="term"}. The interface enables you to configure and run your builds. Information about builds is collected and stored in a database. You can use Toaster to configure and start builds on multiple remote build servers.

> Toaster 是 Yocto 项目的开放嵌入式构建系统的 Web 界面。该界面使您可以配置和运行构建。有关构建的信息将收集并存储在数据库中。您可以使用 Toaster 在多个远程构建服务器上配置和启动构建。

# Toaster Features

Toaster allows you to configure and run builds, and it provides extensive information about the build process.

> 投票器允许您配置和运行构建，并提供有关构建过程的详细信息。

- *Configure and Run Builds:* You can use the Toaster web interface to configure and start your builds. Builds started using the Toaster web interface are organized into projects. When you create a project, you are asked to select a release, or version of the build system you want to use for the project builds. As shipped, Toaster supports Yocto Project releases 1.8 and beyond. With the Toaster web interface, you can:

> 使用 Toaster 网页界面可以配置和启动构建。使用 Toaster 网页界面启动的构建被组织到项目中。创建项目时，您被要求选择要用于项目构建的构建系统版本。按照出货，Toaster 支持 Yocto Project 1.8 及更高版本。使用 Toaster 网页界面，您可以：

```
-   Browse layers listed in the various `layer sources <toaster-manual/reference:layer source>`{.interpreted-text role="ref"} that are available in your project (e.g. the OpenEmbedded Layer Index at :oe_layerindex:[/]{.title-ref}).
-   Browse images, recipes, and machines provided by those layers.
-   Import your own layers for building.
-   Add and remove layers from your configuration.
-   Set configuration variables.
-   Select a target or multiple targets to build.
-   Start your builds.

Toaster also allows you to configure and run your builds from the command line, and switch between the command line and the web interface at any time. Builds started from the command line appear within a special Toaster project called \"Command line builds\".
```

- *Information About the Build Process:* Toaster also records extensive information about your builds. Toaster collects data for builds you start from the web interface and from the command line as long as Toaster is running.

> *有关构建过程的信息：* Toaster 还记录有关您的构建的详细信息。只要 Toaster 正在运行，就可以从 Web 界面和命令行启动构建，Toaster 会收集构建的数据。

```
::: note
::: title
Note
:::

You must start Toaster before the build or it will not collect build data.
:::

With Toaster you can:

-   See what was built (recipes and packages) and what packages were installed into your final image.
-   Browse the directory structure of your image.
-   See the value of all variables in your build configuration, and which files set each value.
-   Examine error, warning, and trace messages to aid in debugging.
-   See information about the BitBake tasks executed and reused during your build, including those that used shared state.
-   See dependency relationships between recipes, packages, and tasks.
-   See performance information such as build time, task time, CPU usage, and disk I/O.
```

For an overview of Toaster, see this [introduction video](https://youtu.be/BlXdOYLgPxA).

> 欲了解 Toaster 的概览，请参阅此[介绍视频](https://youtu.be/BlXdOYLgPxA)。

# Installation Options

You can set Toaster up to run as a local instance or as a shared hosted service.

> 你可以设置 Toaster 以本地实例或共享托管服务的方式运行。

When Toaster is set up as a local instance, all the components reside on a single build host. Fundamentally, a local instance of Toaster is suited for a single user developing on a single build host.

> 当 Toaster 被设置为本地实例时，所有组件都位于单个构建主机上。从根本上讲，Toaster 的本地实例适合于在单个构建主机上进行开发的单个用户。

![image](figures/simple-configuration.png){.align-center width="70.0%"}

Toaster as a hosted service is suited for multiple users developing across several build hosts. When Toaster is set up as a hosted service, its components can be spread across several machines:

> 当将 Toaster 设置为托管服务时，其组件可以分布在多台机器上，因此它适合多用户在多个构建主机上开发。

![image](figures/hosted-service.png){.align-center width="50.0%"}
