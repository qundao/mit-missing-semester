---
layout: lecture
title: "编辑器"
presenter: Anish
video:
  aspect: 62.5
  id: 1vLcusYSrI4
---

## 编辑器的重要性

作为程序员，我们大部分时间都在编辑纯文本文件。值得投入时间学习一个适合您需求的编辑器。

如何学习新的编辑器？您需要强迫自己使用该编辑器一段时间，即使这可能会暂时影响您的工作效率。很快您就会看到回报（两周的时间足以学会基础知识）。

我们将教您使用 Vim，但我们鼓励您尝试其他编辑器。这是一个非常个人化的选择，人们对此有着[强烈的意见](https://en.wikipedia.org/wiki/Editor_war)。

我们无法在 50 分钟内教会您如何使用强大的编辑器，因此我们将重点放在教您基础知识上，展示一些更高级的功能，并为您提供掌握工具的资源。我们将在 Vim 的上下文中教您课程，但大多数思想都可以转换到您使用的任何其他强大的编辑器上（如果不能，那么您可能不应该使用该编辑器！）。

![编辑器学习曲线](files/editor-learning-curves.jpg)

<!-- 来源: https://blogs.msdn.microsoft.com/steverowe/2004/11/17/code-editor-learning-curves/ -->

编辑器学习曲线图是一个谣言。学习使用强大编辑器的基础知识非常容易（尽管可能需要多年时间来掌握）。

今天哪些编辑器很受欢迎？参见这个[Stack Overflow 调查](https://insights.stackoverflow.com/survey/2018/#development-environments-and-tools)（由于 Stack Overflow 用户可能不代表整个程序员群体，可能存在一些偏见）。

## 命令行编辑器

即使您最终决定使用 GUI 编辑器，学习命令行编辑器也是值得的，因为它便于在远程机器上轻松编辑文件。

## Nano

Nano 是一个简单的命令行编辑器。

- 使用箭头键移动
- 所有其他快捷键（保存、退出）显示在底部

## Vim

Vi/Vim 是一个功能强大的文本编辑器。它是一个通常安装在所有地方的命令行程序，这使得在远程机器上编辑文件变得方便。

Vim 也有图形版本，比如 GVim 和
[MacVim](https://macvim-dev.github.io/macvim/)。这些提供了额外的功能，比如 24 位颜色、菜单和弹出窗口。

## Vim 的哲学

- 在编程时，您花费大部分时间是阅读/编辑，而不是写入
  - Vim 是一个 **模态** 编辑器：不同的模式用于插入文本和操作文本
- Vim 是可编程的（使用 Vimscript 和其他语言如 Python）
- Vim 的界面本身就像是一个编程语言
  - 按键（具有助记名称）是命令
  - 命令是可组合的
- 不要使用鼠标：太慢了
- 编辑器应该与您思考的速度一样快

## 初级 Vim

### 模式

Vim 在左下角显示当前模式。

- 普通模式：用于在文件中移动和进行编辑
  - 大部分时间都在这里
- 插入模式：用于插入文本
- 可视（字符、行或块）模式：用于选择文本块

您可以通过按 `<ESC>` 键从任何模式切换回普通模式。从普通模式进入插入模式使用 `i`，进入可视模式使用 `v`，进入可视行模式使用 `V`，进入可视块模式使用 `<C-v>`。

在使用 Vim 时，您会频繁使用 `<ESC>` 键：考虑将 Caps Lock 键重新映射为 Escape。

### 基础知识

Vim ex 命令通过普通模式中的 `:{command}` 发出。

- `:q` 退出（关闭窗口）
- `:w` 保存
- `:wq` 保存并退出
- `:e {文件名}` 打开文件进行编辑
- `:ls` 显示打开的缓冲区
- `:help {主题}` 打开帮助
  - `:help :w` 打开 `:w` ex 命令的帮助
  - `:help w` 打开 `w` 移动的帮助

### 移动

Vim 以高效的移动为特点。在正常模式下导航文件。

- 禁用箭头键以避免养成不良习惯
  \```vim
  nnoremap <Left> :echoe "Use h"<CR>
  nnoremap <Right> :echoe "Use l"<CR>
  nnoremap <Up> :echoe "Use k"<CR>
  nnoremap <Down> :echoe "Use j"<CR>
  \```
- 基本移动：`hjkl`（左、下、上、右）
- 单词：`w`（下一个单词）、`b`（单词开头）、`e`（单词结尾）
- 行：`0`（行首）、`^`（第一个非空字符）、`$`（行尾）
- 屏幕：`H`（屏幕顶部）、`M`（屏幕中部）、`L`（屏幕底部）
- 文件：`gg`（文件开头）、`G`（文件结尾）
- 行号：`:{number}<CR>` 或 `{number}G`（第 {number} 行）
- 其他：`%`（对应项）
- 查找：`f{character}`、`t{character}`、`F{character}`、`T{character}`
  - 在当前行查找/向前/向后 {character}
- 重复 N 次：`{number}{movement}`，例如 `10j` 向下移动 10 行
- 搜索：`/{regex}`，`n` / `N` 用于导航匹配项

### 选择

视觉模式：

- 可视模式
- 可视行模式
- 可视块模式

可以使用移动键进行选择。

### 操作文本

现在你用键盘（和强大的、可组合的命令）做的一切，都曾经是用鼠标完成的。

- `i` 进入插入模式
  - 但是为了操作/删除文本，想要使用比退格键更强大的东西
- `o` / `O` 在下方 / 上方插入行
- `d{motion}` 删除 {motion}
  - 例如 `dw` 是删除单词，`d$` 是删除至行尾，`d0` 是删除至行首
- `c{motion}` 更改 {motion}
  - 例如 `cw` 是更改单词
  - 类似于 `d{motion}` 后跟 `i`
- `x` 删除字符（等同于 `dl`）
- `s` 替换字符（等同于 `xi`）
- 可视模式 + 操作
  - 选择文本，`d` 删除它或 `c` 更改它
- `u` 撤销，`<C-r>` 重做
- 还有很多要学习的：例如 `~` 翻转字符的大小写

### 资源

- `vimtutor` 命令行程序，用于教授 Vim
- [Vim 冒险游戏](https://vim-adventures.com/)，帮助学习 Vim

## 自定义 Vim

Vim 通过纯文本配置文件 `~/.vimrc` 进行自定义（包含 Vimscript 命令）。你可能想要打开很多基本设置。

查看 GitHub 上其他人的配置文件，以获取灵感，但尽量不要直接复制其他人的完整配置。阅读理解并按需采用。

一些可考虑的自定义设置包括：

- 语法高亮：`syntax on`
- 配色方案
- 行号：`set nu` / `set rnu`
- 通过所有内容进行退格：`set backspace=indent,eol,start`

## 高级 Vim

以下是一些示例，展示了编辑器的强大功能。我们无法教授所有这些功能，但你将在实践中逐渐学会。一个好的启发是：每当你使用编辑器时想到“一定有更好的方法”，那么很可能确实有：上网搜索一下。

### 搜索和替换

`:s`（substitute）命令（[文档](http://vim.wikia.com/wiki/Search_and_replace)）。

- `%s/foo/bar/g`
  - 在文件中全局替换 foo 为 bar
- `%s/\[.*\](\(.*\))/\1/g`
  - 替换命名的 Markdown 链接为普通的 URL

### 多窗口

- 使用 `sp` / `vsp` 进行窗口分割
- 可以有同一缓冲区的多个视图。

### 鼠标支持

- `set mouse+=a`
  - 可以点击、滚动选择

### 宏

- `q{字符}` 开始在寄存器 `{字符}` 中记录宏
- `q` 停止记录
- `@{字符}` 重放宏
- 宏执行遇到错误时停止
- `{数字}@{字符}` 执行宏 {数字} 次
- 宏可以递归
  - 首先使用 `q{字符}q` 清除宏
  - 记录宏，在录制宏完成之前使用 `@{字符}` 递归调用宏（在录制完成之前将是一个空操作）
- 示例：将 XML 转换为 JSON（[文件](files/example-data.xml)）
  - 对象数组，具有键 "name" / "email"
  - 使用 Python 程序？
  - 使用 sed / 正则表达式
    - `g/people/d`
    - `%s/<person>/{/g`
    - `%s/<name>\(.*\)<\/name>/"name": "\1",/g`
    - ...
  - Vim 命令 / 宏
    - `Gdd`, `ggdd` 删除第一行和最后一行
    - 格式化单个元素的宏（寄存器 `e`）
      - 转到带有 `<name>` 的行
      - `qe^r"f>s": "<ESC>f<C"<ESC>q`
    - 格式化人的宏
      - 转到带有 `<person>` 的行
      - `qpS{<ESC>j@eA,<ESC>j@ejS},<ESC>q`
    - 格式化人并转到下一个人的宏
      - 转到带有 `<person>` 的行
      - `qq@pjq`
    - 执行宏直到文件末尾
      - `999@q`
    - 手动删除最后一个 `,` 并添加 `[` 和 `]` 分隔符

## 扩展 Vim

有大量的插件可用于扩展 vim。

首先，使用插件管理器进行设置，如
[vim-plug](https://github.com/junegunn/vim-plug)、
[Vundle](https://github.com/VundleVim/Vundle.vim) 或
[pathogen.vim](https://github.com/tpope/vim-pathogen)。

一些可考虑的插件：

- [ctrlp.vim](https://github.com/kien/ctrlp.vim)：模糊文件查找
- [vim-fugitive](https://github.com/tpope/vim-fugitive)：git 集成
- [vim-surround](https://github.com/tpope/vim-surround)：修改 "surroundings"
- [gundo.vim](https://github.com/sjl/gundo.vim)：导航撤销树
- [nerdtree](https://github.com/scrooloose/nerdtree)：文件资源管理器
- [syntastic](https://github.com/vim-syntastic/syntastic)：语法检查
- [vim-easymotion](https://github.com/easymotion/vim-easymotion)：魔术移动
- [vim-over](https://github.com/osyo-manga/vim-over)：替换预览

插件列表：

- [Vim Awesome](https://vimawesome.com/)

## 其他程序中的 Vim 模式

对于许多流行的编辑器（例如 vim 和 emacs），许多其他工具支持编辑器仿真。

- Shell
  - bash：`set -o vi`
  - zsh：`bindkey -v`
  - `export EDITOR=vim`（由诸如 `git` 之类的程序使用的环境变量）
- `~/.inputrc`
  - `set editing-mode vi`

甚至还有针对 Web [浏览器](http://vim.wikia.com/wiki/Vim_key_bindings_for_web_browsers)，一些流行的浏览器扩展有 [Vimium](https://chrome.google.com/webstore/detail/vimium/dbepggeogbaibhgnhhndojpepiihcmeb?hl=en)（适用于 Google Chrome）和 [Tridactyl](https://github.com/tridactyl/tridactyl)（适用于 Firefox）。

## 资源

- [Vim 技巧 Wiki](http://vim.wikia.com/wiki/Vim_Tips_Wiki)
- [Vim 节日日历](https://vimways.org/2018/)：各种 Vim 技巧
- [Neovim](https://neovim.io/) 是一个现代化的 Vim 重新实现，具有更积极的开发。
- [Vim Golf](http://www.vimgolf.com/)：各种 Vim 挑战

<!--
{% comment %}
## 资源

其他编辑器的资源？
{% endcomment %} -->

## 练习

1. 尝试一些编辑器。至少尝试一个命令行编辑器（例如 Vim）和一个图形界面编辑器（例如 Atom）。通过像 `vimtutor` 这样的教程学习（或其他编辑器的等价物）。要真正了解新编辑器的感觉，承诺在几天内专门使用它，继续你的工作。

1. 自定义你的编辑器。在网上查找技巧和窍门，并查看其他人的配置（通常文档齐全）。

1. 尝试使用编辑器的插件。

1. 至少承诺使用一个强大的编辑器几周：到那时你应该会看到收益。在某个时候，你应该能够让你的编辑器工作得和你想的一样快。

1. 安装一个 linter（例如 Python 的 pyflakes），将其链接到你的编辑器并测试它是否正常工作。
