---
layout: lecture
title: "网络和浏览器"
presenter: Jose
video:
  aspect: 62.5
  id: XpZO3S8odec
---

<iframe src="https://www.youtube.com/embed/XpZO3S8odec" frameborder="0" allowfullscreen></iframe>

除了终端之外，网络浏览器是一个你会发现自己花费大量时间的工具。因此，值得学习如何高效地使用它。

## 快捷键

在浏览器中四处点击通常不是最快的选择，熟悉常见的快捷键在长期内会带来真正的回报。

- `中键点击` 在新标签页中打开链接
- `Ctrl+T` 打开新标签页
- `Ctrl+Shift+T` 重新打开最近关闭的标签页
- `Ctrl+L` 选择搜索栏中的内容
- `Ctrl+F` 在网页内搜索。如果经常这样做，你可能会受益于支持正则表达式搜索的扩展程序。

## 搜索操作符

Web 搜索引擎如 Google 或 DuckDuckGo 提供了搜索操作符以实现更精细的网络搜索：

- `"bar foo"` 强制进行 bar foo 的精确匹配
- `foo site:bar.com` 在 bar.com 中搜索 foo
- `foo -bar ` 排除包含 bar 的项
- `foobar filetype:pdf` 搜索该扩展名的文件
- `(foo|bar)` 搜索包含 foo 或 bar 的匹配项

