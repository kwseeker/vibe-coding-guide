官网：https://ui-ux-pro-max-skill.nextlevelbuilder.io
支持主流的AI编程工具。
这个Skill内部提供了50种风格，21中调色板，50种字体搭配，20种图表，8种技术栈（还在不断迭代增加），包括一个完整的构建工作流。
[benchmark-skill-ui-ux-pro-max](https://github.com/hylarucoder/benchmark-skill-ui-ux-pro-max) TS脚本让 ClaudeCode + ui-ux-pro-max 生成100个落地页。
[官方39个网站风格展示](https://ui-ux-pro-max-skill.nextlevelbuilder.io/#styles)

工作原理：
1. 输入用户提示词（不需要明确指定使用这个技能，AI会自己根据任务决定启用此技能）；
2. AI推理，搜索相关内容：产品、风格、排版、颜色、着陆、用户体验领域等；
3. 搜索数据库，加载对应的提示词；
4. 使用Skill 丰富后的提示词，生成页面代码；
5. 质量检查；
6. 获取最终结果。

Skill 安装：
+ Antigravity
	+ 全局安装
		将Github项目 `.claude/skills/ui-ux-pro-max` 下载到 `~/.gemini/antigravity/skills/`，注意里面的两个文件夹是链接，需要替换为链接指向的实际文件夹。
	+ 项目本地安装
		```sh
		npm install -g uipro-cli
		uipro init --ai antigravity
		```
+ ClaudeCode 
	直接通过命令搜索安装即可，项目本地安装可以 `uipro init --ai claude` 。

进阶使用：
这个Skill工作时会首先生成一个 MASTER.md 文档，