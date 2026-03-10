# Firecrawl 技能

Firecrawl 技能位于 `https://github.com/firecrawl/cli` 中 [skills/firecrawl-cli](https://github.com/firecrawl/cli/tree/main/skills/firecrawl-cli "This path skips through empty directories")，
`firecrawl-cli` 是基于 [firecrawl](https://github.com/firecrawl/firecrawl) 的命令行工具。
Firecrawl 技能用于说明怎么使用 `firecrawl-cli`。
Firecrawl 可以免费使用但是有爬取速率限制。

Firecrawl docs： https://docs.firecrawl.dev/zh/introduction 。

Firecrawl 工作模式：
- **scrape**（抓取）
	抓取单个网页的内容。
- **search**（搜索）
	搜索与特定主题相关的内容， 内容类型包括标准网页结果（web）、新闻内容（new）、图片（images）。
- **map**
	从网站极快地获取其所有 URL， URL 格式包括 url、title、description。
	Sitemap（网站地图） 是一个 列出网站所有页面 URL 的文件，主要用于让搜索引擎和程序更容易发现网站内容。它本质上是一个 结构化的 URL 列表。 搜索引擎（如 Google）会读取 sitemap 来发现页面。
- **crawl** (爬取)
	**递归爬取**网站并提取每个页面的内容。

Firecrawl 功能：
- 内置页面缓存（内存）
- 支持按文件类型、数据结构（比如json schema）格式化输出内容
- 支持批量、同步、异步抓取数据