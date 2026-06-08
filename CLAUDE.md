@AGENTS.md

## Claude Code 额外规则

- 这是 Claude Code 的专用入口；优先遵循根目录 `AGENTS.md` 和 `llms.txt`。
- 开始写影刀代码前，先阅读 `llms.txt` 和 `xbot-api-docs/llms.txt`。
- 需要浏览器操作时，参考 `xbot-api-docs/docs/browser.md`。
- 需要表格操作时，参考 `xbot-api-docs/docs/excel.md`。
- 市场指令参数不明确时，参考 `xbot-api-docs/docs/debug/market-extension-source.md`。
- 不要大面积重构现有代码，只做当前任务需要的最小修改。
- 优先写可读、直接、低维护的代码。
- 不要过度工程化，不要为了当前简单需求引入复杂抽象。
- 只基于当前业务、当前代码和当前需求修改。
- 不要优化未知问题，不要提前处理未出现或未验证的场景。
- 不要擅自改动无关文件、无关格式、无关命名或无关结构。
- 如果发现额外问题，先指出问题和建议，不要直接扩大修改范围。
- Claude Code 只负责补充专用入口说明，不承担跨工具的通用规则维护。