更详细的列表可在像[Google](https://ahrefs.com/blog/google-advanced-search-operators/)和[DuckDuckGo](https://duck.co/help/results/syntax)这样的流行引擎中找到。

## 搜索栏

搜索栏也是一个强大的工具。大多数浏览器可以从网站中推断出搜索引擎，并将它们存储起来。通过编辑关键字参数

- 在 Google Chrome 中，它们在[chrome://settings/searchEngines](chrome://settings/searchEngines)中
- 在 Firefox 中，它们在[about:preferences#search](about:preferences#search)中

例如，你可以使`y SOME SEARCH TERMS`直接在 YouTube 中搜索。

此外，如果你拥有一个域名，你可以使用你的注册器设置子域名转发。例如，我已将`https://ht.josejg.com`映射到此课程网站。这样一来，我只需输入`ht.`，搜索栏就会自动完成。此设置的另一个好处是，与书签不同，它们将在每个浏览器中都起作用。

## 隐私扩展

如今，因为广告和跟踪器的存在，网上冲浪可能变得非常烦人和侵入性。一个好的广告拦截器不仅会阻止大多数广告内容，还会阻止可疑和恶意的网站，因为它们会包含在常见的黑名单中。有时它们还会减少页面加载时间，因为它们会减少请求的数量。一些推荐包括：

- **uBlock origin** ([Chrome](https://chrome.google.com/webstore/detail/ublock-origin/cjpalhdlnbpafiamejdnhcphjbkeiagm), [Firefox](https://addons.mozilla.org/en-US/firefox/addon/ublock-origin/)): 基于预定义规则阻止广告和跟踪器。你也应该考虑查看设置中启用的黑名单，因为你可以根据你的地区或浏览习惯启用更多。你甚至可以从[网络上](https://github.com/gorhill/uBlock/wiki/Filter-lists-from-around-the-web)安装过滤器。

- **[Privacy Badger](https://privacybadger.org/)**: 自动检测并阻止跟踪器。例如，当你从一个网站转到另一个网站时，广告公司会跟踪你访问了哪些网站，并建立你的个人资料。

- **[HTTPS Everywhere](https://www.eff.org/https-everywhere)** 是一个很棒的扩展，如果有的话，会自动重定向到网站的 HTTPS 版本。

你可以在[这里](https://www.privacytools.io/privacy-browser-addons/)找到更多类似的插件。

## 样式定制

Web 浏览器只是在**你的机器**上运行的另一个软件，因此通常你有权决定它们应该显示什么或者它们应该如何运作。其中一个例子是自定义样式。浏览器使用层叠样式表（CSS）确定如何呈现网页的样式。

你可以通过检查网页源代码并临时更改其内容和样式来访问网站的源代码（这也是你不应信任网页截图的原因之一）。

如果你想永久地告诉你的浏览器覆盖网页的样式设置，你需要使用一个扩展程序。我们推荐使用 **[Stylus](https://github.com/openstyles/stylus)** ([Firefox](https://addons.mozilla.org/en-US/firefox/addon/styl-us/), [Chrome](https://chrome.google.com/webstore/detail/stylus/clngdbkpkpeebahjckkjfobafhncgmne?hl=en))。

例如，我们可以为类网站编写以下样式

```css
body {
  background-color: #2d2d2d;
  color: #eee;
  font-family: Fira Code;
  font-size: 16pt;
}

a:link {
  text-decoration: none;
  color: #0a0;
}
```

此外，Stylus 可以找到其他用户编写并发布在[userstyles.org](https://userstyles.org/)的样式。大多数常见的网站都有一个或多个深色主题样式表。顺便说一句，你不应该使用 Stylish，因为它被证明泄漏用户数据，更多信息[在这里](https://arstechnica.com/information-technology/2018/07/stylish-extension-with-2m-downloads-banished-for-tracking-every-site-visit/)

## 功能定制

与修改样式的方式相同，你也可以通过编写自定义的 JavaScript 来修改网站的行为，并使用 Web 浏览器扩展程序（例如[Tampermonkey](https://tampermonkey.net/)）来引用它。

例如，以下脚本使用 J 和 K 键启用了类似 Vim 的导航。

```js
// ==UserScript==
// @name VIM HT
// @namespace http://tampermonkey.net/
// @version 0.1
// @description Vim JK for our website
// @author You
// @match https://hacker-tools.github.io/*
// @grant none
// ==/UserScript==

(function () {
  "use strict";

  window.onkeyup = function (e) {
    var key = e.keyCode ? e.keyCode : e.which;

    if (key == 74) {
      // J is key 74
      window.scrollBy(0, 500);
    } else if (key == 75) {
      // K is key 75
      window.scrollBy(0, -500);
    }
  };
})();
```

还有脚本仓库，如[OpenUserJS](https://openuserjs.org/)和[Greasy Fork](https://greasyfork.org/en)。但要警惕，安装他人的用户脚本可能非常危险，因为它们几乎可以做任何事情，比如窃取你的信用卡号。除非你自己阅读了整个脚本，理解了它的作用，并且绝对确定它没有做任何可疑的事情，否则永远不要安装脚本。永远不要安装包含你无法阅读的压缩或混淆代码的脚本！

## Web API

越来越多的网络服务提供应用程序接口，也称为 Web API，以便你可以通过发出网络请求与服务进行交互。
可以在[这里](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Client-side_web_APIs/Introduction)找到有关该主题的更深入的介绍。有[许多公共 API](https://github.com/toddmotto/public-apis)。Web API 之所以有用，有很多原因：

- **检索**。Web API 可以很容易地为你提供信息，例如地图、天气或你的公共 IP 地址。例如，`curl ipinfo.io` 将返回一个包含有关你的公共 IP、区域、位置等信息的 JSON 对象。通过适当的解析，这些工具甚至可以与命令行工具集成。以下 bash 函数与谷歌的自动补全 API 通信，并返回前十个匹配项。

```bash
function c() {
url='https://www.google.com/complete/search?client=hp&hl=en&xhr=t' # NB: user-agent 必须指定才能得到 UTF-8 数据！
curl -H 'user-agent: Mozilla/5.0' -sSG --data-urlencode "q=$*" "$url" |
jq -r ".[1][][0]" |
sed 's,</\?b>,,g'
}
```

- **交互**。Web API 端点也可以用于触发操作。这通常需要一种你可以通过服务获得的身份验证令牌。例如，执行以下操作
  `curl -X POST -H 'Content-type: application/json' --data '{"text":"Hello, World!"}' "https://hooks.slack.com/services/$SLACK_TOKEN"` 将在一个频道中发送 `Hello, World!` 消息。

- **管道传输**。由于一些具有 Web API 的服务相当流行，常见的 Web API “粘合”已经实现并与服务器一起提供。这适用于[If This Then That](https://ifttt.com/)和[Zapier](https://zapier.com/)等服务。

## Web 自动化

有时 Web API 是不够的。如果只需要阅读，你可以使用 HTML 解析器，如 `pup`，或使用库，例如 Python 有 BeautifulSoup。然而，如果需要交互性或 JavaScript 执行，这些解决方案就不够了。WebDriver

例如，以下脚本将使用 Wayback Machine 保存指定的 URL，模拟键入网站的交互。

```python
from selenium.webdriver import Firefox
from selenium.webdriver.common.keys import Keys

def snapshot_wayback(driver, url):

    driver.get("https://web.archive.org/")
    elem = driver.find_element_by_class_name('web-save-url-input')
    elem.clear()
    elem.send_keys(url)
    elem.send_keys(Keys.RETURN)
    driver.close()

driver = Firefox()
url = 'https://hacker-tools.github.io'
snapshot_wayback(driver, url)
```

## 练习

1. 编辑一个你经常使用的 Web 浏览器中的关键字搜索引擎。
1. 安装上面提到的扩展程序。查看如何在某个网站上禁用 uBlock Origin/Privacy Badger。你看到了什么区别？试着在像 YouTube 这样有大量广告的网站上尝试。
1. 安装 Stylus，并使用提供的 CSS 为类网站编写自定义样式。以下是一些常见的编程字符 `=   ==   ===   >=   =>   ++   /=   ~=`。将字体更改为 Fira Code 时会发生什么？如果你想了解更多，请搜索编程字体连字。
1. 找到一个 Web API 来获取你所在城市/地区的天气。
1. 使用 WebDriver 软件，如[Selenium](https://docs.seleniumhq.org/)，自动执行你经常在浏览器中执行的一些重复的手动任务。
