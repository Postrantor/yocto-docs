---
tip: translate by openai@2023-06-07 20:46:50
...
---
subtitle: Architecture of the Tracing and Profiling Tools

> 学习和分析工具的架构
> title: Overall Architecture of the Linux Tracing and Profiling Tools

---

It may seem surprising to see a section covering an \'overall architecture\' for what seems to be a random collection of tracing tools that together make up the Linux tracing and profiling space. The fact is, however, that in recent years this seemingly disparate set of tools has started to converge on a \'core\' set of underlying mechanisms:

> 可能会让人惊讶的是，在这种看似随机收集的跟踪工具中，有一个涵盖“整体架构”的部分。但事实是，近年来，这些看似不相关的工具开始聚焦在一组“核心”的基础机制上：

- static tracepoints
- dynamic tracepoints
  - kprobes
  - uprobes
- the perf_events subsystem
- debugfs

::: admonition

Tying it Together

> 把它拼接在一起

Rather than enumerating here how each tool makes use of these common mechanisms, textboxes like this will make note of the specific usages in each tool as they come up in the course of the text.

> 相較於在這裡列舉每個工具如何使用這些共同機制，像這樣的文字框將在文本中出現時注意到每個工具的特定用法。
> :::
