---
tip: translate by openai@2023-06-07 20:47:20
...
---
title: Real-World Examples
--------------------------

|

This chapter contains real-world examples.

> 这一章包含实际的例子。

# Slow Write Speed on Live Images

In one of our previous releases (denzil), users noticed that booting off of a live image and writing to disk was noticeably slower. This included the boot itself, especially the first one, since first boots tend to do a significant amount of writing due to certain post-install scripts.

> 在我们之前的一次发布（Denzil）中，用户注意到从 Live 映像启动并写入磁盘的速度明显变慢。这包括启动本身，特别是第一次启动，因为第一次启动往往会由于某些安装后脚本而进行大量的写入。

The problem (and solution) was discovered by using the Yocto tracing tools, in this case \'perf stat\', \'perf script\', \'perf record\' and \'perf report\'.

> 问题（和解决方案）是通过使用 Yocto 跟踪工具，在本例中是'perf stat'、'perf script'、'perf record'和'perf report'发现的。

See all the unvarnished details of how this bug was diagnosed and solved here: Yocto Bug #3049

> 在这里查看有关如何诊断和解决此错误的所有未经润饰的细节：Yocto Bug＃3049.
