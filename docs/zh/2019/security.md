---
layout: lecture
title: "Security and Privacy"
presenter: Jon
video:
  aspect: 56.25
  id: OBx_c-i-M8s
---

世界是一个可怕的地方，每个人都在想方设法攻击你。

好吧，也许不是，但这并不意味着你想炫耀你所有的秘密。安全（和隐私）通常都是提高攻击者的门槛。找出你的威胁模型是什么，然后设计你的安全机制围绕着那个模型！如果威胁模型是 NSA 或 Mossad，你可能会有个*糟糕的*时刻。

有*很多*方法可以使你的技术个人更安全。我们将在这里涉及很多高层次的事情，但这是一个过程，教育自己是你可以做的最好的事情之一。所以：

## 关注正确的人

提高你的安全知识的最佳方法之一是关注其他对安全有发言权的人。一些建议：

- [@TroyHunt](https://twitter.com/TroyHunt)
- [@SwiftOnSecurity](https://twitter.com/SwiftOnSecurity)
- [@taviso](https://twitter.com/taviso)
- [@thegrugq](https://twitter.com/thegrugq)
- [@tqbf](https://twitter.com/tqbf)
- [@mattblaze](https://twitter.com/mattblaze)
- [@moxie](https://twitter.com/moxie)

另请参阅[这个列表](https://heimdalsecurity.com/blog/best-twitter-cybersec-accounts/)以获取更多建议。

## 一般安全建议

Tech Solidarity 有一个相当不错的[记者的做和不做](https://web.archive.org/web/20221123204419/https://techsolidarity.org/resources/basic_security.htm)清单，其中有很多明智的建议，并且是相当及时的。[@thegrugq](https://medium.com/@thegrugq)
也有一篇关于[旅行安全的博客文章](https://medium.com/@thegrugq/stop-fabricating-travel-security-advice-35259bf0e869)，值得一读。我们将在这里重复大部分来自这些来源的建议，以及一些其他的建议。另外，购买一个[USB 数据
阻塞器](https://www.amazon.com/dp/B00QRRZ2QM/)，因为[USB
是可怕的](https://www.bleepingcomputer.com/news/security/heres-a-list-of-29-different-types-of-usb-attacks/)。

## 认证

如果你还没有的话，第一件事就是下载一个密码管理器。一些好的选择是：

- [1password](https://1password.com/)
- [KeePass](https://keepass.info/)
- [BitWarden](https://bitwarden.com/)
- [`pass`](https://www.passwordstore.org/)

如果你特别多疑，使用一个在你的计算机上本地加密密码的管理器，而不是在服务器上以明文存储它们。用它为你现在关心的所有网站生成密码。然后，开启两步验证，最好使用一个[FIDO/U2F](https://fidoalliance.org/)的设备（比如[YubiKey](https://www.yubico.com/quiz/)，学生有[20% 折扣](https://www.yubico.com/why-yubico/for-education/)）。TOTP（例如 Google Authenticator 或 Duo）在紧急情况下也可以使用，但[不能防止
钓鱼](https://twitter.com/taviso/status/1082015009348104192)。短信基本上是无用的，除非你的威胁模型只包括随机陌生人拦截你的密码。

另外，关于纸质密钥的说明。通常，服务会给你一个“备用密钥”，如果你丢失了你的真正的第二个因素，你可以用它作为第二个因素（顺便说一句，总是在某个安全的地方保留一个备份设备！）。虽然你*可以*将这些密钥放在你的密码管理器中，但这意味着如果有人访问了你的密码管理器，你将完全搞糟（但也许你对这种威胁模型没关系）。如果你真的很多疑，打印出这些纸质密钥，永远不要将它们以数字形式存储，并将它们放在现实世界的安全处。

## 私人通信

使用 [Signal](https://www.signal.org/)（[设置
说明](https://medium.com/@mshelton/signal-for-beginners-c6b44f76a1f0)）。[Wire](https://wire.com/en/) 也可以 ([fine
too](https://www.securemessagingapps.com/)); WhatsApp 可以; [不要使用
Telegram](https://twitter.com/bascule/status/897187286554628096))。桌面通讯软件非常糟糕（部分原因是通常依赖于 Electron，这是一个巨大的信任堆栈）。

邮件特别有问题，即使是 PGP 签名的也是如此。它通常不是前向安全的，并且密钥分发问题非常严重。[keybase.io](https://keybase.io/) 有所帮助，并且出于许多其他原因也是有用的。此外，PGP 密钥通常在桌面计算机上处理，这是最不安全的计算环境之一。相关地，考虑购买一个 Chromebook，或者只在带键盘的平板电脑上工作。

## 文件安全

文件安全很难，并且在很多层面上操作。你试图防范什么？

[![$5 wrench](https://imgs.xkcd.com/comics/security.png)](https://xkcd.com/538/)

- 离线攻击（当你的笔记本电脑关机时有人偷走它）：打开全磁盘加密。([cryptsetup +
  LUKS](https://wiki.archlinux.org/index.php/Dm-crypt/Encrypting_a_non-root_file_system) 在 Linux,
  [BitLocker](https://fossbytes.com/enable-full-disk-encryption-windows-10/) 在 Windows,
  [FileVault](https://support.apple.com/en-us/HT204837) 在 macOS。注意，如果攻击者*同时*也有你，并且真的想要你的秘密，这是没用的。
- 在线攻击（有人拿到你的笔记本电脑并且它开机了）：使用文件加密。有两种主要机制可以做到这一点
  - 加密文件系统：叠加式文件系统加密软件单独加密文件，而不是使用加密块设备。你可以通过提供解密密钥来“挂载”这些文件系统，然后自由地浏览其中的文件。当你卸载它时，那些文件都不可用了。现代解决方案包括[gocryptfs](https://github.com/rfjakob/gocryptfs) 和 [eCryptFS](http://ecryptfs.org/)。可以在[这里](https://nuetzlich.net/gocryptfs/comparison/)和[这里](https://wiki.archlinux.org/index.php/disk_encryption#Comparison_table)找到更详细的比较。
  - 加密文件：使用对称加密（见 `gpg -c`）和一个秘密密钥对单个文件进行加密。或者，像 `pass` 一样，还可以使用你的公钥加密密钥，这样只有你才能用你的私钥将它读回来。确切的加密设置很重要！
- [似是而非的否认](https://en.wikipedia.org/wiki/Plausible_deniability)（警官看起来有问题？）：通常性能较低，并且更容易丢失数据。很难真正证明它提供了[可否认的加密](https://en.wikipedia.org/wiki/Deniable_encryption)! 参见[这里的讨论](https://security.stackexchange.com/questions/135846/is-plausible-deniability-actually-feasible-for-encrypted-volumes-disks)，然后考虑是否要尝试[ VeraCrypt](https://www.veracrypt.fr/en/Home.html)（好老的 TrueCrypt 的维护分支）。
- 加密备份：使用[Tarsnap](https://www.tarsnap.com/) 或 [Borgbase](https://www.borgbase.com/)
  - 想一想如果攻击者拿到了你的笔记本电脑，他们是否可以删除你的备份！

## 互联网安全和隐私

互联网是一个*非常*可怕的地方。开放的 WiFi 网络[很](https://www.troyhunt.com/the-beginners-guide-to-breaking-website/) [可怕](https://www.troyhunt.com/talking-with-scott-hanselman-on/)。确保在使用后将它们删除，否则你的手机将会愉快地宣布并重新连接到以后具有相同名称的某个东西！

如果你曾经在你不信任的网络上，VPN _可能_ 是值得的，但请记住你非常*信任* VPN 提供商。你真的比你的 ISP 更信任他们吗？如果你真的想要一个 VPN，请使用你确信可以信任的提供商，并且你应该为它付费。或者为自己设置[WireGuard](https://www.wireguard.com/) -- 它是[优秀的](https://web.archive.org/web/20210526211307/https://latacora.micro.blog/there-will-be/)！

许多互联网应用程序也有安全的配置设置，[cipherlist.eu](https://cipherlist.eu/) 上有很多。如果你特别注重隐私，[privacytools.io](https://privacytools.io) 也是一个不错的资源。

有些人可能会对[Tor](https://www.torproject.org/)感到好奇。请记住，Tor 对全球强大的攻击者*不*特别抵抗，并且对流量分析攻击很脆弱。它可能对小规模隐藏流量有用，但在隐私方面并没有真正为你提供太多帮助。你最好在第一时间使用更安全的服务（Signal、TLS + 证书锁定等）。

## Web 安全

所以，你也想上网？
天哪，你真的在冒险。

安装[HTTPS Everywhere](https://www.eff.org/https-everywhere)。SSL/TLS 是[至关重要的](https://www.troyhunt.com/ssl-is-not-about-encryption/)，而且它不仅仅是关于加密，还关于能够验证你首先是在与正确的服务通信！如果你运行自己的 web 服务器，请[测试它](https://www.ssllabs.com/ssltest/index.html)。TLS 配置[可能会变得复杂](https://wiki.mozilla.org/Security/Server_Side_TLS)。HTTPS Everywhere 将尽最大努力永远不会在有替代品的情况下将你导航到 HTTP 网站。这并不能救你，但它有所帮助。如果你真的很多疑，黑名单中任何你绝对不需要的 SSL/TLS CA。

安装[uBlock Origin](https://github.com/gorhill/uBlock)。它是一个[广谱阻断器](https://github.com/gorhill/uBlock/wiki/Blocking-mode)，不仅仅停止广告，还会阻止页面可能尝试进行的所有类型的第三方通信。以及内联脚本之类的。如果你愿意花一些时间来配置使事情工作，进入[中等模式](https://github.com/gorhill/uBlock/wiki/Blocking-mode:-medium-mode)甚至[困难模式](https://github.com/gorhill/uBlock/wiki/Blocking-mode:-hard-mode)。这些*会*使一些网站不工作，直到你调整了设置足够多，但也会显着提高你的在线安全。

如果你使用的是 Firefox，请启用[多账户容器](https://support.mozilla.org/en-US/kb/containers)。为社交网络、银行、购物等创建单独的容器。Firefox 会将每个容器的 cookie 和其他状态完全分开，因此你在一个容器中访问的站点无法窥视来自其他容器的敏感数据。在 Google Chrome 中，您可以使用[Chrome Profiles](https://support.google.com/chrome/answer/2364824)来实现类似的结果。

## 练习

<!-- TODO -->

1. 使用 PGP 加密文件
1. 使用 veracrypt 创建一个简单的加密卷
1. 为你最敏感的数据账户启用两步验证，例如 GMail、Dropbox、Github，等等
