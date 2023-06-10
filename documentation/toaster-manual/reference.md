---
tip: translate by openai@2023-06-07 20:49:55
...
---
title: Concepts and Reference
-----------------------------

In order to configure and use Toaster, you should understand some concepts and have some basic command reference material available. This final chapter provides conceptual information on layer sources, releases, and JSON configuration files. Also provided is a quick look at some useful `manage.py` commands that are Toaster-specific. Information on `manage.py` commands is available across the Web and this manual by no means attempts to provide a command comprehensive reference.

> 为了配置和使用烤面包机，您应该了解一些概念，并准备一些基本的命令参考资料。本章最后提供了有关图层源、发行版和 JSON 配置文件的概念性信息。还提供了一些有用的 Toaster 特定的 `manage.py` 命令的快速查看。关于 `manage.py` 命令的信息可以在 Web 上和本手册中找到，本手册无意提供全面的参考。

# Layer Source

In general, a \"layer source\" is a source of information about existing layers. In particular, we are concerned with layers that you can use with the Yocto Project and Toaster. This chapter describes a particular type of layer source called a \"layer index.\"

> 一般而言，“图层源”是有关现有图层的信息源。特别是，我们关注可以使用 Yocto 项目和 Toaster 的图层。本章介绍了一种称为“图层索引”的特定类型的图层源。

A layer index is a web application that contains information about a set of custom layers. A good example of an existing layer index is the OpenEmbedded Layer Index. A public instance of this layer index exists at :oe_layerindex:[/]. You can find the code for this layer index\'s web application at :yocto_[git:%60/layerindex-web/](git:%60/layerindex-web/)\`.

