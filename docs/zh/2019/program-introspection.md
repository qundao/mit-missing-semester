---
layout: lecture
title: "程序自省"
presenter: Anish
video:
  aspect: 62.5
  id: 74MhV-7hYzg
---

## 调试

当打印输出调试不够用时：使用调试器。

调试器允许您与程序的执行交互，让您可以做到以下事情：

- 当程序达到某一行时停止执行
- 逐步执行程序
- 检查变量的值
- 许多更高级的功能

### GDB/LLDB

[GDB](https://www.gnu.org/software/gdb/) 和 [LLDB](https://lldb.llvm.org/)。
支持许多类似 C 的语言。

让我们看看 [example.c](files/example.c)。使用调试标志编译：`gcc -g -o example example.c`。

打开 GDB：

`gdb example`

一些命令：

- `run`
- `b {函数名}` - 设置断点
- `b {文件}:{行号}` - 设置断点
- `c` - 继续执行
- `step` / `next` / `finish` - 逐步执行 / 跳过 / 退出当前函数
- `p {变量名}` - 打印变量的值
- `watch {表达式}` - 设置监视点，当表达式的值发生变化时触发
- `rwatch {表达式}` - 设置监视点，当读取该值时触发
- `layout`

### PDB

[PDB](https://docs.python.org/3/library/pdb.html) 是 Python 的调试器。

在希望进入 PDB 的地方插入 `import pdb; pdb.set_trace()`，基本上是调试器（如 GDB）和 Python shell 的混合体。

### Web 浏览器开发者工具

另一个调试器的示例，这次是带有图形界面的。

## strace

观察程序所进行的系统调用：`strace {程序}`。

## 性能分析

性能分析的类型：CPU、内存等。

最简单的性能分析器：`time`。

### Go

使用 CPU 分析器运行测试代码：`go test -cpuprofile=cpu.out`

分析分析结果：`go tool pprof -web cpu.out`

使用内存分析器运行测试代码：`go test -memprofile=mem.out`

分析分析结果：`go tool pprof -web mem.out`

### Perf

基本性能统计：`perf stat {命令}`

使用性能分析器运行程序：`perf record {命令}`

分析分析结果：`perf report`
