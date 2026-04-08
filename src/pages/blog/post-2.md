---
layout: ../../layouts/BlogLayout.astro
title: "我的第二篇独立站日记"
date: "2020-04-08"
author: "MTeng"
---
---

好，我们立刻进入**内容型架构的核心战役 —— Markdown 博客系统接入**。

在阶段一，你不得不写一段极其复杂的逻辑：*读取 URL 参数 -> fetch .md 文件 -> 拿给前端的 Markdown-to-HTML 解析器（比如 `marked.js`）去转译 -> 最后用 `innerHTML` 塞进页面*。

在 Astro 里，**你一行解析代码都不需要写！** Astro 天生就能把 Markdown 像 HTML 一样处理。

接下来是优雅到极致的 3 步操作，请在 VS Code 中执行：

---

### 第一步：准备你的 Markdown 库

我们要把文章放在工作区里。

1. 在 `src/pages/` 目录下，新建一个**文件夹**，命名为 `blog`。
2. 在 `src/pages/blog/` 文件夹下，新建一个**文件**，命名为 `post-1.md`。
3. 把下面这段内容（包含头部的 Meta 信息和正文）复制进去并保存：

```markdown
---
title: "独立站建站笔记：为什么放弃前端渲染？"
date: "2023-11-01"
author: "MTeng"
---

# 放弃前端渲染的理由

这是我写的第一篇独立站博客。

在学习了 **Astro** 之后，我发现以前写 JavaScript 去解析 Markdown 简直是在浪费生命。现在我只需要写纯文本，Astro 在打包时会自动把它们变成 `<h1>` 和 `<p>` 标签。

## SEO 优势
爬虫终于能看懂我的文章了！
```

*（你可以顺手再建一个 `post-2.md`，随便写点什么测试一下）*

---

### 第二步：创建一个“相框”（博客专属布局文件）

Markdown 文件只有文字内容，没有 `<html>` 和 `<body>` 标签，所以我们需要给它造一个“相框”，把文章镶嵌进去。

1. 在 `src/` 目录下（注意不是 `pages` 里面），新建一个文件夹，命名为 `layouts`。
2. 在 `src/layouts/` 里新建一个文件，命名为 `BlogLayout.astro`。
3. 把下面的代码复制进去并保存：

```astro
---
// Astro 魔法：这里能自动接收到 Markdown 顶部那些带 --- 的属性！
const { frontmatter } = Astro.props;
---

<html lang="zh">
  <head>
    <meta charset="utf-8" />
    <title>{frontmatter.title}</title>
    <style>
      body { max-width: 800px; margin: 0 auto; font-family: sans-serif; line-height: 1.6; }
      .meta { color: gray; font-size: 0.9em; border-bottom: 1px solid #eee; padding-bottom: 10px; }
      /* Markdown 转出来的图片自动适应宽度 */
      img { max-width: 100%; height: auto; } 
    </style>
  </head>
  <body>
    <a href="/blog">⬅ 返回博客列表</a>
    
    <!-- 文章标题和元信息 -->
    <h1>{frontmatter.title}</h1>
    <div class="meta">
      作者: {frontmatter.author} | 发布日期: {frontmatter.date}
    </div>

    <!-- 🌟 最核心的一行代码：Markdown 转成的 HTML 会被塞进这里！ -->
    <article>
      <slot />
    </article>

  </body>
</html>
```

---

### 第三步：把“照片”放进“相框”里

最后一步，告诉 Markdown 文件：“你要套用哪个相框？”

1. 重新打开你刚才写的 `src/pages/blog/post-1.md`。
2. 在顶部区域加一行 `layout: ...`，修改后长这样并保存：

```markdown
---
layout: ../../layouts/BlogLayout.astro
title: "独立站建站笔记：为什么放弃前端渲染？"
date: "2023-11-01"
author: "MTeng"
---

（下面是正文内容不变...）
```

---

### 见证“降维打击”时刻

浏览器里直接输入：**`http://localhost:4321/blog/post-1`**

**Boom! 💥**
你会看到一篇排版完美、带有返回按钮和 Meta 信息的博客文章。

**发生了什么？**
Astro 在打包时：
1. 发现了 `pages/blog/post-1.md`。
2. 它把里面的 Markdown 语法转换成了 HTML 标签。
3. 它顺着 `layout` 路径找到了 `BlogLayout.astro` 这个相框。
4. 把 HTML 塞进了相框里的 `<slot />` 标签处。
5. 生成了一个完美的 `post-1.html` 文件供用户访问！

---

### 🎯 你的下一步指令

现在，你的文章已经能渲染了，但用户不可能每次都手敲 `post-1` 的网址，我们需要一个**“博客列表页”**来把所有的 `.md` 文章展示出来。

如果上面的单篇文章测试成功，请回复：
👉 **“Markdown 渲染成功！布局文件看懂了，接下来教我怎么做 `/blog` 这个文章列表页吧！”** 

*(有任何样式没生效或者报错，随时告诉我)*