> 一个图层索引是一个包含有关自定义图层集合的信息的 Web 应用程序。一个现有图层索引的好例子是 OpenEmbedded 图层索引。此图层索引的公共实例位于：oe_layerindex：[/]。您可以在：yocto_<git：%60/layerindex-web/>\`中找到此图层索引的 Web 应用程序的代码。

When you tie a layer source into Toaster, it can query the layer source through a `REST <Representational_state_transfer>` API, store the information about the layers in the Toaster database, and then show the information to users. Users are then able to view that information and build layers from Toaster itself without having to clone or edit the BitBake layers configuration file `bblayers.conf`.

> 当您将图层源连接到烤面包机时，它可以通过 `REST <Representational_state_transfer>` API 查询图层源，将有关图层的信息存储在烤面包机数据库中，然后将该信息显示给用户。然后，用户可以查看该信息并从烤面包机本身构建图层，而无需克隆或编辑 BitBake 图层配置文件 `bblayers.conf`。

Tying a layer source into Toaster is convenient when you have many custom layers that need to be built on a regular basis by a community of developers. In fact, Toaster comes pre-configured with the OpenEmbedded Metadata Index.

> 绑定一个层源到烤面包机很方便，当您有许多自定义层需要由一群开发者定期构建时。事实上，烤面包机预先配置了 OpenEmbedded 元数据索引。

::: note
::: title
Note
:::

You do not have to use a layer source to use Toaster. Tying into a layer source is optional.

> 你不必使用图层源来使用 Toaster。连接到图层源是可选的。
> :::

## Setting Up and Using a Layer Source

To use your own layer source, you need to set up the layer source and then tie it into Toaster. This section describes how to tie into a layer index in a manner similar to the way Toaster ties into the OpenEmbedded Metadata Index.

> 要使用自己的图层源，您需要设置图层源，然后将其与 Toaster 相连。本节介绍如何以类似于 Toaster 与 OpenEmbedded Metadata Index 相连的方式与图层索引相连。

### Understanding Your Layers

The obvious first step for using a layer index is to have several custom layers that developers build and access using the Yocto Project on a regular basis. This set of layers needs to exist and you need to be familiar with where they reside. You will need that information when you set up the code for the web application that \"hooks\" into your set of layers.

> 首先使用层索引的明显步骤是，开发人员定期使用 Yocto Project 构建和访问多个自定义层。这组层必须存在，并且您需要熟悉它们的位置。在设置与您的层组相关联的 Web 应用程序的代码时，您将需要此信息。

For general information on layers, see the \"`overview-manual/yp-intro:the yocto project layer model`\" section in the Yocto Project Development Tasks Manual.

> 对于层的一般信息，请参阅 Yocto 项目概述与概念手册中的“概览手册/yp-intro：Yocto 项目层模型”部分。有关如何创建层的信息，请参阅 Yocto 项目开发任务手册中的“dev-manual/layers：理解和创建层”部分。

### Configuring Toaster to Hook Into Your Layer Index

If you want Toaster to use your layer index, you must host the web application in a server to which Toaster can connect. You also need to give Toaster the information about your layer index. In other words, you have to configure Toaster to use your layer index. This section describes two methods by which you can configure and use your layer index.

> 如果你想让 Toaster 使用你的图层索引，你必须将 Web 应用程序托管在一台 Toaster 可以连接的服务器上。你还需要给 Toaster 提供有关你图层索引的信息。换句话说，你必须配置 Toaster 来使用你的图层索引。本节描述了两种方法可以配置和使用你的图层索引。

In the previous section, the code for the OpenEmbedded Metadata Index (i.e. :oe_layerindex:[/]) was referenced. You can use this code, which is at :yocto_[git:%60/layerindex-web/](git:%60/layerindex-web/)\`, as a base to create your own layer index.

> 在上一节中，引用了 OpenEmbedded 元数据索引(即：oe_layerindex：[/])的代码。您可以使用位于 yocto_<git：%60/layerindex-web/> 的这段代码作为基础来创建自己的层索引。

#### Use the Administration Interface

Access the administration interface through a browser by entering the URL of your Toaster instance and adding \"`/admin`\" to the end of the URL. As an example, if you are running Toaster locally, use the following URL:

> 通过在 URL 末尾添加 "/admin"，可以通过浏览器访问管理界面。例如，如果您在本地运行 Toaster，请使用以下 URL：

```
http://127.0.0.1:8000/admin
```

The administration interface has a \"Layer sources\" section that includes an \"Add layer source\" button. Click that button and provide the required information. Make sure you select \"layerindex\" as the layer source type.

> 管理界面有一个“图层来源”部分，包括一个“添加图层来源”按钮。点击该按钮，提供所需的信息。确保您将“图层索引”选择为图层来源类型。

#### Use the Fixture Feature

The Django fixture feature overrides the default layer server when you use it to specify a custom URL. To use the fixture feature, create (or edit) the file `bitbake/lib/toaster.orm/fixtures/custom.xml`, and then set the following Toaster setting to your custom URL:

> 当您使用它来指定默认层服务器时，Django 夹具功能将覆盖默认层服务器。要使用夹具功能，请创建(或编辑)文件 `bitbake / lib / toaster.orm / fixtures / custom.xml`，然后将以下 Toaster 设置设置为您的自定义 URL：

```xml
<?xml version="1.0" ?>
<django-objects version="1.0">
   <object model="orm.toastersetting" pk="100">
      <field name="name" type="CharField">CUSTOM_LAYERINDEX_SERVER</field>
      <field name="value" type="CharField">https://layers.my_organization.org/layerindex/branch/master/layers/</field>
   </object>
