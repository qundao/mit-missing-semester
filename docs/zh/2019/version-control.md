---
layout: lecture
title: "版本控制"
presenter: Jon
video:
  aspect: 56.25
  id: 3fig2Vz8QXs
---

<iframe src="https://www.youtube.com/embed/3fig2Vz8QXs" frameborder="0" allowfullscreen></iframe>

当你在处理随时间变化的东西时，能够跟踪这些变化是很有用的。这可能有很多原因：它为你提供了一条记录，说明了发生了什么变化，如何撤销它，谁改变了它，甚至可能是为什么改变了它。版本控制系统（VCS）提供了这种能力。它们允许你将对一组文件的更改提交，包括描述更改的消息，以及查看和撤消过去所做的更改。

大多数 VCS 支持在多个用户之间共享提交历史。这使得方便的协作成为可能：你可以看到我所做的更改，我也可以看到你所做的更改。由于 VCS 跟踪**变化**，它通常（虽然并不总是）可以弄清楚如何将我们的更改合并，只要它们触及相对分离的事物。

有[**很多**](https://en.wikipedia.org/wiki/Comparison_of_version-control_software)不同的 VCS 存在，它们在支持什么、功能如何以及你如何与它们交互方面存在很大差异。在这里，我们将专注于[git](https://git-scm.com/)，其中一个更常用的 VCS，但我建议你也看看[Mercurial](https://www.mercurial-scm.org/)。

话虽如此——来看看要点！

## git 是黑魔法吗？

并不完全是……你需要理解数据模型。
我们将跳过一些细节，但粗略地说，git 中的核心“事物”是一个提交。

- 每个提交都有一个唯一的名称，“修订哈希”
  长哈希，如 `998622294a6c520db718867354bf98348ae3c7e2`
  通常缩短为一个短（大致唯一的）前缀：`9986222`
- 提交有作者 + 提交消息
- 也有任何**祖先提交**的哈希
  通常只是前一个提交的哈希
- 提交还表示一种**差异**，一种表示如何从提交的祖先到提交的方法（例如，在这个文件中删除这一行，在这个文件中添加这些行，重命名那个文件等）
  - 实际上，git 存储完整的前后状态
  - 可能不希望存储大文件的更改！

最初，**存储库**（大致：git 管理的文件夹）没有内容，也没有提交。让我们来设置一下：

```console
$ git init hackers
$ cd hackers
$ git status
```

输出这里实际上给了我们一个很好的起点。让我们深入研究并确保我们理解了其中的所有内容。

首先，“On branch master”。

- 不想一直使用哈希值。
- 分支是指向哈希值的名称。
- master 传统上是“最新”提交的名称。
  每次进行新提交时，master 名称将被设置为指向新提交的哈希值。
- 特殊名称 `HEAD` 指的是“当前”名称
- 你也可以使用 `git branch`（或 `git tag`）创建自己的名称
  我们稍后会回到这个问题

让我们跳过“没有提交”因为这就是全部。

然后，“nothing to commit”。

- 每个提交包含了你所做的所有更改的差异。
  但是这个差异是如何构建的呢？
- **可以**总是提交自上次提交以来所做的所有更改
  - 有时你只想提交其中一些更改（例如，不是 `TODO`）
  - 有时你想将一个更改分成多个提交，以便为每个更改提供单独的提交消息
- git 允许你将更改进行**暂存**以构建一个提交
  - 使用 `git add` 将更改添加到已暂存的更改中的一个或多个文件
    - 使用 `git add -p` 仅添加文件中的一些更改
    - 没有参数的 `git add` 对“所有已知文件”进行操作
  - 使用 `git rm` 删除一个文件并将其删除添加到已暂存的更改中
  - 使用 `git reset` 清空已暂存更改的集合
    - 请注意，这不会更改任何文件！
      它**只是**表示不会将任何更改包含在提交中
    - 要仅删除一些已暂存的更改：
      `git reset FILE` 或 `git reset -p`
  - 使用 `git diff --staged` 检查已暂存的更改
  - 使用 `git diff` 查看剩余的更改
  - 当你对已暂存的更改满意时，使用 `git commit` 进行提交
    - 如果你只想提交**所有**更改：`git commit -a`
    - `git help add` 还有更多有用的信息

在你尝试以上操作时，尝试运行 `git status` 看看 git 认为你正在做什么——它出人意料地有帮助！

## 一个提交你说……

好的，我们有一个提交，接下来呢？

- 我们可以查看最近的更改：`git log`（或 `git log --oneline`）
- 我们可以查看完整的更改：`git log -p`
- 我们可以显示特定的提交：`git show master`
  - 或者带有 `-p` 以获得完整的差异/补丁
- 我们可以回到某个提交的状态使用 `git checkout NAME`
  - 如果 `NAME` 是一个提交哈希值，git 会说我们“游离”。这只是表示没有指向此提交的 `NAME`，因此如果我们进行提交，没有人会知道它们。
- 我们可以使用 `git revert NAME` 撤销一个更改
  - 将提交在 `NAME` 处的差异应用反向。
- 我们可以使用 `git diff NAME..` 将旧版本与此版本进行比较
  - `a..b` 是一个提交**范围**。如果有一个被省略，它表示 `HEAD`。
- 我们可以使用 `git log NAME..` 显示之间的所有提交
  - `-p` 在这里也有效
- 我们可以使用 `git reset NAME` 将 `master` 指向特定的提交（实际上撤消自那时以来的所有更改）：
  - 嗯，为什么？`reset` 不是用来更改暂存更改的吗？
    reset 有一个“第二”形式（参见 `git help reset`），它将 `HEAD` 设置为给定名称指向的提交。
  - 注意这并没有更改任何文件——`git diff` 现在有效地显示 `git diff NAME..`。

## 名字中有什么？

显然，在 git 中，名称很重要。它们是理解 git 中发生的**很多**事情的关键。到目前为止，我们已经谈论了提交哈希、master 和 `HEAD`。但还有更多！

- 你可以用 `git branch b` 创建你自己的分支（类似于 master）
  - 创建一个新名称 `b`，指向 `HEAD` 处的提交
  - 但你仍然在 master 上，所以如果你进行新提交，master 将指向该新提交，`b` 不会。
  - 使用 `git checkout b` 切换到一个分支
    - 你所做的任何提交现在都会更新 `b` 名称
    - 使用 `git checkout master` 切换回 master
      - 所有你在 `b` 中的更改都会被隐藏起来
    - 这是一个非常方便的方法，可以轻松地测试更改
- 标签是其他名称，永远不会更改，并且有它们自己的消息。通常用于标记发布和更改日志。
- `NAME^` 表示 “`NAME` 之前的提交”
  - 可以递归应用：`NAME^^^`
  - 当你使用 `~` 时，你**most likely** 想要的是 `~`
    - `~` 是“时间上的”，而 `^` 是按祖先的
    - `~~` 和 `^^` 是相同的
    - 使用 `~` 你也可以写 `X~3` 表示 “比 `X` 早 3 次提交”
    - 你不想要 `^3`
  - `git diff HEAD^`
- `-` 表示 “前一个名称”
- 大多数命令在没有给出其他参数的情况下操作 `HEAD`

## 清理你的混乱

你的提交历史很可能会以这种形式结束：

- `add feature x` —— 也许甚至有一个关于 `x` 的提交消息！
- `forgot to add file`
- `fix bug`
- `typo`
- `typo2`
- `actually fix`
- `actually actually fix`
- `tests pass`
- `fix example code`
- `typo`
- `x`
- `x`
- `x`
- `x`

在 git 看来这是**正常**的，但对于你未来的自己或对于对已发生更改感兴趣的其他人来说并不是很有帮助。git 让你清理这些东西：

- `git commit --amend`：将已暂存的更改合并到上一个提交中
  - 请注意，这**更改**了上一个提交，给它一个新的哈希值！
- `git rebase -i HEAD~13` 是**神奇**的。
  对于过去的 13 个提交中的每一个，选择要执行的操作：
  - 默认是 `pick`；什么也不做
  - `r`：更改提交消息
  - `e`：更改提交（添加或删除文件）
  - `s`：将提交与上一个提交合并并编辑提交消息
  - `f`："fixup" —— 将提交与上一个合并；丢弃提交消息
  - 最后，`HEAD` 被设置为现在的最后一个提交
  - 常被称为**合并**提交
  - 它实际上做了什么：回退 `HEAD` 到重新基础起点，然后按指示顺序重新应用提交。
- `git reset --hard NAME`：将所有文件的状态重置为 `NAME`（如果没有给出名称，则为 `HEAD`）的状态。对于撤消更改很方便。

## 与他人合作

版本控制的一个常见用例是允许多个人对一组文件进行更改，而不会互相干扰。或者说，确保如果他们互相干扰，他们不会只是默默地覆盖彼此的更改。

git 是一个**分布式**版本控制系统：每个人都有整个存储库的本地副本（好吧，是其他人选择发布的所有内容）。一些版本控制系统是**集中式**的（例如，subversion）：服务器拥有所有提交，客户端只有他们已经“检出”的文件。基本上，他们只有**当前**文件，并且需要向服务器询问是否需要其他任何内容。

每个 git 存储库的副本都可以列为“远程”。你可以使用 `git clone ADDRESS` 复制现有的 git 存储库（而不是 `git init`）。这将创建一个名为**origin**的远程，指向 `ADDRESS`。你可以使用 `git fetch REMOTE` 从远程获取名称和它们指向的提交。远程的所有名称都可以作为 `REMOTE/NAME` 对你可用，并且你可以像使用本地名称一样使用它们。

如果你有对远程的写入权限，你可以使用 `git push` 将名称在远程更改为指向你所做的提交。例如，让远程 `origin` 上的 master 名称（分支）指向我们当前的 master 分支指向的提交：

- `git push origin master:master`
- 为方便起见，你可以将 `origin/master` 设置为从当前分支 `git push` 时的默认目标
- 考虑一下这是什么？ `git push origin master:HEAD^`

通常你会使用 GitHub、GitLab、BitBucket 或其他东西作为你的远程。就 git 而言，这一点并没有什么“特别”的。这都只是名称和提交。如果有人对 master 进行了更改，并更新了 `github/master` 以指向他们的提交（我们稍后会回到这个问题），那么当你 `git fetch github` 时，你将能够使用 `git log github/master` 查看他们的更改。

## 与他人合作

到目前为止，分支似乎没什么用处：你可以创建它们，在上面工作，但是接下来呢？最终，你仍然会将 master 指向它们，对吗？

- 如果你在做一个大功能时需要修复一些东西怎么办？
- 如果其他人同时对 master 进行了更改怎么办？

不可避免地，你将不得不将一个分支中的更改与另一个分支中的更改**合并**在一起，无论这些更改是由你还是其他人进行的。git 允许你使用 `git merge NAME` 进行此操作。`merge` 将：

- 寻找 `HEAD` 和 `NAME` 共享的提交祖先的最新点（即它们分离的地方）
- （尝试）将所有这些更改应用到当前的 `HEAD`
- 生成一个包含所有这些更改的提交，并将 `HEAD` 和 `NAME` 都列为它的祖先
- 将 `HEAD` 设置为该提交的哈希

一旦你的大功能完成了，你可以将它的分支合并到 master 中，git 将确保你不会丢失任何分支的更改！

如果你之前使用过 git，你可能会通过一个不同的名称来识别 `merge`：`pull`。当你执行 `git pull REMOTE BRANCH` 时，这是：

- `git fetch REMOTE`
- `git merge REMOTE/BRANCH`
- 在这里，就像 `push` 一样，通常省略了 `REMOTE` 和 `BRANCH`，并使用“跟踪”远程分支（记得 `-u` 吗？）

这通常运行得**很好**。只要要合并的分支的更改是不重叠的。如果不是，你会得到一个**合并冲突**。听起来有点可怕……

- 合并冲突只是 git 告诉你它不知道最终的差异应该是什么
- git 暂停并要求你完成“合并提交”的暂存
- 在你的编辑器中打开冲突的文件，并查找大量的尖括号（`<<<<<<<`）。`=======` 上面的内容是自共享祖先提交以来在 `HEAD` 中所做的更改。下面的内容是自共享提交以来在 `NAME` 中所做的更改。
- `git mergetool` 非常方便——打开一个差异编辑器
- 一旦你通过弄清楚文件现在应该看起来像什么来**解决**冲突，使用 `git add` 暂存那些更改。
- 当所有冲突都解决后，用 `git commit` 完成
  - 你可以使用 `git merge --abort` 放弃

你刚刚解决了你的第一个 Git 合并冲突！\o/
现在你可以使用 `git push` 命令将你完成的更改发布出去。

## 当世界碰撞

当你`push`时，git 会检查如果你更新了要推送到的远程名称，是否会丢失其他人的工作。它通过检查远程名称的当前提交是否是你正在推送的提交的祖先来实现。如果是，git 可以安全地更新名称；这称为**快速前进**。如果不是，git 将拒绝更新远程名称，并告诉你已经有更改了。

如果你的推送被拒绝了，你该怎么办呢？

- 使用 `git pull` 将远程更改合并到本地（即 `fetch` + `merge`）
- 使用 `--force` 强制推送：这将丢失其他人的更改！
  - 还有 `--force-with-lease`，只有在你上次从该远程获取数据后，远程名称没有发生变化时，才会强制更改。这样更安全！
  - 如果你已经重写了之前推送的本地提交（“历史重写”；可能不要这样做），你将不得不强制推送。想想为什么！
- 尝试在远程进行的更改之上“重新应用”你的更改
  - 这就是 `rebase`！
    - 回滚自共享祖先以来的所有本地提交
    - 将 `HEAD` 快进到远程名称处的提交
    - 按顺序应用本地提交
      - 可能会出现你必须手动解决的冲突
      - `git rebase --continue` 或 `--abort`
    - 这里有更多 [内容](https://git-scm.com/book/en/v2/Git-Branching-Rebasing)
  - `git pull --rebase` 会为你开始这个过程
  - 是否应该合并还是重新应用是一个热门话题！一些好的阅读材料：
    - [这个](https://www.atlassian.com/git/tutorials/merging-vs-rebasing)
    - [这个](http://web.archive.org/web/20210106220723/https://derekgourlay.com/blog/git-when-to-merge-vs-when-to-rebase/)
    - [这个](https://stackoverflow.com/questions/804115/when-do-you-use-git-rebase-instead-of-git-merge)

## 进一步阅读

[![XKCD 上的 git](https://imgs.xkcd.com/comics/git.png)](https://xkcd.com/1597/)

- [学习 git 分支](https://learngitbranching.js.org/)
- [用简单的话解释 git](https://smusamashah.github.io/blog/2017/10/14/explain-git-in-simple-words)
- [从下往上学 git](https://jwiegley.github.io/git-from-the-bottom-up/)
- [计算机科学家的 git](http://eagain.net/articles/git-for-computer-scientists/)
- [哦，该死，git！](https://ohshitgit.com/)
- [《Pro Git》书](https://git-scm.com/book/en/v2)

## 练习

1. 在一个仓库中尝试修改一个现有文件。当你运行 `git stash` 时会发生什么？运行 `git log --all --oneline` 时看到什么？运行 `git stash pop` 撤销你用 `git stash` 做的事情。在什么情况下这可能有用？

1. 在学习 git 时的一个常见错误是提交不应由 git 管理的大文件或添加敏感信息。尝试向存储库添加一个文件，进行一些提交，然后从历史记录中删除该文件（你可能想看看 [这个](https://help.github.com/articles/removing-sensitive-data-from-a-repository/)）。此外，如果你确实希望 git 为你管理大文件，请查看 [Git-LFS](https://git-lfs.github.com/)

1. git 非常方便地撤消更改，但即使是最不可能的更改也必须熟悉

   1. 如果一个文件在某个提交中被错误地修改了，可以使用 `git revert` 恢复它。但是如果一个提交涉及多个更改，`revert` 可能不是最佳选项。我们如何使用 `git checkout` 从特定提交恢复文件的版本？
   1. 创建一个分支，在该分支中进行一次提交，然后删除它。你仍然可以恢复该提交吗？尝试查看 `git reflog`。（注意：快速恢复悬空的东西，git 会定期自动清理没有指向的提交。）
   1. 如果一个人过于轻率地使用 `git reset --hard` 而不是 `git reset`，更改可能会很容易丢失。然而，由于更改已被暂存，我们可以恢复它们。 （查看 `git fsck --lost-found` 和 `.git/lost-found`）

1. 在任何 git 仓库中查看 `.git/hooks` 文件夹，你会找到一堆以 `.sample` 结尾的脚本。如果你去掉 `.sample`，它们将根据其名称运行。例如，`pre-commit` 将在提交前执行。尝试一下

1. 像许多命令行工具一样，`git` 提供了一个配置文件（或点文件）叫做 `~/.gitconfig`。使用 `~/.gitconfig` 创建一个别名，以便当你运行 `git graph` 时，你会得到 `git log --oneline --decorate --all --graph` 的输出（这是一个快速可视化提交图的好命令）

1. Git 还让你在 `~/.gitignore_global` 下定义全局忽略模式，这对于防止添加 RSA 密钥等常见错误非常有用。创建一个 `~/.gitignore_global` 文件，并添加模式 `*rsa`，然后在一个仓库中测试它是否有效。

1. 一旦你开始对 `git` 变得更熟悉，你会发现自己遇到了一些常见的任务，比如编辑你的 `.gitignore`。[git extras](https://github.com/tj/git-extras/blob/master/Commands.md) 提供了一些与 `git` 集成的小工具。例如 `git ignore PATTERN` 将指定的模式添加到你的仓库中的 `.gitignore` 文件中，而 `git ignore-io LANGUAGE` 将从 [gitignore.io](https://www.gitignore.io) 获取该语言的常见忽略模式。安装 `git extras`，尝试使用一些工具，比如 `git alias` 或 `git ignore`。

1. 有时候，git GUI 程序也可以是一个很好的资源。在一个 git 仓库中运行 [gitk](https://git-scm.com/docs/gitk) 并探索界面的不同部分。然后运行 `gitk --all`，有什么区别吗？

1. 一旦你习惯了命令行应用程序，GUI 工具可能会感觉笨重/臃肿。在两者之间有一个很好的折衷是 ncurses 基于工具，可以从命令行进行导航，并且仍然提供交互式界面。Git 有 [tig](https://github.com/jonas/tig)，尝试安装它并在一个仓库中运行它。你可以在[这里](https://www.atlassian.com/blog/git/git-tig)找到一些用法示例。

<!-- {% comment %}

 - 强制推送 + `--force-with-lease`
 - git merge/rebase --abort
 - git blame
 - 关于为什么重写公共提交是不好的练习

{% endcomment %} -->
