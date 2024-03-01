---
layout: lecture
title: "命令行环境"
presenter: Jose
video:
  aspect: 62.5
  id: i0rf1gpKL1E
---

## 别名与函数

正如您所想象的那样，输入涉及许多标志或冗长选项的长命令可能会变得乏味。然而，大多数 Shell 支持**别名**。例如，在 bash 中，别名的结构如下（请注意`=`周围没有空格）：

```bash
alias 别名="要别名的命令"
```

<!-- 我们可以通过别名对我们的命令添加一些常用的标志，比如`alias ll=ls -ltAh`。别名也能组合使用  -->

别名具有许多便利功能：

```bash
# 别名可以总结好的默认标志
alias ll="ls -lh"

# 为常见命令节省大量输入
alias gc="git commit"

# 别名可以覆盖现有命令
alias mv="mv -i"
alias mkdir="mkdir -p"

# 别名可以组合
alias la="ls -A"
alias lla="la -l"

# 要忽略别名，请在其前面加上\
\ls
# 或者可以使用unalias禁用
unalias la
```

<!--
要摆脱别名，可以运行`unalias 别名`，或者在运行命令时在命令前面加上反斜杠`\别名`以忽略别名。当别名覆盖现有名称时，这是方便的。 -->

然而，在许多场景中，别名可能有限，特别是当您尝试将链命令连在一起以使用相同的参数时。另一种选择是**函数**，它们介于别名和自定义 Shell 脚本之间。

以下是一个示例函数，它创建一个目录并进入其中。

```bash
mcd () {
    mkdir -p $1
    cd $1
}
```

默认情况下，别名和函数在 Shell 会话中不会持久存在。要使别名持久化，您需要将其包含在 Shell 启动脚本文件中，例如`.bashrc`或`.zshrc`。我的建议是将它们单独写在一个`.alias`文件中，并从不同的 Shell 配置文件中`source`该文件。

<!-- 最后，如果您决定将任何工具的别名设置为“改进”版本，例如 `alias bat=cat`，那么知道您可以通过执行`\cat`告诉bash忽略别名，并通过执行`command cat`同时忽略别名和函数是很有用的。 -->

## Shell 与框架

在 Shell 和脚本编程中，我们介绍了`bash` Shell，因为它是迄今为止最常见的 Shell，大多数系统将其作为默认选项。然而，它并不是唯一的选择。

例如，`zsh` Shell 是`bash`的超集，并提供了许多方便的功能，例如：

- 更智能的通配符匹配，`**`
- 内联通配符扩展
- 拼写纠正
- 更好的选项补全/选择
- 路径扩展（`cd /u/lo/b`会扩展为`/usr/local/bin`）

