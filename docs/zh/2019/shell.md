---
layout: lecture
title: "Shell编程和脚本"
presenter: Jon
video:
  aspect: 56.25
  id: dbDRfmH5uSI
---

<iframe src="https://www.youtube.com/embed/dbDRfmH5uSI" frameborder="0" allowfullscreen></iframe>

Shell 是一种高效的计算机文本界面。

Shell 提示符：当你打开终端时会见到的界面，它允许你运行程序和命令；一些常见的包括：

- `cd` 用于改变目录
- `ls` 用于列出文件和目录
- `mv` 和 `cp` 用于移动和复制文件

但是 Shell 能让你做**更多**事情；你可以调用计算机上的任何程序，并且几乎所有你想做的事情都存在命令行工具。而且它们通常比图形界面更高效。我们将在本课程中介绍一系列这样的工具。

Shell 提供了一个交互式编程语言（“脚本”）。存在许多种 Shell：

- 你可能使用过 `sh` 或 `bash`。
- 也有匹配特定语言的 Shell：`csh`。
- 或者是"更好"的 Shell：`fish`、`zsh`、`ksh`。

在本课程中，我们将关注无处不在的 `sh` 和 `bash`，但是随意尝试其他的也无妨。我喜欢 `fish`。

Shell 编程是你工具箱中**非常**有用的工具。可以直接在提示符下编写程序，或者写入到一个文件中。`#!/bin/sh` + `chmod +x` 使得 Shell 脚本可执行。

## 使用 Shell

多次运行一个命令

```bash
for i in $(seq 1 5); do echo hello; done
```

这里有很多内容需要讲解：

- `for x in list; do BODY; done`
  - `;` 结束一个命令 —— 相当于新行
  - 分割 `list`，将每个元素赋值给 `x`，然后运行代码主体
  - 分割是基于“空白字符分割”，这个我们稍后会讨论
  - Shell 中没有花括号，所以用 `do` + `done`
- `$(seq 1 5)`
  - 运行程序 `seq` 并带有参数 `1` 和 `5`
  - 将整个 `$()` 用该程序的输出替换
  - 等价于
    ```bash
    for i in 1 2 3 4 5
    ```
- `echo hello`
  - Shell 脚本中的一切都是命令
  - 在这个案例中，运行 `echo` 命令，它会把参数`hello`打印出
  - 所有命令都在 `$PATH` 中搜索（冒号分隔）

我们有变量：

```bash
for f in $(ls); do echo $f; done
```

将会打印当前目录中每个文件的名称。也可以使用 `=` 设置变量（没有空格！）：

```bash
foo=bar
echo $foo
```

还有一些"特殊"变量：

- `$1` 到 `$9`：脚本的参数
- `$0` 脚本本身的名称
- `$#` 参数的数量
- `$$` 当前 Shell 的进程 ID

仅打印目录

```bash
for f in $(ls); do if test -d $f; then echo dir $f; fi; done
```

更多的解释在这里：

- `if CONDITION; then BODY; fi`
  - `CONDITION`是一个命令；如果它返回退出状态 0（成功），则执行`BODY`。
  - 也可以加入`else`或`elif`
  - 同样，没有大括号，因此使用`then` + `fi`
- `test`是另一个程序，提供各种检查和比较，并且如果它们为真（`$?`），则退出状态为 0
  - `man COMMAND`是你的好朋友：`man test`
  - 也可以使用`[` + `]`来调用：`[ -d $f ]`
    - 看看`man test`和`which "["`

但等一下！这样做是错误的！如果一个文件名叫做"My Documents"怎么办？

- `for f in $(ls)`展开为`for f in My Documents`
- 首先对`My`进行测试，然后对`Documents`
- 这不是我们想要的！
- 是 shell 脚本中最大的 bug 来源

## 参数分割

Bash 按空白符分割参数；这并不总是你想要的！

- 需要使用引号来处理参数中的空格
  `for f in "My Documents"` 将会正确工作
- 同样的问题出现在别处——你看到是哪里了吗？
  `test -d $f`：如果`$f`包含空格，`test`会出错！
- `echo`碰巧没问题，因为它通过空格分割再连接
  但如果文件名包含换行符怎么办？！会变成空格！
- 引用所有你不想被分割的变量的使用
- 但我们如何修复上面的脚本呢？
  你认为`for f in "$(ls)"`会做什么？

通配符是答案！

- bash 知道如何使用模式查找文件：
  - `*` 任意字符串字符
  - `?` 任何单个字符
  - `{a,b,c}` 这些字符中的任何一个
- `for f in *`：此目录中的所有文件
- 使用通配符时，每个匹配的文件成为其自己的参数
  - 使用时仍需确保引用：`test -d "$f"`
- 可以制作高级模式：
  - `for f in a*`：当前目录中以`a`开头的所有文件
  - `for f in foo/*.txt`：`foo`中的所有`.txt`文件
  - `for f in foo/*/p??.txt`
    `foo`的子目录中以 p 开头的所有三个字母的文本文件