<django-objects>
```

When you start Toaster for the first time, or if you delete the file `toaster.sqlite` and restart, the database will populate cleanly from this layer index server.

> 当您第一次启动 Toaster 或者删除文件'toaster.sqlite'并重新启动时，数据库将从此层索引服务器清洁地填充。

Once the information has been updated, verify the new layer information is available by using the Toaster web interface. To do that, visit the \"All compatible layers\" page inside a Toaster project. The layers from your layer source should be listed there.

> 一旦信息更新完毕，可以通过使用 Toaster 网页界面来验证新图层信息是否可用。要做到这一点，请访问 Toaster 项目中的“所有兼容图层”页面。您的图层源应该会在那里列出。

If you change the information in your layer index server, refresh the Toaster database by running the following command:

> 如果您更改了图层索引服务器中的信息，请通过运行以下命令来刷新 Toaster 数据库：

```shell
$ bitbake/lib/toaster/manage.py lsupdates
```

If Toaster can reach the API URL, you should see a message telling you that Toaster is updating the layer source information.

> 如果 Toaster 能够访问 API URL，您应该会看到一条消息，提示 Toaster 正在更新图层源信息。

# Releases

When you create a Toaster project using the web interface, you are asked to choose a \"Release.\" In the context of Toaster, the term \"Release\" refers to a set of layers and a BitBake version the OpenEmbedded build system uses to build something. As shipped, Toaster is pre-configured with releases that correspond to Yocto Project release branches. However, you can modify, delete, and create new releases according to your needs. This section provides some background information on releases.

> 当您使用 Web 界面创建 Toaster 项目时，您会被要求选择“发布”。在 Toaster 的上下文中，术语“发布”指的是 OpenEmbedded 构建系统用于构建某物的一组图层和 BitBake 版本。按照出货，Toaster 预先配置了与 Yocto 项目发布分支对应的发布版本。但是，您可以根据您的需要修改、删除和创建新的发布版本。本节提供了有关发布版本的一些背景信息。

## Pre-Configured Releases

As shipped, Toaster is configured to use a specific set of releases. Of course, you can always configure Toaster to use any release. For example, you might want your project to build against a specific commit of any of the \"out-of-the-box\" releases. Or, you might want your project to build against different revisions of OpenEmbedded and BitBake.

> 原装时，Toaster 配置为使用特定的发行版本。当然，您总是可以配置 Toaster 以使用任何发行版本。例如，您可能希望您的项目建立在任何“现成”发行版本的特定提交上。或者，您可能希望您的项目建立在不同的 OpenEmbedded 和 BitBake 版本上。

As shipped, Toaster is configured to work with the following releases:

> 离开工厂时，烤面包机配置好可以使用以下版本：

- *Yocto Project &DISTRO; \"&DISTRO_NAME;\" or OpenEmbedded \"&DISTRO_NAME;\":* This release causes your Toaster projects to build against the head of the &DISTRO_NAME_NO_CAP; branch at :yocto_[git:%60/poky/log/?h=&DISTRO_NAME_NO_CAP](git:%60/poky/log/?h=&DISTRO_NAME_NO_CAP);\` or :oe_[git:%60/openembedded-core/commit/?h=&DISTRO_NAME_NO_CAP](git:%60/openembedded-core/commit/?h=&DISTRO_NAME_NO_CAP);\`.

> *Yocto 项目&DISTRO;“&DISTRO_NAME;”或 OpenEmbedded“&DISTRO_NAME;”：此版本使您的 Toaster 项目可以与&DISTRO_NAME_NO_CAP;分支的头部构建，位于：yocto_[git:%60/poky/log/?h=&DISTRO_NAME_NO_CAP](git:%60/poky/log/?h=&DISTRO_NAME_NO_CAP);或:oe_[git:%60/openembedded-core/commit/?h=&DISTRO_NAME_NO_CAP](git:%60/openembedded-core/commit/?h=&DISTRO_NAME_NO_CAP);。

- *Yocto Project \"Master\" or OpenEmbedded \"Master\":* This release causes your Toaster Projects to build against the head of the master branch, which is where active development takes place, at :yocto_[git:%60/poky/log/](git:%60/poky/log/)[ or :oe_git:]/openembedded-core/log/\`.

> "Yocto Project" 主分支或 OpenEmbedded 主分支：此发行版会使您的 Toaster 项目基于主分支头构建，该主分支是活跃开发发生的地方，地址为：yocto_[git:/poky/log/](git:/poky/log/)[或:oe_git:]/openembedded-core/log/\`。

- *Local Yocto Project or Local OpenEmbedded:* This release causes your Toaster Projects to build against the head of the `poky` or `openembedded-core` clone you have local to the machine running Toaster.

> 本地 Yocto 项目或本地 OpenEmbedded：此发行版使您的 Toaster 项目构建基于您在运行 Toaster 的机器上本地克隆的 `poky` 或 `openembedded-core` 头部。

# Configuring Toaster

In order to use Toaster, you must configure the database with the default content. The following subsections describe various aspects of Toaster configuration.

> 为了使用 Toaster，您必须使用默认内容配置数据库。以下子节描述了 Toaster 配置的各个方面。

## Configuring the Workflow

The `bldcontrol/management/commands/checksettings.py` file controls workflow configuration. Here is the process to initially populate this database.

> `bldcontrol/management/commands/checksettings.py` 文件控制工作流配置。以下是最初填充此数据库的过程。

1. The default project settings are set from `orm/fixtures/settings.xml`.

> 默认项目设置从 `orm/fixtures/settings.xml` 设置。

2. The default project distro and layers are added from `orm/fixtures/poky.xml` if poky is installed. If poky is not installed, they are added from `orm/fixtures/oe-core.xml`.

> 默认项目发行版和层是从 `orm/fixtures/poky.xml` 中添加的，如果安装了 poky。如果没有安装 poky，则从 `orm/fixtures/oe-core.xml` 中添加。

3. If the `orm/fixtures/custom.xml` file exists, then its values are added.

> 如果 `orm/fixtures/custom.xml` 文件存在，那么它的值将被添加。

4. The layer index is then scanned and added to the database.

> 然后扫描层索引，并将其添加到数据库中。

Once these steps complete, Toaster is set up and ready to use.

> 一旦完成这些步骤，烤箱就可以使用了。

## Customizing Pre-Set Data

The pre-set data for Toaster is easily customizable. You can create the `orm/fixtures/custom.xml` file to customize the values that go into the database. Customization is additive, and can either extend or completely replace the existing values.

> 预设的 Toaster 数据很容易定制。您可以创建 `orm/fixtures/custom.xml` 文件来定制要插入数据库的值。定制是可以累加的，可以扩展或完全替换现有的值。

You use the `orm/fixtures/custom.xml` file to change the default project settings for the machine, distro, file images, and layers. When creating a new project, you can use the file to define the offered alternate project release selections. For example, you can add one or more additional selections that present custom layer sets or distros, and any other local or proprietary content.

> 你可以使用 `orm/fixtures/custom.xml` 文件来更改机器，发行版，文件镜像和层的默认项目设置。在创建新项目时，可以使用该文件定义提供的其他项目发布选择。例如，你可以添加一个或多个附加选择，提供自定义层集或发行版，以及任何其他本地或专有内容。

Additionally, you can completely disable the content from the `oe-core.xml` and `poky.xml` files by defining the section shown below in the `settings.xml` file. For example, this option is particularly useful if your custom configuration defines fewer releases or layers than the default fixture files.

> 此外，您可以通过在 `settings.xml` 文件中定义下面显示的部分，完全禁用 `oe-core.xml` 和 `poky.xml` 文件中的内容。例如，如果您的自定义配置比默认固件文件定义的发行版或层次结构少，则此选项特别有用。

The following example sets \"name\" to \"CUSTOM_XML_ONLY\" and its value to \"True\".

> 以下示例将“name”设置为“CUSTOM_XML_ONLY”，其值设置为“True”。

```xml
<object model="orm.toastersetting" pk="99">
   <field type="CharField" name="name">CUSTOM_XML_ONLY</field>
   <field type="CharField" name="value">True</field>