此外，许多 Shell 可以通过**框架**进行改进，一些受欢迎的通用框架如 [prezto](https://github.com/sorin-ionescu/prezto) 或 [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh)，以及专注于特定功能的较小框架，例如 [zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting) 或 [zsh-history-substring-search](https://github.com/zsh-users/zsh-history-substring-search)。其他 Shell，如 [fish](https://fishshell.com/)，默认情况下包含许多这些用户友好的功能。其中一些功能包括：

- 右侧提示符
- 命令语法高亮显示
- 历史子字符串搜索
- 基于 man 页的标志补全
- 更智能的自动补全
- 提示符主题

值得注意的是，当使用这些框架时，如果它们运行的代码没有得到适当的优化，或者代码太多，您的 Shell 可能会开始变慢。您可以对其进行分析，并禁用您不经常使用或价值低于速度的功能。

## 终端仿真器和多路复用器

除了定制您的 Shell 外，值得花些时间来确定您选择的**终端仿真器**及其设置。市面上有许多终端仿真器（这里是一份[比较](https://anarc.at/blog/2018-04-12-terminal-emulators-1/)）。

由于您可能会在终端中花费数百至数千小时，因此查看其设置是值得的。您可能想要在终端中修改的一些方面包括：

- 字体选择
- 配色方案
- 快捷键
- 标签/窗格支持
- 滚动配置
- 性能（一些较新的终端仿真器如[Alacritty](https://github.com/jwilm/alacritty)提供了 GPU 加速）

值得一提的是**终端多路复用器**，例如 [tmux](https://github.com/tmux/tmux)。 `tmux` 允许您在多个 Shell 会话之间切换。它还支持附加和分离，这是一种非常常见的用例，当您在远程服务器上工作并希望保持 Shell 运行时，而无需担心放弃当前进程时（默认情况下，注销时会终止您的进程）。通过 `tmux`，您可以轻松地在复杂的终端布局中切换。与终端仿真器类似，`tmux` 支持通过编辑 `~/.tmux.conf` 文件进行大量定制。

## 命令行实用工具

大多数基于 UNIX 的操作系统默认都有的命令行实用工具已经足以满足您通常需要做的 99%的事情。

在接下来的几个小节中，我将介绍一些用于非常常见的 Shell 操作的替代工具，这些工具更方便使用。其中一些工具为命令添加了新的改进功能，而另一些则专注于提供更简单、更直观的界面，并具有更好的默认设置。

### `fasd` vs `cd`

即使有了改进的路径扩展和标签自动完成，更改目录仍然可能变得相当重复。[Fasd](https://github.com/clvv/fasd)（或[autojump](https://github.com/wting/autojump)）通过跟踪您最近和频繁访问的文件夹并执行模糊匹配来解决这个问题。

因此，如果我访问了路径 `/home/user/awesome_project/code`，运行 `z code` 将 `cd` 到该路径。如果我有多个名为 code 的文件夹，我可以通过运行 `z awe code` 来消除歧义，这将更接近匹配。与 autojump 不同，fasd 还提供了命令，而不是执行 `cd`，它只会扩展频繁和/或最近的文件、文件夹或两者。

### `bat` vs `cat`

尽管 `cat` 完成了它的工作，但 [bat](https://github.com/sharkdp/bat) 通过提供语法高亮、分页、行号和 Git 集成来改进它。

### `exa`/`ranger` vs `ls`

`ls` 是一个很好的命令，但其中一些默认设置可能令人讨厌，比如以原始字节显示大小。[exa](https://github.com/ogham/exa) 提供了更好的默认设置。

如果您需要导航许多文件夹和/或预览许多文件，[ranger](https://github.com/ranger/ranger) 可能比 `cd` 和 `cat` 更高效，因为它具有出色的界面。它非常可定制，通过正确的设置，甚至可以在终端中[预览图像](https://github.com/ranger/ranger/wiki/Image-Previews)。

### `fd` vs `find`

[fd](https://github.com/sharkdp/fd) 是 `find` 的一个简单、快速和用户友好的替代品。`find` 的默认设置（99% 的情况下您都希望使用 `--name` 标志）使它更容易在日常使用中使用。它也能识别 `git`，默认情况下会跳过您的 `.gitignore` 和 `.git` 文件夹中的文件。它还具有很好的默认颜色编码。

### `rg/fzf` vs `grep`

`grep` 是一个很棒的工具，但如果您想一次搜索多个文件，有更好的工具可以实现这个目的。[ack](https://github.com/beyondgrep/ack3)、[ag](https://github.com/ggreer/the_silver_searcher) 和 [rg](https://github.com/BurntSushi/ripgrep) 会递归搜索您当前目录的正则表达式模式，同时遵循您的 gitignore 规则。它们的工作方式都很相似，但我更喜欢 `rg`，因为它可以非常快速地搜索我的整个家目录。

同样，您可能会发现自己一遍又一遍地执行 `CMD | grep PATTERN`。[fzf](https://github.com/junegunn/fzf) 是一个命令行模糊查找器，可以让您交互地过滤几乎任何命令的输出。

### `rsync` vs `cp/scp`

虽然 `mv` 和 `scp` 对于大多数情况都很完美，但在复制/移动大量文件、大文件或一些数据已经存在于目标位置时，`rsync` 是一个巨大的改进。`rsync` 将跳过已经传输过的文件，并且通过 `--partial` 标志可以从先前中断的复制中恢复。

### `trash` vs `rm`

`rm` 是一个危险的命令，因为一旦删除文件就无法恢复。但是，现代操作系统在文件资源管理器中删除文件时不会像这样操作，它们只是将文件移动到垃圾箱文件夹，该文件夹会定期清除。

由于垃圾箱的管理方式因操作系统而异，因此没有单个的 CLI 实用程序。在 macOS 中有 [trash](https://hasseg.org/trash/)，在 Linux 中有 [trash-cli](https://github.com/andreafrancia/trash-cli/) 等。

### `mosh` vs `ssh`

`ssh` 是一个非常方便的工具，但如果您的连接速度很慢，延迟可能会变得很烦人，如果连接中断，您必须重新连接。[mosh](https://mosh.org/) 是一个方便的工具，它支持漫游，支持断断续续的连接，并提供智能本地回显。

### `tldr` vs `man`

您可以使用 `man` 和 `-h`/`--help` 标志大多数情况下了解命令做什么以及有哪些选项。但在某些情况下，如果详细了解它们可能会有点困难。

[tldr](https://github.com/tldr-pages/tldr) 命令是一个社区驱动的文档系统，可以从命令行中访问，它提供了命令的简单说明以及最常见的参数选项的几个简单示例。

### `aunpack` vs `tar/unzip/unrar`

正如 [这个 xkcd](https://xkcd.com/1168/) 所提到的，记住 `tar` 的选项可能会相当棘手，有时您需要一个完全不同的工具，比如对于 .rar 文件需要 `unrar`。
[atool](https://www.nongnu.org/atool/) 软件包提供了 `aunpack` 命令，它将找出正确的选项，并始终将提取的归档放入新文件夹中。

## 练习

1. 运行 `cat .bash_history | sort | uniq -c | sort -rn | head -n 10`（或者对于 zsh，运行 `cat .zhistory | sort | uniq -c | sort -rn | head -n 10`）以获取最常用的前 10 个命令，并考虑为它们编写更短的别名。

1. 选择一个终端仿真器，并弄清楚如何更改以下属性：

   - 字体选择
   - 颜色方案。标准方案有多少种颜色？为什么？
   - 滚动历史记录大小

1. 安装 `fasd` 或类似软件，并编写一个名为 `v` 的 bash/zsh 函数，对传递的参数执行模糊匹配，并打开最匹配的结果在您选择的编辑器中。然后，修改它，以便如果有多个匹配项，则可以使用 `fzf` 进行选择。

1. 由于 `fzf` 对执行模糊搜索非常方便，并且 shell 历史记录很容易受到这种搜索的影响，请调查如何将 `fzf` 绑定到 `^R`。您可以在 [这里](https://github.com/junegunn/fzf/wiki/Configuring-shell-key-bindings) 找到一些信息。

1. 在 `ack` 中，`--bar` 选项有什么作用？
