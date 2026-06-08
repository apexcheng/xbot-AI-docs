# 影刀 xbot API 文档整理

本仓库整理影刀 ShadowBot / xbot 编码版常用 API、调试方法和实战方案，主要供 AI Agent / Claude Code / Codex 编写自动化脚本时查阅。

## 快速入口

| 类型 | 文档 |
|---|---|
| Agent 规则 | [`AGENTS.md`](AGENTS.md) |
| LLM 索引 | [`llms.txt`](llms.txt) |
| 基础对象与全局变量 | [`docs/package.md`](docs/package.md) |
| 浏览器操作 | [`docs/browser.md`](docs/browser.md) |
| 表格操作 | [`docs/excel.md`](docs/excel.md) |
| 桌面对话框与通知 | [`docs/notification.md`](docs/notification.md) |
| 日志记录 | [`docs/logging.md`](docs/logging.md) |
| Windows 自动化 | [`docs/win32.md`](docs/win32.md) |
| 压缩解压 | [`docs/xzip.md`](docs/xzip.md) |
| 键盘鼠标 | [`docs/keyboard-mouse.md`](docs/keyboard-mouse.md) |
| 市场指令调试 | [`docs/debug/market-extension-source.md`](docs/debug/market-extension-source.md) |
| 市场指令扩展开发 | [`docs/extension-instructions.md`](docs/extension-instructions.md) |
| 价格监控方案 | [`recipes/price-monitoring.md`](recipes/price-monitoring.md) |

## 文档分区

- [`docs/`](docs/)：API 能力说明
- [`recipes/`](recipes/)：业务场景方案
- [`examples/`](examples/)：最小代码示例

## 重要约定

- 本仓库以整理后的 Markdown 文档为主。
- 不提交、不分发影刀运行包源码。
- 未验证内容必须标注 `"需运行验证"`。
- Agent 写代码前应先读 [`AGENTS.md`](AGENTS.md) 和 [`llms.txt`](llms.txt)。

## 源码参考路径

- 内置指令源码：`C:\Program Files\ShadowBot\shadowbot-6.0.30\Resources\Code-Activity\Zh-CN\xbot`
- 已安装的市场指令：`C:\Users\Administrator\AppData\Local\ShadowBot\users\859019956984664066\apps\03259a30-a8c9-47e2-b647-ccb6bc4d4206\xbot_extensions`
- 如果该用户 ID 对应目录不存在，再查同目录下其它用户的 `apps` 目录

## 核心结论速查

### 浏览器

- 原生 `xbot.web.WebBrowser` 没有 `wait_for_element`，应使用 `wait_appear` / `wait_disappear`
- 中文、长文本输入优先考虑 `clipboard_input()`

### 表格

- 只做后台读写 `.xlsx` 时，优先考虑 `openpyxl`
- 大量数据写入时，优先使用 `set_range()` 一次性写二维数组

### 键盘鼠标

- 能拿到元素时，优先用 `element.click()` / `element.input()` / `element.clipboard_input()`
- 拿不到元素时，再用 `win32.mouse_move()` / `win32.mouse_click()` / `win32.send_keys()`
