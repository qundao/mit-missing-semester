---
layout: lecture
title: "远程机器"
presenter: Jose
video:
  aspect: 62.5
  id: X5c2Y8BCowM
---

现在，程序员在日常工作中越来越常使用远程服务器。如果您需要使用远程服务器来部署后端软件，或者需要具有更高计算能力的服务器，您将不得不使用安全外壳协议（SSH）。与大多数涉及的工具一样，SSH 是高度可配置的，因此值得学习。

## 执行命令

`ssh` 的一个经常被忽视的功能是直接运行命令。

- `ssh foobar@server ls` 将在 foobar 的主文件夹中执行 ls。
- 它可以与管道一起使用，因此 `ssh foobar@server ls | grep PATTERN` 将在本地搜索 `ls` 的远程输出，并且 `ls | ssh foobar@server grep PATTERN` 将在远程搜索 `ls` 的本地输出。

## SSH 密钥

基于密钥的身份验证利用公钥加密技术向服务器证明客户端拥有秘密私钥，而不会透露密钥。这样，您就不需要每次都重新输入密码了。然而，私钥（例如 `~/.ssh/id_rsa`）实际上就是您的密码，所以请像对待密码一样对待它。

- 密钥生成。要生成一对密钥，您可以简单地运行 `ssh-keygen -t rsa -b 4096`。如果您不选择密码，任何获取您私钥的人都将能够访问已授权的服务器，因此建议您选择一个密码，并使用 `ssh-agent` 来管理 shell 会话。

