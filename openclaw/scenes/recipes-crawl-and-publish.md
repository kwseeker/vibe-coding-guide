# 食谱爬取与发布

某食谱应用中需要定期发布运营数据，先爬取后发布。
项目根目录： `~/openclaw-projects/crawler-and-publisher`。

## 整体流程

1. **定时任务触发工作流程**
	比如每天晚上11点触发。
2. **从文件读取目标网站地址**
	待爬取的目标网站列表存储到项目根目录的 `crawl-target-websites.json` 文件。
3. **使用 `firecrawl` 技能爬取一批食谱页面并存储**
	使用 `firecrawl` 先深度遍历筛选出10个食谱页面存储到 `<yyyy-MM-dd>-target-urls.json` 文件。
4. **遍历  `<yyyy-MM-dd>-target-urls.json` 文件中的 URL 抓取食谱内容并格式化输出**
	格式化输出到 `<yyyy-MM-dd>-recipes.json` 文件。
	格式要求：
	```ts
	// 食谱数据结构
	export interface Recipe {
	  title: string         //菜谱标题
	  description?: string  //菜谱描述
	  coverImage?: string   //菜谱封面图
	  servings: number      //食材份量（几人食用）
	  prepTime?: number     //预处理时间（分钟）
	  cookingTime?: number  //烹饪时间（分钟）
	  tips?: string         //烹饪提示与技巧
	  ratingAvg?: number       //菜谱平均评分
	  ratingCount?: number     //菜谱评分人数
	  ingredients?: Ingredient[] //食材列表
	  steps?: Step[]             //食材烹饪步骤
	}
	// 食谱使用食材数据结构
	export interface Ingredient {
	  name: string    //食材名称
	  amount?: number //食材用量
	  unit?: string   //食材用量单位
	}
    // 食谱烹饪步骤数据结构
    export interface Step {
	  stepNumber: number //第几步
	  content: string    //步骤描述
	  imageUrl?: string  //步骤图片
	}
	```
5. **数据加工**（先实现前面的流程）
	文字描述错别字修改，语法修复等。
6. **数据审核**（先实现前面的流程）
	数据发布前需要先经过人工审核。
7. **数据发布**（先实现前面的流程）
	调用后端专门的接口发布食谱数据。

## 具体实现

### **定时触发**

参考：[定时任务](https://docs.openclaw.ai/zh-CN/automation/cron-jobs)

```sh
openclaw cron add \
  --name "Crawl Trigger" \
  --at "2026-02-01T16:00:00Z" \
  --session main \
  --system-event "Reminder: check the cron docs draft" \
  --wake now \
  --delete-after-run
```
## TODO

- 多角色？每个角色有自己的目录么？
- 这个流程可以封装成什么形式，用作后续复用？