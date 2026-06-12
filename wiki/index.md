# LLM Wiki 总入口

本目录用于沉淀影刀 xbot / AI 开发知识库中的跨文档总结、常见坑点、错误修正和待验证事项。

`xbot-api-docs/docs/` 是稳定 API 文档层；`wiki/` 是 LLM 维护的整理层。

## 页面索引

- [影刀项目协作与实战经验](xbot-project-practices.md): 沉淀知识库检索路径、最小改动约束、钉钉 AI 表格字段约定、通知解耦模式和真实项目收尾要求。
- [错误修正记录](error-book.md): 历史错误结论、正确说法、依据和影响范围。
- [待验证事项](unresolved.md): 还没有运行验证、源码验证或业务确认的内容。

## 使用边界

- 已确认、可直接作为开发依据的 API 说明，写 `xbot-api-docs/docs/`。
- 跨文档总结、常见坑点、历史纠错和待验证内容，先写 `wiki/`。
- 不确定的 API 行为先标 `需运行验证`，确认后再回写到 `xbot-api-docs/docs/`。
- 不要把真实项目路径、账号、token、Cookie 或其他敏感信息写入 Wiki。
