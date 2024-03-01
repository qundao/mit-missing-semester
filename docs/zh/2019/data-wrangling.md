---
layout: lecture
title: "数据整理"
presenter: Jon
video:
  aspect: 56.25
  id: VW2jn9Okjhw
---

<iframe src="https://www.youtube.com/embed/VW2jn9Okjhw" frameborder="0" allowfullscreen></iframe>

你是否曾经有一大堆文本并希望对其进行处理？很好，这就是数据整理的全部意义！具体来说，就是将数据从一种格式调整到另一种，直到你得到了想要的结果。

我们已经看到了基本的数据整理：`journalctl | grep -i intel`。

- 查找所有提到 Intel 的系统日志条目（不区分大小写）。
- 实际上，大多数数据整理都是关于了解你拥有哪些工具，以及如何将它们组合使用。

让我们从头开始：我们需要一个数据源，以及对其进行操作。日志通常是一个很好的用例，因为你经常想要调查其中的事情，并且阅读整个日志是不可行的。让我们通过查看我的服务器日志来找出谁正在尝试登录到我的服务器：

```bash
ssh myserver journalctl
```

这些内容太多了。让我们将其限制在与 ssh 相关的内容上：

```bash
ssh myserver journalctl | grep sshd
```

请注意，我们正在使用管道将一个 **远程** 文件通过我们本地计算机上的 `grep` 进行流式处理！`ssh` 真是神奇。但这仍然是比我们想要的要多得多的内容。而且很难阅读。让我们做得更好一些：

```bash
ssh myserver journalctl | grep sshd | grep "Disconnected from"
```

这里仍然有很多噪音。有很多方法可以消除这些噪音，但让我们看看你工具箱中最强大的工具之一：`sed`。

`sed` 是一个建立在旧版 `ed` 编辑器之上的 "流编辑器"。在其中，你基本上是提供了如何修改文件的简短命令，而不是直接操作其内容（尽管你也可以这样做）。有大量的命令，但最常见的之一是 `s`：替换。例如，我们可以写：

```bash
ssh myserver journalctl
 | grep sshd
 | grep "Disconnected from"
 | sed 's/.*Disconnected from //'
```

我们刚刚写的是一个简单的 **正则表达式**；这是一种强大的构造，让你能够根据模式匹配文本。`s` 命令的写法是：`s/正则表达式/替换文本/`，其中 `正则表达式` 是你想要搜索的正则表达式，而 `替换文本` 是你想要用匹配文本替换的文本。

## 正则表达式

正则表达式是如此常见和有用，以至于值得花一些时间来理解它们是如何工作的。让我们首先看一下我们上面使用的那个：`/.*Disconnected from /`。正则表达式通常（虽然不总是）被 `/` 包围。大多数 ASCII 字符只是保持其正常含义，但某些字符具有“特殊”的匹配行为。确切地说，哪些字符做什么在不同的正则表达式实现之间有所不同，这是一个令人沮丧的问题。非常常见的模式有：

- `.` 表示“任意单个字符”，除了换行符
- `*` 前一个匹配项的零个或多个
- `+` 前一个匹配项的一个或多个
- `[abc]` `a`、`b` 和 `c` 中的任意一个字符
- `(RX1|RX2)` 匹配 `RX1` 或 `RX2`
- `^` 行的开头
- `$` 行的结尾