如果您已经配置了使用 SSH 密钥推送到 GitHub，您可能已经按照[此处](https://help.github.com/articles/connecting-to-github-with-ssh/)概述的步骤操作，并且已经有了有效的密钥对。要检查是否设置了密码并进行验证，您可以运行 `ssh-keygen -y -f /path/to/key`。

- 基于密钥的身份验证。`ssh` 将查看 `.ssh/authorized_keys` 文件以确定应该允许进入的客户端。要复制一个公钥，我们可以使用

```bash
cat .ssh/id_dsa.pub | ssh foobar@remote 'cat >> ~/.ssh/authorized_keys'
```

可以通过 `ssh-copy-id` 在可用时实现更简单的解决方案。

```bash
ssh-copy-id -i .ssh/id_dsa.pub foobar@remote
```

## 通过 SSH 复制文件

有许多方法可以通过 SSH 复制文件：

- `ssh+tee`，最简单的方法是使用 `ssh` 命令执行和 stdin 输入，例如 `cat localfile | ssh remote_server tee serverfile`
- `scp` 当需要复制大量文件/目录时，使用安全复制命令 `scp` 更方便，因为它可以轻松递归遍历路径。语法为 `scp path/to/local_file remote_host:path/to/remote_file`
- `rsync` 通过检测本地和远程相同文件并防止再次复制来改进 `scp`。它还提供了更细粒度的对符号链接、权限的控制，并具有额外功能，例如 `--partial` 标志，可以从先前中断的复制中恢复。`rsync` 的语法与 `scp` 类似。

## 后台进程

默认情况下，当中断 SSH 连接时，父 shell 的子进程也会被终止。有几种替代方法

- `nohup` - `nohup` 工具有效地允许进程在终端被终止时继续运行。尽管有时可以使用 `&` 和 `disown` 实现这一点，但 nohup 是一个更好的默认选择。更多细节可以在[这里](https://unix.stackexchange.com/questions/3886/difference-between-nohup-disown-and)找到。

- `tmux`，`screen` - 虽然 `nohup` 有效地将进程放入后台，但对于交互式 shell 会话来说并不方便。在这种情况下，使用终端复用器如 `screen` 或 `tmux` 是一个方便的选择，因为可以轻松地分离和重新附加关联的 shell。

最后，如果您放弃了一个程序并希望重新将其附加到当前终端，则可以查看 [reptyr](https://github.com/nelhage/reptyr)。`reptyr PID` 将抓取具有 ID 为 PID 的进程，并将其附加到当前终端。

## 端口转发

在许多情况下，您将遇到通过监听机器端口来运行的软件。当这种情况发生在您的本地机器上时，您可以简单地执行 `localhost:PORT` 或 `127.0.0.1:PORT`，但是对于没有直接通过网络/互联网提供其端口的远程服务器该怎么办呢？这称为端口转发，有两种类型：本地端口转发和远程端口转发（有关更多详细信息，请参见下面的图片，图片来源于[此 SO 帖子](https://unix.stackexchange.com/questions/115897/whats-ssh-port-forwarding-and-whats-the-difference-between-ssh-local-and-remot)）。

**本地端口转发**
![Local Port Forwarding](https://i.stack.imgur.com/a28N8.png "Local Port Forwarding")

**远程端口转发**
![Remote Port Forwarding](https://i.stack.imgur.com/4iK3b.png "Remote Port Forwarding")

最常见的情况是本地端口转发，其中远程机器中的服务监听一个端口，而您希望将本地机器上的一个端口链接到远程端口。例如，如果我们在远程服务器上执行 `jupyter notebook`，它监听端口 `8888`。因此，要将其转发到本地端口 `9999`，我们将执行 `ssh -L 9999:localhost:8888 foobar@remote_server`，然后在本地机器上导航到 `locahost:9999`。

## 图形转发

有时仅仅转发端口是不够的，因为我们希望在服务器上运行基于 GUI 的程序。您可以始终使用远程桌面软件发送整个桌面环境（例如 RealVNC、Teamviewer 等选项）。但是对于单个 GUI 工具，SSH 提供了一个很好的替代方案：图形转发。

使用 `-X` 标志告诉 SSH 转发

对于可信的 X11 转发，可以使用 `-Y` 标志。

最后要注意的是，为了使其工作，服务器上的 `sshd_config` 必须具有以下选项

```bash
X11Forwarding yes
X11DisplayOffset 10
```

## 漫游

连接到远程服务器时常见的痛苦是由于关闭/睡眠计算机或更改网络而导致的断开连接。而且，如果一个连接具有显着的延迟，使用 SSH 可能会变得非常令人沮丧。 [Mosh](https://mosh.org/)，移动 shell，改进了 SSH，允许漫游连接，间歇性连接并提供智能本地回显。

Mosh 存在于所有常见的发行版和包管理器中。Mosh 需要服务器中运行着一个 SSH 服务器。您不需要是超级用户来安装 mosh，但它确实需要服务器中开放的端口 60000 到 60010（它们通常是开放的，因为它们不在特权范围内）。

`mosh` 的一个缺点是它不支持漫游端口/图形转发，因此如果您经常使用这些功能，`mosh` 将帮助不大。

## SSH 配置

#### 客户端

我们已经介绍了许多参数，我们可以传递。一个诱人的替代方法是创建外观类似于 `alias my_serer="ssh -X -i ~/.id_rsa -L 9999:localhost:8888 foobar@remote_server` 的 shell 别名，但是有一个更好的选择，使用 `~/.ssh/config`。

```bash
Host vm
    User foobar
    HostName 172.16.174.141
    Port 22
    IdentityFile ~/.ssh/id_rsa
    RemoteForward 9999 localhost:8888

# 配置也可以采用通配符
Host *.mit.edu
    User foobaz
```

使用 `~/.ssh/config` 文件而不是别名的一个额外优势是其他程序如 `scp`、`rsync`、`mosh` 等也能读取它，并将设置转换为相应的标志。

请注意，`~/.ssh/config` 文件可以被视为一个 dotfile，在一般情况下，将其与其他 dotfile 一起包含是可以接受的。但是，如果您将其公开，要考虑一下您可能向互联网上的陌生人提供的信息：您的服务器地址、您正在使用的用户、开放的端口等。这可能会促使某些类型的攻击，因此请慎重考虑分享您的 SSH 配置。

警告：永远不要将您的 RSA 密钥（`~/.ssh/id_rsa*`）包含在公共存储库中！

#### 服务器端

服务器端的配置通常在 `/etc/ssh/sshd_config` 中指定。在这里，您可以进行更改，如禁用密码身份验证、更改 SSH 端口、启用 X11 转发等。您可以按用户指定配置设置。

## 远程文件系统

有时将远程文件夹挂载为本地文件夹是很方便的。 [sshfs](https://github.com/libfuse/sshfs) 可以将远程服务器上的文件夹挂载到本地，然后您可以使用本地编辑器。

## 练习

1. 要使 SSH 工作，主机需要运行 SSH 服务器。在虚拟机中安装 SSH 服务器（例如 OpenSSH），以便您可以完成其他练习。要找出机器的 IP 是什么，请运行 `ip addr` 命令并查找 inet 字段（忽略 `127.0.0.1` 条目，该条目对应环回接口）。

1. 转到 `~/.ssh/`，检查那里是否有一对 SSH 密钥。如果没有，请使用 `ssh-keygen -t rsa -b 4096` 生成它们。建议您使用一个密码并使用 `ssh-agent`，更多信息请参见[这里](https://www.ssh.com/ssh/agent)。

1. 使用 `ssh-copy-id` 将密钥复制到您的虚拟机。测试一下是否可以无密码 SSH。然后，在服务器上编辑您的 `sshd_config`，通过编辑 `PasswordAuthentication` 的值来禁用密码身份验证。通过编辑 `PermitRootLogin` 的值来禁用 root 登录。

1. 编辑服务器上的 `sshd_config`，更改 SSH 端口，并检查是否仍然可以 SSH。如果您有一个面向公众的服务器，默认端口和仅密钥登录将大幅减少恶意攻击。

1. 在服务器/虚拟机中安装 mosh，建立连接，然后断开服务器/虚拟机的网络适配器。Mosh 能否正常恢复？

1. 本地端口转发的另一个用途是将某些主机隧道到服务器。如果您的网络过滤了一些网站，例如 `reddit.com`，您可以通过服务器进行隧道连接，操作如下：

   - 运行 `ssh remote_server -L 80:reddit.com:80`
   - 在 `/etc/hosts` 中将 `reddit.com` 和 `www.reddit.com` 设置为 `127.0.0.1`
   - 检查您是否通过服务器访问该网站
   - 如果不明显，请使用诸如[ipinfo.io](https://ipinfo.io/)之类的网站，它将根据您的主机公共 IP 更改。

1. 可以通过添加一些额外的标志轻松实现后台端口转发。了解一下 `ssh` 中 `-N` 和 `-f` 标志的作用，弄清楚像 `ssh -N -f -L 9999:localhost:8888 foobar@remote_server` 这样的命令是做什么的。

## 参考资料

- [SSH Hacks](http://matt.might.net/articles/ssh-hacks/)
- [Secure Secure Shell](https://stribika.github.io/2015/01/04/secure-secure-shell.html)

<!--
{% comment %}
讲义将在讲座开始前提供。
{% endcomment %} -->