空白字符问题不止这些：

- `if [ $foo = "bar" ]; then` —— 看到问题了吗？
- 如果`$foo`为空呢？传递给`[`的参数是`=`和`bar`...
- **可以**通过使用`[ x$foo = "xbar" ]`来解决这个问题，但呃
- 相反，使用`[[`：bash 内置的比较器，具有特殊解析
  - 也允许使用`&&`代替`-a`，`||`代替`-o`等。

<!-- TODO: 数组？ $@. ${array[@]} vs "${array[@]}". -->

## 可组合性

Shell 之所以强大部分原因是因为其可组合性。可以将多个程序链接在一起，而不是让一个程序做所有事情。

关键字符是`|`（管道）。

- `a | b`意味着同时运行`a`和`b`
  将`a`的所有输出作为输入发送给`b`
  打印`b`的输出

你启动的所有程序（“进程”）都有三个“流”：

- `STDIN`：当程序读取输入时，它来自这里
- `STDOUT`：当程序打印某些东西时，它去到这里
- `STDERR`：程序可以选择使用的第二个输出
- 默认情况下，`STDIN`是你的键盘，`STDOUT`和`STDERR`都是你的终端。但你可以改变这一点！
  - `a | b`使`a`的`STDOUT`成为`b`的`STDIN`。
  - 还有：
    - `a > foo`（`a`的`STDOUT`进入文件`foo`）
    - `a 2> foo`（`a`的`STDERR`进入文件`foo`）
    - `a < foo`（从文件`foo`读取`a`的`STDIN`）
    - 提示：`tail -f`会在文件被写入时打印它
- 为什么这有用？让你可以操纵程序的输出！
  - `ls | grep foo`：包含单词`foo`的所有文件
  - `ps | grep foo`：包含单词`foo`的所有进程
  - `journalctl | grep -i intel | tail -n5`：
    最后 5 条包含单词 intel（不区分大小写）的系统日志消息
  - `who | sendmail -t me@example.com`
    将登录用户列表发送至`me@example.com`
  - 形成了数据处理的基础，我们稍后会介绍

Bash 还提供了许多其他方式来组合程序。

你可以用`(a; b) | tac`组合命令：运行`a`，然后是`b`，并将它们的所有输出发送给`tac`，它会以相反的顺序打印其输入。

一个较不为人知，但非常有用的是**进程替代**。
`b <(a)`将运行`a`，为其输出流生成一个临时文件名，并将该文件名传递给`b`。例如：

```bash
diff <(journalctl -b -1 | head -n20) <(journalctl -b -2 | head -n20)
```

将显示上一次启动日志的前 20 行与之前那次的差异。

<!-- TODO: 退出代码? -->

## 作业和进程控制

如果你想在后台运行长期任务怎么办？

- 使用`&`后缀可以让程序“在后台”运行
  - 这会立即返回命令提示符
  - 如果你想同时运行两个程序很方便
    比如服务器和客户端：`server & client`
  - 注意，运行的程序仍然使用你的终端作为`STDOUT`！
    尝试：`server > server.log & client`
- 用`jobs`查看所有这样的进程
  - 注意它显示为“Running”
- 使用`fg %JOB`将其带到前台（无参数表示最新的）
- 如果你想将当前程序放到后台：`^Z` + `bg`（这里的`^Z`表示按`Ctrl+Z`）
  - `^Z`停止当前进程并将其变为一个“作业”
  - `bg`在后台运行最后一个作业（就像你做的`&`）
- 后台作业仍然绑定到你当前的会话，如果你登出它们会退出。`disown`允许你切断这个连接。或者使用`nohup`。
- `$!`是最后一个后台进程的进程 ID

<!-- TODO: 进程输出控制（^S 和 ^Q）？ -->

关于你电脑上运行的其他东西怎么办？

- `ps`是你的好朋友：列出运行中的进程
  - `ps -A`：打印所有用户的进程（也可以用`ps ax`）
  - `ps`有**很多**参数：见`man ps`
- `pgrep`：通过搜索找到进程（类似于`ps -A | grep`）
  - `pgrep -af`：搜索并显示参数
