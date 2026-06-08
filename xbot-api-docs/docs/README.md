# API 文档

`docs/` 目录存放影刀 xbot 编码版的 API 能力说明文档。

## 文档入口

| 模块 | 文档 | 说明 |
|---|---|---|
| 基础对象与全局变量 | [package.md](package.md) | 元素库、图像库、资源文件、全局变量的常用写法 |
| 浏览器操作 | [browser.md](browser.md) | 打开网页、获取网页、查找元素、点击、输入、等待、下载、上传、截图、Cookie、网络监听等 |
| 表格操作 | [excel.md](excel.md) | 创建/打开 Excel、读取、写入、Sheet 操作、区域操作、格式、复制粘贴、宏、透视表、导出 PDF 等 |
| 桌面对话框与通知 | [notification.md](notification.md) | `xbot.app.dialog` 的对话框、通知、选择器相关方法 |
| 日志记录 | [logging.md](logging.md) | `xbot.app.logging` 的日志级别输出与导出 |
| Windows 自动化 | [win32.md](win32.md) | `xbot.win32` 的窗口、鼠标、键盘、锁屏相关方法 |
| 压缩解压 | [xzip.md](xzip.md) | `xbot.xzip` 的压缩与解压方法 |
| 键盘鼠标 | [keyboard-mouse.md](keyboard-mouse.md) | 全局键盘输入、鼠标移动、点击、滚轮、元素点击、元素输入、剪切板输入等 |
| 市场指令调试 | [debug/market-extension-source.md](debug/market-extension-source.md) | 市场指令文档不清楚、可视化能跑但编码版异常时，定位 `xbot_extensions` 并查看真实实现逻辑 |
| 市场指令扩展开发 | [extension-instructions.md](extension-instructions.md) | 7 个常用市场指令目录的结构分析、调用规则、参数映射和开发指南 |

## 使用原则

1. **先查文档，再写代码。** 不确定时，先打开对应模块的文档，找到方法签名和参数说明。
2. **文档不明确时，不要猜。** 优先查看 `debug/market-extension-source.md` 排查源码。
3. **市场指令参数不明确时**，查看 `debug/market-extension-source.md`，用 `inspect.getfile()` 定位真实实现。
4. **未验证内容必须标注** `"需运行验证"`，不要包装成确定结论。
