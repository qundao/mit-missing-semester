---
layout: lecture
title: "点文件（Dotfiles）"
presenter: Anish
video:
  aspect: 62.5
  id: YSZBWWJw3mI
---

<iframe src="https://www.youtube.com/embed/YSZBWWJw3mI" frameborder="0" allowfullscreen></iframe>

许多程序都是使用称为“点文件”（因为文件名以 `.` 开头，例如 `~/.gitconfig`，因此它们默认在目录列表 `ls` 中被隐藏）的纯文本文件进行配置。

您使用的许多工具可能有许多可以精细调整的设置。通常情况下，工具是使用专门的语言进行定制，例如 Vim 的 Vimscript 或 shell 的自己语言。

定制和调整您的工具以适应您的首选工作流程将使您更加高效。我们建议您花时间自定义您的工具，而不是从 GitHub 克隆别人的点文件。

您可能已经设置了一些点文件。一些查找点文件的位置：

- `~/.bashrc`
- `~/.emacs`
- `~/.vim`
- `~/.gitconfig`

一些程序不会直接将文件放在您的主文件夹下，而是将它们放在 `~/.config` 文件夹下。

点文件不仅限于命令行应用程序，例如 [MPV](https://mpv.io/) 视频播放器可以通过编辑 `~/.config/mpv` 下的文件进行配置。

## 学习定制工具

您可以通过阅读在线文档或 [man 页面](https://en.wikipedia.org/wiki/Man_page) 来了解您的工具设置。另一个很好的方法是在互联网上搜索关于特定程序的博客文章，作者会告诉您他们的首选定制方式。了解定制的另一种方法是查看其他人的点文件：您可以在 GitHub 上找到大量的 [点文件存储库](https://github.com/search?o=desc&q=dotfiles&s=stars&type=Repositories)——在这里看到最受欢迎的一个 [here](https://github.com/mathiasbynens/dotfiles)（我们建议您不要盲目复制配置）。

## 组织

您应该如何组织您的点文件？它们应该放在自己的文件夹中，进行版本控制，并使用脚本进行**符号链接**。这有以下好处：

- **简单安装**：如果您登录到一个新的机器，应用您的定制将只需要一分钟
- **可移植性**：您的工具将在任何地方都以相同的方式工作
- **同步**：您可以在任何地方更新您的点文件并保持它们同步
- **更改跟踪**：您可能会在整个编程生涯中维护您的点文件，长期项目具有版本历史记录是很好的

```shell
cd ~/src
mkdir dotfiles
cd dotfiles
git init
touch bashrc
## 创建一个bashrc文件并包括一些配置，比如：
##     PS1='\w > '
touch install
chmod +x install
## 将下面的内容插入到安装脚本中:
##     #!/usr/bin/env bash
##     BASEDIR=$(dirname $0)
##     cd $BASEDIR
#
##     ln -s ${PWD}/bashrc ~/.bashrc
git add bashrc install
git commit -m 'Initial commit'
```

## 高级主题

### 特定于机器的定制

大多数情况下，您希望在不同机器上拥有相同的配置，但有时，您可能希望在特定机器上有一些微小的差异。以下是几种处理此情况的方法：

### 每台机器一个分支

使用版本控制来维护每台机器一个分支。这种方法在逻辑上很直接，但可能会比较繁重。

### If 语句

如果配置文件支持，使用类似于 if 语句的等价物来应用特定于机器的定制。例如，您的 shell 可以有类似以下的内容：

```shell
if [[ "$(uname)" == "Linux" ]]; then {do_something else}; fi

## Darwin是macOS系统的架构名称
if [[ "$(uname)" == "Darwin" ]]; then {do_something}; fi

## 您也可以使其特定于机器
if [[ "$(hostname)" == "myServer" ]]; then {do_something}; fi
```

### 包含

如果配置文件支持，可以使用包含功能。例如，`~/.gitconfig` 可以有一个设置：

```gitignore
[include]
    path = ~/.gitconfig_local
```

然后在每台机器上，`~/.gitconfig_local` 可以包含特定于机器的设置。您甚至可以在一个单独的存储库中跟踪这些特定于机器的设置。

如果您希望不同程序共享一些配置，这个想法也很有用。例如，如果您希望 `bash` 和 `zsh` 共享相同的一组别名，您可以将它们写在 `.aliases` 下，并在两者中都添加以下块。

这个想法也很有用，如果您希望不同的程序共享一些配置。例如，如果您希望 `bash` 和 `zsh` 共享相同的一组别名，您可以将它们写在 `.aliases` 下，并在两者中都添加以下块。

```bash
## Test if ~/.aliases exists and source it
if [ -f ~/.aliases ]; then
    source ~/.aliases
fi
```

## 资源

- 您的教练的点文件：
  [Anish](https://github.com/anishathalye/dotfiles),
  [Jon](https://github.com/jonhoo/configs),
  [Jose](https://github.com/jjgo/dotfiles)
- [GitHub 上的点文件](http://dotfiles.github.io/)：点文件框架、实用工具、示例和教程
- [Shell 启动脚本](https://blog.flowblok.id.au/2013-02/shell-startup-scripts.html)：解释了用于您的 shell 的不同配置文件

## 练习

1. 为您的点文件创建一个文件夹，并设置 [版本控制](version-control.md)。

2. 为至少一个程序添加配置，例如您的 shell，并进行一些定制（开始时，可以简单地通过设置 `$PS1` 来定制您的 shell 提示符）。

3. 设置一种方法，可以快速地（并且不需要手动操作）在新机器上安装您的点文件。这可以是一个简单的 shell 脚本，每个文件调用 `ln -s`，或者您可以使用一个 [专门的实用工具](http://dotfiles.github.io/utilities/)。

4. 在一个全新的虚拟机上测试您的安装脚本。

5. 将您当前所有工具的配置迁移到您的点文件存储库。

6. 在 GitHub 上发布您的点文件。
