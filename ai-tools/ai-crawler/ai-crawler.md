爬虫工具：
+ Firecrawl
	2025年最火的爬虫工具，
+ Jina Reader
	专注于提取网页纯净内容，如果需要转换为格式化数据，需要配合大模型来完成。
+ Crawl4AI

## Firecrawl


## Jina Reader

**简单用法**：
1. 在想要抓取的网站地址前加上 `https://r.jina.ai/`；
2. 将返回的 Markdown 内容和需要转换成的格式 JSON 都发给 AI 工具，让AI将内容转化为目标格式。

```ts
export interface Recipe {
  id: number
  title: string
  description?: string
  coverImage?: string
  servings: number
  prepTime?: number    // 预处理时间（分钟）
  cookingTime?: number // 烹饪时间（分钟）
  viewCount: number
  visibility: 'public' | 'private'
  tips?: string
  ratingAvg?: number       // 计算字段：所有 RecipeRating 的平均分
  ratingCount?: number     // 计算字段：RecipeRating 数量
  authorId: number
  author?: User
  ingredients?: Ingredient[]
  steps?: Step[]
  comments?: RecipeComment[]   // 评论列表
  myRating?: RecipeRating      // 当前登录用户的评分（详情页携带）
  isCollected?: boolean        // 是否被当前登录用户收藏（详情页、卡片携带）
  createdAt: string
  updatedAt: string
}
```
## Crawl4AI

