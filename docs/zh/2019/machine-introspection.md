---
layout: lecture
title: "机器自省"
presenter: Jon
video:
  aspect: 56.25
  id: eNYT2Oq3PF8
---

<iframe src="https://www.youtube.com/embed/eNYT2Oq3PF8" frameborder="0" allowfullscreen></iframe>

有时，计算机会表现不良。而且很多时候，您会想知道为什么。
让我们看看一些可以帮助您做到这一点的工具！

但首先，让我们确保您能够进行内省。通常，系统内省需要您具有某些特权，比如是某个组的成员（比如 `power` 组用于关机）。`root` 用户拥有最高特权；他们几乎可以做任何事情。您可以使用 `sudo` 以 `root` 用户身份运行命令（但要小心！）。

## 发生了什么？

如果出现了问题，首先要查看问题发生时周围发生了什么。为此，我们需要查看日志。

传统上，日志都存储在 `/var/log` 中，而且很多仍然如此。通常每个程序都有一个文件或文件夹。使用 `grep` 或 `less` 来查找其中的内容。

还有一个内核日志，您可以使用 `dmesg` 命令查看。这曾经是作为一个纯文本文件提供的，但如今您通常必须通过 `dmesg` 来获取它。

最后，有一个“系统日志”，它越来越成为所有日志消息的存储地点。在**大多数**，尽管不是所有的 Linux 系统上，这个日志由 `systemd` 管理，即“系统守护进程”，它控制所有在后台运行的服务（目前更多的功能）。如果您是 root 用户，或者属于 `admin` 或 `wheel` 组，可以通过不太方便的 `journalctl` 工具访问该日志。

对于 `journalctl`，您应特别注意以下标志：

- `-u UNIT`：仅显示与给定 systemd 服务相关的消息
- `--full`：不截断长行（最愚蠢的功能）
- `-b`：仅显示来自最新引导的消息（还可见 `-b -2`）
- `-n100`：仅显示最后 100 条记录

## 正在发生什么？

如果出现了问题，或者您只是想了解系统的运行情况，您可以使用多种工具来检查当前正在运行的系统：

首先，有 `top` 和改进版本 `htop`，它们会显示系统上当前正在运行的进程的各种统计信息。CPU 使用情况、内存使用情况、进程树等。有很多快捷键，但 `t` 对启用树视图特别有用。您还可以使用 `pstree`（+ `-p` 包括 PID）来查看进程树。如果您想了解这些程序正在做什么，通常需要跟踪它们的日志文件。`journalctl -f`、`dmesg -w` 和 `tail -f` 在这里是您的朋友。

有时，您想了解整个系统正在使用的资源更多一些。[`dstat`](http://dag.wiee.rs/home-made/dstat/) 对此非常好。它为许多不同的子系统提供实时资源指标，如 I/O、网络、CPU 利用率、上下文切换等。`man dstat` 是开始的地方。

如果您的磁盘空间不足，有两个主要的工具您需要了解：`df` 和 `du`。前者显示系统上所有分区的状态（尝试使用 `-h`），而后者则测量您提供的所有文件夹的大小，包括其中的内容（也可以查看 `-h` 和 `-s`）。

要查看您打开的网络连接，`ss` 是一个好方法。`ss -t` 将显示所有打开的 TCP 连接。`ss -tl` 将显示系统上所有监听（即服务器）端口。`-p` 还会包含使用该连接的进程，`-n` 将给出原始端口号。

## 系统配置

有**许多**配置系统的方法，但我们将介绍两种非常常见的方法：网络和服务。您系统上的大多数应用程序都会告诉您如何在它们的手册页中配置它们，通常涉及编辑 `/etc` 目录下的文件；系统配置目录。

如果您想配置网络，`ip` 命令可以让您做到这一点。它的参数采用稍微奇怪的形式，但 `ip help command` 可以帮助您走得很远。`ip addr` 显示有关您的网络接口及其配置（IP 地址等）的信息，而 `ip route` 显示网络流量如何路由到不同的网络主机。网络问题通常可以纯粹通过 `ip` 工具解决。还有 `iw` 用于管理无线网络接口。`ping` 是一个方便的工具，用于检查问题的严重程度。尝试对主机名（google.com）、外部 IP 地址（1.1.1.1）和内部 IP 地址（192.168.1.1 或默认网关）进行 ping。您还可能需要调整 `/etc/resolv.conf` 来检查您的 DNS 设置（主机名如何解析为 IP 地址）。

要配置服务，您现在基本上必须与 `systemd` 交互，无论好坏。您系统上的大多数服务都会有一个定义 systemd **单元** 的 systemd 服务文件。这些文件定义了服务启动时要运行的命令、如何停止它、日志记录位置等。它们通常阅读起来不会太糟糕，您可以在 `/usr/lib/systemd/system/` 中找到大多数文件。您还可以在 `/etc/systemd/system` 中自定义自己的文件。

一旦您有了一个 systemd 服务的想法，就可以使用 `systemctl` 命令与之交互。`systemctl enable UNIT` 将设置服务在启动时启动（`disable` 将其移除），`start`、`stop` 和 `restart` 将执行您预期的操作。如果出现问题，systemd 会让您知道，您可以使用 `journalctl -u UNIT` 来查看应用程序的日志。您还可以使用 `systemctl status` 查看所有系统服务的状态。如果您的启动感觉很慢，很可能是由于一些慢速服务，您可以使用 `systemd-analyze`（尝试使用 `blame`）来找出是哪些服务。

## 练习

- `locate`?
- `dmidecode`?
- `tcpdump`?
- `/boot`?
- `iptables`?
- `/proc`?