</object>
```

## Understanding Fixture File Format

Here is an overview of the file format used by the `oe-core.xml`, `poky.xml`, and `custom.xml` files.

> 以下是 `oe-core.xml`、`poky.xml` 和 `custom.xml` 文件使用的文件格式的概览。

The following subsections describe each of the sections in the fixture files, and outline an example section of the XML code. you can use to help understand this information and create a local `custom.xml` file.

> 以下小节描述了每个固定文件中的部分，并概述了 XML 代码的示例部分。您可以使用此信息来帮助理解此信息并创建本地的“custom.xml”文件。

### Defining the Default Distro and Other Values

This section defines the default distro value for new projects. By default, it reserves the first Toaster Setting record \"1\". The following demonstrates how to set the project default value for `DISTRO`:

> 此部分定义新项目的默认分发值。默认情况下，它保留第一个 Toaster 设置记录“1”。以下演示如何为 `DISTRO` 设置项目默认值：

```xml
<!-- Set the project default value for DISTRO -->
<object model="orm.toastersetting" pk="1">
   <field type="CharField" name="name">DEFCONF_DISTRO</field>
   <field type="CharField" name="value">poky</field>
</object>
```

You can override other default project values by adding additional Toaster Setting sections such as any of the settings coming from the `settings.xml` file. Also, you can add custom values that are included in the BitBake environment. The \"pk\" values must be unique. By convention, values that set default project values have a \"DEFCONF\" prefix.

> 你可以通过添加其他 Toaster 设置部分来覆盖其他默认项目值，例如来自 `settings.xml` 文件的任何设置。此外，您还可以添加包含在 BitBake 环境中的自定义值。“pk”值必须是唯一的。按照惯例，设置默认项目值的值具有“DEFCONF”前缀。

### Defining BitBake Version

The following defines which version of BitBake is used for the following release selection:

> 以下定义使用哪个版本的 BitBake 来进行以下发行版选择：

```xml
<!-- Bitbake versions which correspond to the metadata release -->
<object model="orm.bitbakeversion" pk="1">
   <field type="CharField" name="name">&DISTRO_NAME_NO_CAP;</field>
   <field type="CharField" name="giturl">git://git.yoctoproject.org/poky</field>
   <field type="CharField" name="branch">&DISTRO_NAME_NO_CAP;</field>
   <field type="CharField" name="dirpath">bitbake</field>
