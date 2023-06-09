---
tip: translate by openai@2023-06-10 10:46:30
...
---
title: Using the Error Reporting Tool
-------------------------------------

The error reporting tool allows you to submit errors encountered during builds to a central database. Outside of the build environment, you can use a web interface to browse errors, view statistics, and query for errors. The tool works using a client-server system where the client portion is integrated with the installed Yocto Project `Source Directory` (e.g. `poky`). The server receives the information collected and saves it in a database.

> 该错误报告工具可以将构建过程中遇到的错误提交到中央数据库。在构建环境之外，您可以使用 Web 界面浏览错误、查看统计信息和查询错误。该工具使用客户端/服务器系统工作，其中客户端部分与安装的 Yocto 项目源目录(例如 poky)集成。服务器接收收集的信息并将其保存到数据库中。

There is a live instance of the error reporting server at [https://errors.yoctoproject.org](https://errors.yoctoproject.org). When you want to get help with build failures, you can submit all of the information on the failure easily and then point to the URL in your bug report or send an email to the mailing list.

> 在 [https://errors.yoctoproject.org](https://errors.yoctoproject.org) 上有一个错误报告服务器的实时实例。当您想获得构建失败的帮助时，您可以轻松地提交所有失败信息，然后在漏洞报告中指向 URL，或者发送电子邮件到邮件列表。

::: note
::: title
Note
:::

If you send error reports to this server, the reports become publicly visible.
:::

# Enabling and Using the Tool

By default, the error reporting tool is disabled. You can enable it by inheriting the `ref-classes-report-error`:

> 默认情况下，错误报告工具被禁用。您可以通过在您的 `Build Directory` 类来启用它：

```
INHERIT += "report-error"
```

By default, the error reporting feature stores information in `$/error-report`. However, you can specify a directory to use by adding the following to your `local.conf` file:

> 默认情况下，错误报告功能将信息存储在 `$/error-report` 中。但是，您可以通过在 `local.conf` 文件中添加以下内容来指定要使用的目录：

```
ERR_REPORT_DIR = "path"
```

Enabling error reporting causes the build process to collect the errors and store them in a file as previously described. When the build system encounters an error, it includes a command as part of the console output. You can run the command to send the error file to the server. For example, the following command sends the errors to an upstream server:

> 启用错误报告会导致构建过程收集错误并按照先前描述的方式存储在文件中。当构建系统遇到错误时，它会将一条命令包含在控制台输出中。您可以运行该命令以将错误文件发送到服务器。例如，以下命令将错误发送到上游服务器：

```
$ send-error-report /home/brandusa/project/poky/build/tmp/log/error-report/error_report_201403141617.txt
```

In the previous example, the errors are sent to a public database available at [https://errors.yoctoproject.org](https://errors.yoctoproject.org), which is used by the entire community. If you specify a particular server, you can send the errors to a different database. Use the following command for more information on available options:

> 在前面的例子中，错误会发送到一个公共数据库，可以在 [https://errors.yoctoproject.org](https://errors.yoctoproject.org) 访问，被整个社区使用。如果指定一个特定的服务器，你可以将错误发送到不同的数据库。要了解更多可用选项，请使用以下命令：

```
$ send-error-report --help
```

When sending the error file, you are prompted to review the data being sent as well as to provide a name and optional email address. Once you satisfy these prompts, the command returns a link from the server that corresponds to your entry in the database. For example, here is a typical link: [https://errors.yoctoproject.org/Errors/Details/9522/](https://errors.yoctoproject.org/Errors/Details/9522/)

> 当发送错误文件时，您将被提示审查要发送的数据，并提供名称和可选的电子邮件地址。一旦您满足这些要求，该命令将从服务器返回与数据库中条目相对应的链接。例如，这里是一个典型的链接：[https://errors.yoctoproject.org/Errors/Details/9522/](https://errors.yoctoproject.org/Errors/Details/9522/)

Following the link takes you to a web interface where you can browse, query the errors, and view statistics.

# Disabling the Tool

To disable the error reporting feature, simply remove or comment out the following statement from the end of your `local.conf` file in your `Build Directory`:

> 在您的构建目录中的 `local.conf` 文件末尾，只需删除或注释掉以下语句，即可禁用错误报告功能：

```
INHERIT += "report-error"
```

# Setting Up Your Own Error Reporting Server

If you want to set up your own error reporting server, you can obtain the code from the Git repository at :yocto_[git:%60/error-report-web/](git:%60/error-report-web/)\`. Instructions on how to set it up are in the README document.

> 如果你想设置自己的错误报告服务器，你可以从 Git 存储库 yocto_[git:%60/error-report-web/](git:%60/error-report-web/)获取代码。关于如何设置它的说明在 README 文件中。
