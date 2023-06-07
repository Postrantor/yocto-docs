---
tip: translate by baidu@2023-06-07 17:11:54
...
---
title: Using the Error Reporting Tool
-------------------------------------

The error reporting tool allows you to submit errors encountered during builds to a central database. Outside of the build environment, you can use a web interface to browse errors, view statistics, and query for errors. The tool works using a client-server system where the client portion is integrated with the installed Yocto Project `Source Directory`{.interpreted-text role="term"} (e.g. `poky`). The server receives the information collected and saves it in a database.

> 错误报告工具允许您将生成过程中遇到的错误提交到中央数据库。在生成环境之外，您可以使用 web 界面浏览错误、查看统计信息和查询错误。该工具使用客户端-服务器系统工作，其中客户端部分与已安装的 Yocto 项目“源目录”｛.explored text role=“term”｝（例如“poky”）集成。服务器接收收集的信息并将其保存在数据库中。

There is a live instance of the error reporting server at [https://errors.yoctoproject.org](https://errors.yoctoproject.org). When you want to get help with build failures, you can submit all of the information on the failure easily and then point to the URL in your bug report or send an email to the mailing list.

> 存在错误报告服务器的活动实例，位于 [https://errors.yoctoproject.org](https://errors.yoctoproject.org)。当你想获得有关构建失败的帮助时，你可以很容易地提交有关失败的所有信息，然后指向错误报告中的 URL 或向邮件列表发送电子邮件。

::: note
::: title
Note
:::

If you send error reports to this server, the reports become publicly visible.

> 如果将错误报告发送到此服务器，则这些报告将公开可见。
> :::

# Enabling and Using the Tool

By default, the error reporting tool is disabled. You can enable it by inheriting the `ref-classes-report-error`{.interpreted-text role="ref"} class by adding the following statement to the end of your `local.conf` file in your `Build Directory`{.interpreted-text role="term"}:

> 默认情况下，错误报告工具处于禁用状态。您可以通过继承 `ref classes report error`｛.depreted text role=“ref”｝类来启用它，方法是将以下语句添加到 `Build Directory`｛.repreted text role=“term”｝中的 `local.conf` 文件的末尾：

```
INHERIT += "report-error"
```

By default, the error reporting feature stores information in `${``LOG_DIR`{.interpreted-text role="term"}`}/error-report`. However, you can specify a directory to use by adding the following to your `local.conf` file:

> 默认情况下，错误报告功能将信息存储在 `$｛` LOG_DIR `｛.explored text role=“term”｝`｝/error-report` 中。但是，您可以通过在“local.conf”文件中添加以下内容来指定要使用的目录：

```
ERR_REPORT_DIR = "path"
```

Enabling error reporting causes the build process to collect the errors and store them in a file as previously described. When the build system encounters an error, it includes a command as part of the console output. You can run the command to send the error file to the server. For example, the following command sends the errors to an upstream server:

> 启用错误报告会导致生成过程收集错误并将其存储在文件中，如前所述。当构建系统遇到错误时，它会将一个命令作为控制台输出的一部分。您可以运行该命令将错误文件发送到服务器。例如，以下命令将错误发送到上游服务器：

```
$ send-error-report /home/brandusa/project/poky/build/tmp/log/error-report/error_report_201403141617.txt
```

In the previous example, the errors are sent to a public database available at [https://errors.yoctoproject.org](https://errors.yoctoproject.org), which is used by the entire community. If you specify a particular server, you can send the errors to a different database. Use the following command for more information on available options:

> 在前面的示例中，错误被发送到位于的公共数据库 [https://errors.yoctoproject.org](https://errors.yoctoproject.org)，供整个社区使用。如果指定了特定的服务器，则可以将错误发送到其他数据库。有关可用选项的详细信息，请使用以下命令：

```
$ send-error-report --help
```

When sending the error file, you are prompted to review the data being sent as well as to provide a name and optional email address. Once you satisfy these prompts, the command returns a link from the server that corresponds to your entry in the database. For example, here is a typical link: [https://errors.yoctoproject.org/Errors/Details/9522/](https://errors.yoctoproject.org/Errors/Details/9522/)

> 发送错误文件时，系统会提示您查看正在发送的数据，并提供名称和可选的电子邮件地址。满足这些提示后，该命令将从服务器返回一个链接，该链接对应于数据库中的条目。例如，这里有一个典型的链接：[https://errors.yoctoproject.org/Errors/Details/9522/](https://errors.yoctoproject.org/Errors/Details/9522/)

Following the link takes you to a web interface where you can browse, query the errors, and view statistics.

> 按照链接，您将进入一个 web 界面，在那里您可以浏览、查询错误和查看统计信息。

# Disabling the Tool

To disable the error reporting feature, simply remove or comment out the following statement from the end of your `local.conf` file in your `Build Directory`{.interpreted-text role="term"}:

> 要禁用错误报告功能，只需从“构建目录”中的“local.conf”文件末尾删除或注释掉以下语句即可｛.depreted text role=“term”｝：

```
INHERIT += "report-error"
```

# Setting Up Your Own Error Reporting Server

If you want to set up your own error reporting server, you can obtain the code from the Git repository at :yocto\_[git:%60/error-report-web/](git:%60/error-report-web/)\`. Instructions on how to set it up are in the README document.

> 如果您想设置自己的错误报告服务器，可以从 Git 存储库中获取代码，网址为：yocto\_[Git:%60/error-report-web/]（Git:%60/error-report-web/）\`。README 文档中提供了有关如何设置它的说明。