</object>
```

### Defining Release

The following defines the releases when you create a new project:

> 以下定义了在创建新项目时的发布版本：

```xml
<!-- Releases available -->
<object model="orm.release" pk="1">
   <field type="CharField" name="name">&DISTRO_NAME_NO_CAP;</field>
   <field type="CharField" name="description">Yocto Project &DISTRO; "&DISTRO_NAME;"</field>
   <field rel="ManyToOneRel" to="orm.bitbakeversion" name="bitbake_version">1</field>
   <field type="CharField" name="branch_name">&DISTRO_NAME_NO_CAP;</field>
   <field type="TextField" name="helptext">Toaster will run your builds using the tip of the <a href="https://git.yoctoproject.org/cgit/cgit.cgi/poky/log/?h=&DISTRO_NAME_NO_CAP;">Yocto Project &DISTRO_NAME; branch</a>.</field>
</object>
```

The \"pk\" value must match the above respective BitBake version record.

> "pk" 值必须与上述相应的 BitBake 版本记录相匹配。

### Defining the Release Default Layer Names

The following defines the default layers for each release:

> 以下定义了每个版本的默认图层：

```xml
<!-- Default project layers for each release -->
<object model="orm.releasedefaultlayer" pk="1">
   <field rel="ManyToOneRel" to="orm.release" name="release">1</field>
   <field type="CharField" name="layer_name">openembedded-core</field>
</object>
```

The \'pk\' values in the example above should start at \"1\" and increment uniquely. You can use the same layer name in multiple releases.

> 在上面的例子中，'pk'值应该从"1"开始，并且唯一地递增。您可以在多个版本中使用相同的图层名称。

### Defining Layer Definitions

Layer definitions are the most complex. The following defines each of the layers, and then defines the exact layer version of the layer used for each respective release. You must have one `orm.layer` entry for each layer. Then, with each entry you need a set of `orm.layer_version` entries that connects the layer with each release that includes the layer. In general all releases include the layer.

> 定义层是最复杂的。下面定义每个层，然后为每个相应的发布定义精确的层版本。您必须为每个层有一个 `orm.layer` 条目。然后，您需要一组 `orm.layer_version` 条目来将层与每个包含该层的发布连接起来。通常，所有发布都包含层。

```xml
<object model="orm.layer" pk="1">
   <field type="CharField" name="name">openembedded-core</field>
   <field type="CharField" name="layer_index_url"></field>
   <field type="CharField" name="vcs_url">git://git.yoctoproject.org/poky</field>
   <field type="CharField" name="vcs_web_url">https://git.yoctoproject.org/cgit/cgit.cgi/poky</field>
   <field type="CharField" name="vcs_web_tree_base_url">https://git.yoctoproject.org/cgit/cgit.cgi/poky/tree/%path%?h=%branch%</field>
   <field type="CharField" name="vcs_web_file_base_url">https://git.yoctoproject.org/cgit/cgit.cgi/poky/tree/%path%?h=%branch%</field>
</object>
<object model="orm.layer_version" pk="1">
   <field rel="ManyToOneRel" to="orm.layer" name="layer">1</field>
   <field type="IntegerField" name="layer_source">0</field>
   <field rel="ManyToOneRel" to="orm.release" name="release">1</field>
   <field type="CharField" name="branch">&DISTRO_NAME_NO_CAP;</field>
   <field type="CharField" name="dirpath">meta</field>
