---
layout: lecture
title: "OS Customization"
presenter: Anish
video:
  aspect: 62.5
  id: epSRVqQzeDo
---

在设置菜单之外，您可以对操作系统进行大量定制。

## 键盘重映射

您的键盘可能有一些您很少使用的按键。您可以将这些无用的按键重映射为有用的功能。

### 重映射到其他按键

最简单的方法是将按键重映射到其他按键。例如，如果您很少使用大写锁定键，则可以将其重映射为更有用的功能。例如，如果您是 Vim 用户，您可能希望将大写锁定键重映射为 Escape 键。

在 macOS 上，您可以通过系统偏好设置中的键盘设置进行一些重映射；对于更复杂的映射，您需要特殊的软件。

### 重映射为任意命令

您不仅可以将按键重映射到其他按键：还有一些工具可以让您将按键（或按键组合）重映射为任意命令。例如，您可以使命令-Shift-T 打开一个新的终端窗口。

## 自定义隐藏的操作系统设置

### macOS

macOS 通过 `defaults` 命令暴露了许多有用的设置。例如，您可以使 Dock 中隐藏的应用程序图标半透明：

```shell
defaults write com.apple.dock showhidden -bool true
```

没有一个包含所有可能设置的单一列表，但您可以在网上找到特定自定义的列表，例如 Mathias Bynens 的 [.macos](https://github.com/mathiasbynens/dotfiles/blob/master/.macos)。

## 窗口管理

### 平铺式窗口管理

[平铺式窗口管理](https://en.wikipedia.org/wiki/Tiling_window_manager) 是窗口管理的一种方法，其中您将窗口组织成不重叠的框架。如果您使用的是基于 Unix 的操作系统，您可以安装一个平铺式窗口管理器；如果您使用的是类似于 Windows 或 macOS 的操作系统，您可以安装应用程序来模拟此行为。

### 屏幕管理

您可以设置键盘快捷键来帮助您在屏幕之间操作窗口。

### 布局

如果您在屏幕上有特定的窗口布局方式，而不是手动“执行”该布局，您可以编写脚本来简化布局的实例化过程。

## 资源

- [Hammerspoon](https://www.hammerspoon.org/) - macOS 桌面自动化
- [Rectangle](https://rectangleapp.com/) - macOS 窗口管理器
- [Karabiner](https://karabiner-elements.pqrs.org/) - 复杂的 macOS 键盘重映射
- [r/unixporn](https://www.reddit.com/r/unixporn/) - 人们花哨配置的截图和文档

## 练习

1. 找出如何将 Caps Lock 键重映射为您更常用的按键（例如 Escape、Ctrl 或 Backspace）。
2. 创建一个自定义的全局键盘快捷键，用于打开一个新的终端窗口或一个新的浏览器窗口。

<!-- {% comment %}

TODO

- [Bitbar](https://github.com/matryer/bitbar) / [Polybar](https://github.com/polybar/polybar)
- 剪贴板管理器（堆栈/可搜索历史记录）

{% endcomment %} -->
