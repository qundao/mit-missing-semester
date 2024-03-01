---
layout: lecture
title: "自动化"
presenter: Jose
video:
  aspect: 56.25
  id: BaLlAaHz-1k
---

<iframe src="https://www.youtube.com/embed/BaLlAaHz-1k" frameborder="0" allowfullscreen></iframe>

有时您会编写一个执行某些操作的脚本，但希望它定期运行，比如备份任务。您可以编写一个临时的解决方案，让其在后台运行，并定期上线。然而，大多数 UNIX 系统都配备了 cron 守护进程，它可以根据简单的规则每分钟运行一次任务。

在大多数 UNIX 系统上，cron 守护进程 `crond` 默认情况下都会运行，但您可以随时使用 `ps aux | grep crond` 命令进行检查。

## crontab

cron 的配置文件可以通过运行 `crontab -l` 进行显示，通过运行 `crontab -e` 进行编辑。cron 使用的时间格式由五个以空格分隔的字段以及用户和命令组成：

- **分钟**（minute） - 命令将在小时内的哪一分钟运行，取值范围为 '0' 到 '59'
- **小时**（hour） - 控制命令将在哪个小时运行，使用 24 小时制，值必须在 0 到 23 之间（0 代表午夜）
- **月内日期**（dom） - 这是您希望命令在月份中运行的日期，例如，要在每个月的 19 日运行命令，则 dom 为 19。
- **月份**（month） - 这是指定命令将在其运行的月份，可以使用数字（0-12）或月份名称（例如，May）来表示
- **周内星期**（dow） - 这是您希望命令在周几运行，也可以是数字（0-7）或星期几的名称（例如，sun）。
- **用户**（user） - 这是运行命令的用户。
- **命令**（command） - 这是要运行的命令。该字段可以包含多个单词或空格。

注意，使用星号 `*` 表示所有，使用星号后跟斜杠和数字表示每第 n 个值。因此，`*/5` 表示每五个。一些示例是：

```shell
*/5   *    *   *   *       # 每五分钟
  0   *    *   *   *       # 每小时整点
  0   9    *   *   *       # 每天上午9点
  0   9-17 *   *   *       # 每天上午9点到下午5点之间每小时
  0   0    *   *   5       # 每个星期五凌晨12点
  0   0    1   */2 *       # 每隔一个月的第一天，凌晨12点

```

您可以在 [crontab.guru](https://crontab.guru/examples.html) 上找到更多常见 crontab 计划的示例。

## Shell 环境和日志记录

使用 cron 的一个常见陷阱是它不会加载像 `.bashrc`、`.zshrc` 等常见 shell 脚本中的相同环境脚本，并且默认情况下不会将输出记录到任何位置。再加上最大频率为一分钟，最初调试 cron 脚本可能会变得非常痛苦。

要解决环境问题，请确保在所有脚本中使用绝对路径，并修改您的环境变量，如 `PATH`，以便脚本可以成功运行。为了简化日志记录，一个好的建议是以如下格式编写您的 crontab：

```shell
* * * * *   用户  /路径/至/脚本/文件.sh >> /tmp/cron_every_minute.log 2>&1
```

并将脚本编写在单独的文件中。请记住，`>>` 表示追加到文件中，`2>&1` 将 `stderr` 重定向到 `stdout`（尽管您可能希望保持它们分开）。

## Anacron

使用 cron 的一个缺点是，如果计算机在 cron 脚本应该运行时处于关机或睡眠状态，则不会执行该脚本。对于频繁的任务，这可能没问题，但如果一个任务运行得不那么频繁，您可能希望确保它被执行。[anacron](https://linux.die.net/man/8/anacron) 与 `cron` 类似，但其频率是以天为单位指定的。与 cron 不同，它不假设计算机连续运行。因此，它可以用于不连续运行的机器，以控制日常、每周和每月的常规作业。

## 练习

1. 创建一个脚本，每分钟在您的下载文件夹中查找任何图片文件（您可以查看 [MIME 类型](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types) 或使用正则表达式匹配常见扩展名），然后将它们移动到您的图片文件夹中。

1. 编写一个 cron 脚本，每周检查您系统中过时的软件包，并提示您更新它们或自动更新它们。

<!-- {% comment %}

- [fswatch](https://github.com/emcrisostomo/fswatch)
- 图形化界面自动化 (pyautogui) [Automating the boring stuff Chapter 18](https://automatetheboringstuff.com/chapter18/)
- Ansible/puppet/chef

- https://xkcd.com/1205/
- https://xkcd.com/1319/

{% endcomment %} -->