</object> <object model="orm.layer_version" pk="2">
   <field rel="ManyToOneRel" to="orm.layer" name="layer">1</field>
   <field type="IntegerField" name="layer_source">0</field>
   <field rel="ManyToOneRel" to="orm.release" name="release">2</field>
   <field type="CharField" name="branch">HEAD</field>
   <field type="CharField" name="commit">HEAD</field>
   <field type="CharField" name="dirpath">meta</field>
</object>
<object model="orm.layer_version" pk="3">
   <field rel="ManyToOneRel" to="orm.layer" name="layer">1</field>
   <field type="IntegerField" name="layer_source">0</field>
   <field rel="ManyToOneRel" to="orm.release" name="release">3</field>
   <field type="CharField" name="branch">master</field>
   <field type="CharField" name="dirpath">meta</field>
</object>
```

The layer \"pk\" values above must be unique, and typically start at \"1\". The layer version \"pk\" values must also be unique across all layers, and typically start at \"1\".

> "pk"层的值必须是唯一的，通常从"1"开始。所有层的版本"pk"值也必须在所有层中唯一，通常从"1"开始。

# Remote Toaster Monitoring

Toaster has an API that allows remote management applications to directly query the state of the Toaster server and its builds in a machine-to-machine manner. This API uses the `REST <Representational_state_transfer>` interface and the transfer of JSON files. For example, you might monitor a build inside a container through well supported known HTTP ports in order to easily access a Toaster server inside the container. In this example, when you use this direct JSON API, you avoid having web page parsing against the display the user sees.

> 智能烤面包机拥有一个 API，允许远程管理应用程序以机器对机器的方式直接查询智能烤面包机服务器及其建立的状态。此 API 使用 REST 接口和 JSON 文件传输。例如，您可以通过支持的已知 HTTP 端口监视容器中的构建，以便轻松访问容器中的智能烤面包机服务器。在此示例中，当您使用此直接 JSON API 时，您可以避免对用户看到的显示进行网页解析。

## Checking Health

Before you use remote Toaster monitoring, you should do a health check. To do this, ping the Toaster server using the following call to see if it is still alive:

> 在使用远程烤面包机监控之前，你应该做一次健康检查。要做到这一点，使用以下呼叫来 ping 烤面包机服务器，以查看它是否仍然存活：

```
http://host:port/health
```

Be sure to provide values for host and port. If the server is alive, you will get the response HTML:

> 确保提供主机和端口的值。如果服务器是活的，你会得到响应的 HTML。

```html
<!DOCTYPE html>
<html lang="en">
   <head><title>Toaster Health</title></head>
   <body>Ok</body>