`sed` 的正则表达式有些奇怪，你需要在大多数特殊字符前面加上 `\` 才能赋予它们特殊含义。或者你可以使用 `-E` 选项。

因此，回顾一下 `/.*Disconnected from /`，我们看到它匹配任何以任意数量字符开头，后跟文本字符串“Disconnected from”。这正是我们想要的。但要小心，正则表达式是棘手的。如果有人尝试使用用户名“Disconnected from”登录，我们会得到：

```
Jan 17 03:13:00 thesquareplanet.com sshd[2631]: Disconnected from invalid user Disconnected from 46.97.239.16 port 55920 [preauth]
```

我们最终会得到什么呢？嗯，`*` 和 `+` 默认情况下是“贪婪”的。它们会尽可能匹配更多的文本。因此，在上面的例子中，我们最终会得到：

```
46.97.239.16 port 55920 [preauth]
```

这可能不是我们想要的结果。在某些正则表达式实现中，您可以将 `*` 或 `+` 后缀添加 `?` 以使它们变得非贪婪，但不幸的是 `sed` 不支持该功能。不过，我们可以切换到 Perl 的命令行模式，因为它支持这种构造：

```bash
perl -pe 's/.*?Disconnected from //'
```

不过，我们将在接下来的内容中继续使用 `sed`，因为它是处理这类任务的更常用工具。`sed` 还可以执行其他方便的操作，比如打印给定匹配后的行、每次调用进行多个替换、搜索等等。但我们在这里不会过多涉及。`sed` 实际上是一个独立的主题，但通常有更好的工具。

好的，我们还有一个后缀想要去掉。我们该如何做呢？匹配跟在用户名后面的文本有点棘手，特别是如果用户名中包含空格之类的字符！我们需要做的是匹配整行：

```bash
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user .* [^ ]+ port [0-9]+( \[preauth\])?$//'
```

让我们通过 [regex 调试器](https://regex101.com/r/qqbZqh/2) 来看一下发生了什么。好的，开头仍然和之前一样。然后，我们匹配了任何“user”变体（日志中有两个前缀）。然后我们匹配了任何包含用户名的字符串。接着我们匹配了任何单词（`[^ ]+`；任何非空的非空格字符序列）。然后是单词 "port" 后面跟着一系列数字。然后可能是后缀 ` [preauth]`，最后是行尾。

请注意，通过这种技术，“Disconnected from” 作为用户名将不再混淆我们。你能理解为什么吗？

不过，这种方法有一个问题，那就是整个日志都变为空了。毕竟，我们仍然想要保留用户名。为此，我们可以使用“捕获组”。由括号括起来的正则表达式匹配的任何文本都将存储在一个编号的捕获组中。这些捕获组在替换中（有些引擎甚至在模式本身中）可以使用 `\1`、`\2`、`\3` 等访问。因此：

```bash
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'
```

正如你可能想象的那样，你可以设计出非常复杂的正则表达式。例如，这里有一篇关于如何匹配 [电子邮件地址](https://www.regular-expressions.info/email.html) 的文章。这并不 [容易](https://web.archive.org/web/20221223174323/http://emailregex.com/)。还有 [很多讨论](https://stackoverflow.com/questions/201323/how-to-validate-an-email-address-using-a-regular-expression/1917982)。人们已经 [编写了测试](https://fightingforalostcause.net/content/misc/2006/compare-email-regex.php)。还有 [测试矩阵](https://mathiasbynens.be/demo/url-regex)。你甚至可以编写一个用于确定给定数字是否 [是质数](https://www.noulakaz.net/2007/03/18/a-regular-expression-to-check-for-prime-numbers/) 的正则表达式。

正则表达式因难以编写正确而臭名昭著，但它们也是你工具箱中非常有用的工具！

## 回到数据整理

好的，现在我们有了

```bash
ssh myserver journalctl
 | grep sshd
 | grep "Disconnected from"
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'
```

我们完全可以只用 `sed` 来完成，但为什么呢？就是为了好玩。

```bash
ssh myserver journalctl
 | sed -E
   -e '/Disconnected from/!d'
   -e 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'
```

这展示了 `sed` 的一些功能。`sed` 还可以注入文本（使用 `i` 命令）、显式打印行（使用 `p` 命令）、按索引选择行等等。查看 `man sed`！

不管怎样。现在我们得到了尝试登录的所有用户名列表。但这相当没有帮助。让我们寻找常见的用户名：

```bash
ssh myserver journalctl
 | grep sshd
 | grep "Disconnected from"
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'
 | sort | uniq -c
```

`sort` 将对其输入进行排序。`uniq -c` 将连续出现的相同行合并为一行，并在前面加上出现次数的计数。我们可能也想对其进行排序，并且只保留最常见的登录：

```bash
ssh myserver journalctl
 | grep sshd
 | grep "Disconnected from"
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'
 | sort | uniq -c
 | sort -nk1,1 | tail -n10
```

`sort -n` 将按照数字（而不是词典顺序）排序。`-k1,1` 表示“仅按第一个以空格分隔的列排序”。`,n` 部分表示“排序直到第 `n` 个字段，其中默认值是行的末尾。在这 **特定** 示例中，按整行排序并不重要，但我们在这里是为了学习！

如果我们想要 **最** 不常见的用户名，我们可以使用 `head` 而不是 `tail`。还有 `sort -r`，它以相反的顺序排序。

好的，这很酷，但我们有点希望只提供用户名，也许不是每行一个？

```bash
ssh myserver journalctl
 | grep sshd
 | grep "Disconnected from"
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'
 | sort | uniq -c
 | sort -nk1,1 | tail -n10
 | awk '{print $2}' | paste -sd,
```

让我们从 `paste` 开始：它允许您通过给定的单个字符分隔符（`-d`）将行（`-s`）合并在一起。但是这个 `awk` 是什么？

## awk——另一个编辑器

`awk` 是一种编程语言，恰好非常擅长处理文本流。如果您想要学会它，关于 `awk` 有 **很多** 要说的，但和其他很多东西一样，我们只会介绍基础知识。

首先，`{print $2}` 做什么？嗯，`awk` 程序的形式是可选模式加上一个块，说出如果模式与给定行匹配则要做什么。默认模式（我们上面用过的）匹配所有行。在块内，`$0` 被设置为整行的内容，`$1` 到 `$n` 被设置为该行的第 `n` 个字段，当用 `awk` 字段分隔符（默认为空白字符，可以用 `-F` 更改）分隔时。在这种情况下，我们说，对于每一行，打印第二个字段的内容，这恰好是用户名！

让我们看看是否能做点更高级的东西。让我们计算以 `c` 开头且以 `e` 结尾的一次性用户名的数量：

```bash
 | awk '$1 == 1 && $2 ~ /^c[^ ]*e$/ { print $2 }' | wc -l
