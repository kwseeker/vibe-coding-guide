# Tailwind 样式

Tailwind 语法约束规则：

- **引入设计系统**
	实现样式控制的
	- 一致性
	- 可维护性
	- 可拓展
	- 可切换主题
- **设计系统已有token设置时，尽量不要使用“任意值写法”**
	因为这种写法不符合 Tailwind CSS IntelliSense 插件的 lint 建议，会在任意值写法下面显示难看的波浪线警告。比如：
	- 反例：`bg-[color:var(--background)]`
	- 正例：`bg-background`