</html>
```

## Determining Status of Builds in Progress

Sometimes it is useful to determine the status of a build in progress. To get the status of pending builds, use the following call:

> 有时，确定正在进行中的构建状态是有用的。要获取待处理构建的状态，请使用以下调用：

```
http://host:port/toastergui/api/building
```

Be sure to provide values for host and port. The output is a JSON file that itemizes all builds in progress. This file includes the time in seconds since each respective build started as well as the progress of the cloning, parsing, and task execution. Here is sample output for a build in progress:

> 请确保提供 host 和 port 的值。输出是一个 JSON 文件，它列出了所有正在进行中的构建。此文件还包括自每个构建开始以来的秒数以及克隆、解析和任务执行的进度。以下是正在进行中的构建的示例输出：

```JSON
{"count": 1,
 "building": [
   {"machine": "beaglebone",
     "seconds": "463.869",
     "task": "927:2384",
     "distro": "poky",
     "clone": "1:1",
     "id": 2,
     "start": "2017-09-22T09:31:44.887Z",
     "name": "20170922093200",
     "parse": "818:818",
     "project": "my_rocko",
     "target": "core-image-minimal"
   }]
}
```

The JSON data for this query is returned in a single line. In the previous example the line has been artificially split for readability.

> 此查询的 JSON 数据以单行形式返回。在前面的例子中，该行为了提高可读性而被人为拆分。

## Checking Status of Builds Completed

Once a build is completed, you get the status when you use the following call:

> 一旦构建完成，您可以使用以下调用获得状态：

```
http://host:port/toastergui/api/builds
```

Be sure to provide values for host and port. The output is a JSON file that itemizes all complete builds, and includes build summary information. Here is sample output for a completed build:

> 确保提供 host 和 port 的值。输出是一个 JSON 文件，它列出所有完整构建，并包括构建摘要信息。这是完成构建的示例输出：

```JSON
{"count": 1,
 "builds": [
   {"distro": "poky",
      "errors": 0,
      "machine": "beaglebone",
      "project": "my_rocko",
      "stop": "2017-09-22T09:26:36.017Z",
      "target": "quilt-native",
      "seconds": "78.193",
      "outcome": "Succeeded",
      "id": 1,
      "start": "2017-09-22T09:25:17.824Z",
      "warnings": 1,
      "name": "20170922092618"
   }]
}
```

The JSON data for this query is returned in a single line. In the previous example the line has been artificially split for readability.

> 这个查询的 JSON 数据以单行的形式返回。在上一个示例中，为了方便阅读，行已被人为地分割。

## Determining Status of a Specific Build

Sometimes it is useful to determine the status of a specific build. To get the status of a specific build, use the following call:

> 有时候确定特定构建的状态是有用的。要获取特定构建的状态，请使用以下调用：

```
http://host:port/toastergui/api/build/ID
```

Be sure to provide values for host, port, and ID. You can find the value for ID from the Builds Completed query. See the \"`toaster-manual/reference:checking status of builds completed`\" section for more information.

> 请确保提供主机、端口和 ID 的值。您可以从“已完成的构建查询”中找到 ID 的值。有关详细信息，请参阅“toaster-manual / reference：检查已完成构建的状态”部分。

The output is a JSON file that itemizes the specific build and includes build summary information. Here is sample output for a specific build:

> 输出是一个 JSON 文件，它列出了具体的构建，并包括构建摘要信息。这里是特定构建的示例输出：

```JSON
{"build":
   {"distro": "poky",
    "errors": 0,
    "machine": "beaglebone",
    "project": "my_rocko",
    "stop": "2017-09-22T09:26:36.017Z",
    "target": "quilt-native",
    "seconds": "78.193",
    "outcome": "Succeeded",
    "id": 1,
    "start": "2017-09-22T09:25:17.824Z",
    "warnings": 1,
    "name": "20170922092618",
    "cooker_log": "/opt/user/poky/build-toaster-2/tmp/log/cooker/beaglebone/build_20170922_022607.991.log"
   }
}
```

The JSON data for this query is returned in a single line. In the previous example the line has been artificially split for readability.

> 此查询的 JSON 数据以单行的形式返回。在前面的示例中，为了便于阅读，该行已被人为地拆分。

# Useful Commands

In addition to the web user interface and the scripts that start and stop Toaster, command-line commands are available through the `manage.py` management script. You can find general documentation on `manage.py` at the [Django](https://docs.djangoproject.com/en/2.2/topics/settings/) site. However, several `manage.py` commands have been created that are specific to Toaster and are used to control configuration and back-end tasks. You can locate these commands in the `Source Directory` (e.g. `poky`) at `bitbake/lib/manage.py`. This section documents those commands.

> 除了网页用户界面和启动和停止烤面包机的脚本外，还可以通过 `manage.py` 管理脚本获得命令行命令。您可以在 [Django](https://docs.djangoproject.com/en/2.2/topics/settings/) 网站上找到关于 `manage.py` 的一般文档。但是，已经创建了几个专门用于 Toaster 的 `manage.py` 命令，用于控制配置和后端任务。您可以在源目录(例如 `poky`)中的 `bitbake/lib/manage.py` 中找到这些命令。本节文档记录了这些命令。

::: note
::: title
Note
:::

- When using `manage.py` commands given a default configuration, you must be sure that your working directory is set to the `Build Directory`.

> 当使用默认配置的 `manage.py` 命令时，您必须确保工作目录设置为 `Build Directory`。从 `Build Directory` 使用 `manage.py` 命令可以让 Toaster 找到位于 `Build Directory` 中的 `toaster.sqlite` 文件。

- For non-default database configurations, it is possible that you can use `manage.py` commands from a directory other than the `Build Directory`. To do so, the `toastermain/settings.py` file must be configured to point to the correct database backend.

> 对于非默认的数据库配置，您可以从与“构建目录”不同的目录中使用“manage.py”命令。为此，必须配置“toastermain / settings.py”文件以指向正确的数据库后端。
> :::

## `buildslist`

The `buildslist` command lists all builds that Toaster has recorded. Access the command as follows:

> `buildslist` 命令可以列出 Toaster 所记录的所有构建。要使用该命令，可以按照以下方式访问：

```shell
$ bitbake/lib/toaster/manage.py buildslist
```

The command returns a list, which includes numeric identifications, of the builds that Toaster has recorded in the current database.

> 命令返回一个列表，其中包括 Toaster 在当前数据库中记录的建立的数字标识。

You need to run the `buildslist` command first to identify existing builds in the database before using the ``toaster-manual/reference:\`\`builddelete\`\` `` names:

> 你需要先运行 `buildslist` 命令来识别数据库中现有的构建，然后才能使用 `toaster-manual/reference:` builddelete `命令。这里有一个假设默认存储库和` 构建目录 ` 名称的示例：