```

这里有很多要理解的地方。首先，注意我们现在有一个模式（在 `{...}` 之前的内容）。模式表示行的第一个字段应该等于 1（这是 `uniq -c` 的计数），并且第二个字段应该匹配给定的正则表达式。块只是说要打印用户名。然后我们用 `wc -l` 计算输出中的行数。

但是，`awk` 是一种编程语言，记得吗？

```awk
BEGIN { rows = 0 }
$1 == 1 && $2 ~ /^c[^ ]*e$/ { rows += $1 }
END { print rows }
```

`BEGIN` 是一个匹配输入开头的模式（`END` 匹配结尾）。现在，每行的块只是将第一个字段的计数添加起来（虽然在这种情况下它总是为 1），然后我们在最后打印它出来。实际上，我们 **可以** 完全摆脱 `grep` 和 `sed`，因为 `awk` [可以做到](https://backreference.org/2010/02/10/idiomatic-awk/)所有这些，但我们将这留给读者作为一个练习。

## 数据分析

你可以进行数学运算！

```bash
 | paste -sd+ | bc -l
```

```bash
echo "2*($(data | paste -sd+))" | bc -l
```

您可以以多种方式获取统计信息。
[`st`](https://github.com/nferraz/st) 非常不错，但如果您已经有 R：

```bash
ssh myserver journalctl
 | grep sshd
 | grep "Disconnected from"
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'
 | sort | uniq -c
 | awk '{print $1}' | R --slave -e 'x <- scan(file="stdin", quiet=TRUE); summary(x)'
```

R 是另一种（奇怪的）编程语言，非常擅长数据分析和[绘图](https://ggplot2.tidyverse.org/)。我们不会详细讨论，但可以说 `summary` 打印了关于矩阵的摘要统计信息，我们从输入流中计算出一个矩阵，因此 R 给出了我们想要的统计信息！

如果你只想进行简单的绘图，`gnuplot` 是你的好朋友：

```bash
ssh myserver journalctl
 | grep sshd
 | grep "Disconnected from"
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'
 | sort | uniq -c
 | sort -nk1,1 | tail -n10
 | gnuplot -p -e 'set boxwidth 0.5; plot "-" using 1:xtic(2) with boxes'
```

## 数据处理以生成参数

有时，您想要对数据进行处理，以查找基于某个更长列表的安装或删除内容。到目前为止，我们所讨论的数据处理 + `xargs` 可以是一个强大的组合：

```bash
rustup toolchain list | grep nightly | grep -vE "nightly-x86|01-17" | sed 's/-x86.*//' | xargs rustup toolchain uninstall
```

## 练习

1. 如果您不熟悉正则表达式，[这里](https://regexone.com/)有一个简短的交互式教程，涵盖了大部分基础知识
1. `sed s/REGEX/SUBSTITUTION/g` 和普通的 `sed` 有什么不同？ `/I` 或 `/m` 呢？
1. 要进行原位替换，很容易诱惑地执行类似 `sed s/REGEX/SUBSTITUTION/ input.txt > input.txt` 的操作。但这是一个坏主意，为什么？这是否特定于 `sed`？
1. 在您熟悉的语言中使用正则表达式实现一个简单的 grep 等效工具。如果您希望输出具有类似 grep 的颜色高亮显示，则搜索 ANSI 颜色转义序列。
1. 有时，一些操作（如重命名文件）可能使用原始命令（如 `mv`）会很棘手。`rename` 是一个聪明的工具，可以实现此目的，并具有类似于 sed 的语法。尝试创建一堆带有空格的文件名，并使用 `rename` 将它们替换为下划线。
1. 查找在过去的三次重启之间**不共享**的启动消息（请参阅 `journalctl` 的 `-b` 标志）。您可能希望将所有引导日志混合在一个单独的文件中，因为这样可能会更容易一些。
1. 使用消息的时间戳
   ```
   Logs begin at ...
   ```
   和
   ```
   systemd[577]: Startup finished in...
   ```
   产生您的系统启动时间的一些统计信息。
1. 找到至少包含三个 `a` 且没有以 `'s` 结尾的单词（在 `/usr/share/dict/words` 中）。这些单词的最后两个字母中最常见的是什么？`sed` 的 `y` 命令或 `tr` 程序可能会帮助您进行大小写不敏感的匹配。这些两个字母组合有多少？作为挑战：哪些组合不存在？
1. 查找一个在线数据集，例如[这个](https://stats.wikimedia.org/EN/TablesWikipediaZZ.htm)或[这个](https://ucr.fbi.gov/crime-in-the-u.s/2016/crime-in-the-u.s.-2016/topic-pages/tables/table-1)。也许还有其他的[这里](https://www.springboard.com/blog/data-science/free-public-data-sets-data-science-project/)。使用 `curl` 获取它，并提取出只有两列数值数据。如果您正在获取 HTML 数据，可能会有帮助的 [`pup`](https://github.com/EricChiang/pup)。对于 JSON 数据，尝试 [`jq`](https://stedolan.github.io/jq/)。在单个命令中找到一列的最小值和最大值，并找到两列之间差值的总和。