- `kill`：通过 ID 向进程发送一个**信号**（通过搜索`pkill` + `-f`）
  - 信号告诉进程“做某事”
  - 最常见的是：`SIGKILL`（`-9`或`-KILL`）：告诉它**现在**退出
    等同于`^\`
  - 还有`SIGTERM`（`-15`或`-TERM`）：告诉它优雅地退出
    等同于`^C`

## 标志

大多数命令行工具使用**标志**来接受参数。标志通常有短格式（`-h`）和长格式（`--help`）。通常运行`CMD -h`或`man CMD`会给你一个程序接受的标志列表。
短标志通常可以组合，运行`rm -r -f`等同于运行`rm -rf`或`rm -fr`。
一些常见的标志是事实上的标准，在许多应用程序中你会看到它们：

- `-a`通常指所有文件（即包括那些以点开头的）
- `-f`通常指强制做某事，比如`rm -f`
- `-h`显示大多数命令的帮助
- `-v`通常启用详细输出
- `-V`通常打印命令的版本

此外，双破折号`--`在内置命令和许多其他命令中用于表示命令选项的结束，之后只接受位置参数。所以如果你有一个名为`-v`的文件（这是可能的）并想用 grep 查找它`grep pattern -- -v`会起作用而`grep pattern -v`不会。实际上，创建这样的文件的一种方式是执行`touch -- -v`。

## 练习

1. 如果您完全是 Shell 的新手，您可能希望阅读更全面的指南，例如[BashGuide](http://mywiki.wooledge.org/BashGuide)。如果您想要更深入的介绍，[Linux 命令行](http://linuxcommand.org/tlcl.php)是一个很好的资源。

1. **PATH、which、type**命令
    我们简要讨论了`PATH`环境变量用于定位通过命令行运行的程序。让我们更深入地探讨一下：

    - 运行`echo $PATH`（或`echo $PATH | tr -s ':' '\n'`进行漂亮打印），并检查其内容，列出了哪些位置？
    - 命令`which`用于在用户 PATH 中定位程序。尝试运行`which`，用于常见命令如`echo`、`ls`或`mv`。注意`which`有些局限，因为它不理解 Shell 别名。尝试对同样的命令运行`type`和`command -v`。输出有什么不同？
    - 运行`PATH=`，然后尝试重新运行之前的命令，有些可以工作，有些不行，您能找出原因吗？

1. **特殊变量**

    - 变量`~`展开为什么？`.`呢？`..`呢？
    - 变量`$?`是做什么的？
    - 变量`$_`的作用是什么？
    - 变量`!!`展开为什么？`!!*`呢？`!l`呢？
    - 查找这些选项的文档并熟悉它们。

1. **xargs**
    有时候，使用管道无法正常工作，因为要输入的命令不是期望的以换行符分隔的格式。例如`file`命令告诉您文件的属性。
    尝试运行`ls | file`和`ls | xargs file`。`xargs`在做什么？

1. **Shebang**注释
    当您编写脚本时，可以使用[shebang](<https://en.wikipedia.org/wiki/Shebang_(Unix)>)行指定您的 Shell 应该使用什么解释器来解释脚本。编写一个名为`hello`的脚本，内容如下，并使用`chmod +x hello`使其可执行。然后用`./hello`执行它。然后删除第一行并再次执行它？Shell 如何使用第一行？
    ```bash
    #! /usr/bin/python

    print("Hello World!")
    ```
    您经常会看到具有类似`#! usr/bin/env bash`的 shebang 的程序。这是一种更便携的解决方案，具有自己的一套[优点和缺点](https://unix.stackexchange.com/questions/29608/why-is-it-better-to-use-usr-bin-env-name-instead-of-path-to-name-as-my)。`env`与`which`有什么不同？`env`使用哪个环境变量来决定要运行哪个程序？

1. **管道、进程替换、子 shell**
    创建一个名为`slow_seq.sh`的脚本，内容如下，并执行`chmod +x slow_seq.sh`使其可执行。
    ```bash
    #! /usr/bin/env bash

    for i in $(seq 1 10); do
             echo $i;
             sleep 1;
    done
    ```
    管道（和进程替换）与使用子 shell 执行（即`$()`）有一种不同的方式。运行以下命令并观察差异：

    - `./slow_seq.sh | grep -P "[3-6]"`
    - `grep -P "[3-6]" <(./slow_seq.sh)`
    - `echo $(./slow_seq.sh) | grep -P "[3-6]"`

1. **其他**
    - 尝试运行`touch {a,b}{a,b}`然后`ls`，看到了什么？
    - 有时您希望保留 STDIN 并将其仍然输入到文件中。尝试运行`echo HELLO | tee hello.txt`
    - 尝试运行`cat hello.txt > hello.txt `您期望会发生什么？实际发生了什么？
    - 运行`echo HELLO > hello.txt`，然后运行`echo WORLD >> hello.txt`。`hello.txt`的内容是什么？`>`和`>>`有什么不同？
    - 运行`printf "\e[38;5;81mfoo\e[0m\n"`。输出有何不同？如果想了解更多，请搜索 ANSI 颜色转义序列。
    - 运行`touch a.txt`，然后运行`^txt^log`。bash 为您做了什么？同样，运行`fc`。它是做什么的？
  <!-- {% comment %}
    TODO

    1. **parallel**
    - set -e, set -x
    - traps

    {% endcomment %} -->

1. **键盘快捷键**
    与您经常使用的任何应用程序一样，熟悉其键盘快捷键是值得的。键入以下快捷键，并尝试弄清楚它们的作用以及在何种情况下掌握它们可能会很方便。对于其中一些，通过在线搜索它们的作用可能会更容易。（记住`^X`表示按`Ctrl+X`）
    - `^A`、`^E`
    - `^R`
    - `^L`
    - `^C`、`^\`和`^D`
    - `^U`和`^Y`