```shell
$ cd poky/build
$ python ../bitbake/lib/toaster/manage.py buildslist
```

If your Toaster database had only one build, the above ``toaster-manual/reference:\`\`buildslist\`\` `` command would return something like the following:

> 如果您的 Toaster 数据库只有一个构建，上面的 ``toaster-manual / reference：````buildslist`` 命令将返回类似以下内容：

```
1: qemux86 poky core-image-minimal
```

## `builddelete`

The `builddelete` command deletes data associated with a build. Access the command as follows:

> `builddelete` 命令可以删除与构建相关的数据。使用如下命令访问：

```
$ bitbake/lib/toaster/manage.py builddelete build_id
```

The command deletes all the build data for the specified build_id. This command is useful for removing old and unused data from the database.

> 命令删除指定 build_id 的所有构建数据。此命令对于从数据库中删除旧的和未使用的数据很有用。

Prior to running the `builddelete` command, you need to get the ID associated with builds by using the ``toaster-manual/reference:\`\`buildslist\`\` `` command.

> 在运行 `builddelete` 命令之前，您需要使用 `toaster-manual/reference:` buildslist` 命令获取与构建相关联的 ID。

## `perf`

The `perf` command measures Toaster performance. Access the command as follows:

> 使用 `perf` 命令可以测量 Toaster 的性能。要使用该命令，请按照以下步骤操作：

```shell
$ bitbake/lib/toaster/manage.py perf
```

The command is a sanity check that returns page loading times in order to identify performance problems.

> 命令是一个简单的检查，可以返回页面加载时间，以便识别性能问题。

## `checksettings`

The `checksettings` command verifies existing Toaster settings. Access the command as follows:

> 命令 `checksettings` 用来验证现有的烤面包机设置。使用以下方式访问该命令：

```shell
$ bitbake/lib/toaster/manage.py checksettings
```

Toaster uses settings that are based on the database to configure the building tasks. The `checksettings` command verifies that the database settings are valid in the sense that they have the minimal information needed to start a build.

> 烤面包机使用基于数据库的设置来配置构建任务。`checksettings` 命令验证数据库设置是否有足够的信息来启动构建。

In order for the `checksettings` command to work, the database must be correctly set up and not have existing data. To be sure the database is ready, you can run the following:

> 为了使 `checksettings` 命令正常工作，数据库必须正确设置，不能有现有的数据。为了确保数据库准备就绪，您可以运行以下内容：

```shell
$ bitbake/lib/toaster/manage.py syncdb
$ bitbake/lib/toaster/manage.py migrate orm
$ bitbake/lib/toaster/manage.py migrate bldcontrol
```

After running these commands, you can run the `checksettings` command.

> 在运行了这些命令之后，你可以运行 `checksettings` 命令。

## `runbuilds`

The `runbuilds` command launches scheduled builds. Access the command as follows:

> `runbuilds` 命令可以启动定时生成。按照以下方式访问该命令：

```shell
$ bitbake/lib/toaster/manage.py runbuilds
```

The `runbuilds` command checks if scheduled builds exist in the database and then launches them per schedule. The command returns after the builds start but before they complete. The Toaster Logging Interface records and updates the database when the builds complete.

> `runbuilds` 命令检查数据库中是否存在计划构建，然后根据时间表启动它们。命令在构建启动后返回，但在它们完成之前。当构建完成时，烤面包机日志接口记录并更新数据库。
