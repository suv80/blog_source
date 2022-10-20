---
title: 如何使用 HTML 将 Twitter Card 和 Open Graph 社交元数据添加到你的网页
date: 2021-12-10 15:46:23
tags: [html,meta]
---

当你在社交媒体平台上分享链接时，它们通常会显示丰富的图像和格式良好的标题、摘要和链接，而不是纯文本。你知道是怎么实现这样的功能的吗？

### 一、为什么要设置社交媒体元标签

通常，我们可以通过在 HTML 文档的中包含某些标签，来将这些富媒体社交共享功能添加到你的站点，从而实现定制化分享的显示内容。

下图是一个没有设置这些元数据，Twitter 分享链接显示的效果：

![默认社交媒体分享](https://gitee.com/limeng1984/pstore/raw/master/blog/simple-share.png)

默认情况下，大多数社交网络会尽最大努力创建你的内容预览。 看起来似乎还行？

下图是设置了这些元数据后的效果，相比之下，前一个的分享效果显得就很是单薄无趣了。

![社交媒体元数据](https://gitee.com/limeng1984/pstore/raw/master/blog/rich-share.png)

因此，正确的设置这些社交媒体元数据是十分重要的，这能使你的内容在互联网上大量的内容中脱颖而出。

现如今有两个主要标准定义如何格式化此元数据：Twitter Cards 和 Facebook Open Graph 协议。

在本教程中，你将学会如何将 Twitter Card 和 Open Graph 元数据添加到你的网页中。

### 二、 Facebook Open Graph

Open Graph （开放图谱协议）由 Facebook 的创建，简称 OG 协议或 OGP。它是 Facebook 在 2010 年 F8 开发者大会公布的一种网页元信息（Meta Information）标记协议，属于 Meta Tag （Meta 标签）的范畴，是一种为社交分享而生的 Meta 标签。它允许在 Facebook 上，其他网站能像 Facebook 内容一样具有丰富的媒体对象，进而促进 Facebook 和其他网站之间的集成。

Open Graph 标签不仅被 Facebook 使用，也被 LinkedIn 和 Twitter 等平台使用。请注意 Twitter 也有自己的一套 Twitter Cards 定义，如果网页提供了 Twitter 自己的定义，则优先使用自己的标准。

**Open Graph 协议格式**

Open Graph 元标签的格式如下：

```html
<meta property="[NAME]" content="[VALUE]" />
```

**Open Graph 常用属性**

一组最小的 Open Graph 标签如下所示：

```html
<!-- 标题 -->
<meta property="og:title" content="HTML meta 标签详解">
<!-- 类型 -->
<meta property="og:type" content="article">
<!-- 网页的永久链接 -->
<meta property="og:url" content="https://www.liuxing.io/blog/html-meta-tags/">
<!-- 网页的需要展示图片 -->
<meta property="og:image" content="https://www.liuxing.io/blog/html-meta-tags/meta-tags.webp">
```

对于 Open Graph 来说，每个页面里需要以上四个必需属性：

- og:title - 网页的标题，例如“刘星的个人网站”。
- og:type - 网页的类型，所描述内容的类型。可以是 article，book，website, profile 等
- og:image - 一个图像 URL, 用于设置与页面关联的图像。
- og:url - 对象的规范 URL，例如，“https://liuxing.io/"

Open Graph 协议定义了一些网页类型， 包括 article、book、website、profile 等。

如果你的网页为个人主页，`og:type` 可以为 `profile`；如果你的网页是一个视频为主的网页面，则可以为`video`；如果你的网页是一篇博客文章则可以为`article`。如果你的网页没什么特殊的类别，则可将`og:type` 设置为通用的`website`。

你可以在 https://ogp.me/#types 找到 Open Graph 通用的类型定义

除了以上四个必需属性之外，Open Graph 还有以下常用属性：

```html
<meta property="og:description" content="Description Here">
<meta property="og:site_name" content="Site Name">
<meta property="og:locale" content="en_US">
<meta property="og:video" content="link">
<meta property="og:audio" content="link">
```

- og:site_name 你的网站的名称
- og:description 你的页面的描述
- og:audio - 网页中的音频链接。
- og:locale - 标记语言环境。格式为 language_TERRITORY。默认为 en_US。
- og:video - 你网页中的视频链接

除了以上介绍的常用内容外，Open Graph 还有很多属性。有关 Open Graph 协议的更多信息，请访问官方 Open Graph 协议网站 https://ogp.me/

如果你的网站设置了 Open Graph 你可以使用 [facebook 的 debug 工具](https://developers.facebook.com/tools/debug/) 来调试及查看显示效果

### 三、Twitter Card

大多数社交网站都遵循 Open Graph 协议，但是也有一些平台自己实现了一些定义，例如 Twitter。

Twitter Cards 是 Twitter 使用的元数据规范，用于在 Twitter 上共享链接时显示富文本、图像和视频。

**Twitter Card 格式**

Twitter Cards 元标签的格式如下：

```html
<meta name="[NAME]" content="[VALUE]" />
```

**Twitter Cards 常用属性**

Twitter Cards 有四种不同的类型：Summary, Summary Card With Large Image, App, 和 Player.

```html
<meta name=twitter:card content="summary_large_image">
<meta name=twitter:image content="https://www.liuxing.io/blog/html-meta-tags/meta-tags.webp">
<meta name=twitter:title content="HTML meta 标签详解">
<meta name=twitter:description content="meta 标签标签定义了关于 HTML 文档的元数据">
<meta name="twitter:creator" content="@liuxing_chn">
<meta name="twitter:site" content="@site_account">
```

- twitter:card : 卡片类型，将是 summary、summary_large_image、app 或 player 之一
- twitter:site : 用于卡片页脚中使用的网站 @username
- twitter:creator ：内容创建者 / 作者的 @username
- twitter:url : 分享对象的链接
- twitter:title : 分享对象的标题 （最多 70 个字符）
- twitter:description : 分享对象的描述 （最多 200 个字符）
- twitter:image : 分享对象的图片 URL, 支持的格式有 JPG、PNG、WEBP 和 GIF。只会导入 GIF 的第一帧

给你的网页设置以上 meta 标签，再分享到 Twitter 就能看到如下效果

![Twitter Cards](https://gitee.com/limeng1984/pstore/raw/master/blog/rich-share-20211210155406367.png)

上面介绍了 Twitter Cards 一些常用属性，有关其他 Twitter Card 标签和选项的更多信息，请访问『官方 Twitter Cards 文档』( https://developer.twitter.com/en/docs/twitter-for-websites/cards/overview/abouts-cards)。

如果你的网站设置了 Open Graph 你可以使用 [Twitter Cards 验证器](https://cards-dev.twitter.com/validator) 来调试及查看显示效果

**Open Graph 与 Twitter Cards 常用属性汇总**

Facebook 和 Twitter 提供了有关如何使用上述元标记的指南。在下表中，我们总结了有关它们的基本信息以及各自的建议。

| 元标签 | facebook| twitter |
| ---- | ---- | :--- |
| 标题 | og:title 文章的标题，例如你的网站名称。                      | twitter:title Twitter 卡片的必需参数，最大长度 70 个字符。   |
| 图片 | og:image 当有人将内容分享到 Facebook 时显示的图像的 URL      | twitter:image 表示页面内容的唯一图像的 URL                   |
| 网站 | og:url 你页面的规范 URL                                      | twitter:url 你页面的规范 URL                                 |
| 描述 | og:description 对内容的简要描述，通常在 2 到 4 个句子之间。这将显示在 Facebook 帖子的标题下方 | Twitter:description 简明概括内容的描述，适合在推文中呈现。你不应重复使用标题作为描述或使用此字段来描述网站提供的一般服务 |

### 总结

本文讲解了如何通过 Open Graph 以及 Twitter Cards 设置社交媒体元标签。正确的设置这些 meta tag，在社交媒体中显示内容丰富的卡片，能够让你的受众眼前一亮。快去试试吧！



[原文链接：https://www.liuxing.io/blog/meta-tags-for-social-media/](https://www.liuxing.io/blog/meta-tags-for-social-media/)
