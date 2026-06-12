# 待验证事项

记录还没有运行验证、源码验证或业务确认的内容。

## 记录格式

```md
## YYYY-MM-DD 待验证标题

- 问题：
- 当前判断：
- 需要验证：
- 验证方式：
- 关联文档：
```

## 记录列表

## 2026-06-13 package 底层对象详细用法

- 问题：`xbot.selector.SelectorStore`、`xbot.selector.ImageSelectorStore`、`xbot.primitives.VariableDict`、`xbot.primitives.ResourceReader` 的底层初始化参数和返回对象行为尚未按当前影刀版本源码验证。
- 当前判断：稳定开发优先使用项目生成的 `package.selector()`、`package.image_selector()`、`package.variables`、`package.resources`，不要在业务代码中直接猜底层对象行为。
- 需要验证：`SelectorStore(name=...)` 的 `name` 与元素库名称的对应关系、`VariableDict()` 是否完整等价于当前项目全局变量、`ResourceReader()` 的资源路径解析规则。
- 验证方式：在真实影刀项目中用 `inspect.signature()`、`inspect.getfile()` 查看当前版本实现，并用最小流程运行确认。
- 关联文档：`xbot-api-docs/docs/package.md`

## 2026-06-13 通知和市场指令参数完整枚举

- 问题：部分参数已能按常见用法开发，但完整枚举仍需源码或运行验证。
- 当前判断：`show_notifycation()` 常见 `placement` / `level` 已在稳定文档记录；`process56`、`qn_login`、`close_ads` 的常见调用已在市场指令文档记录，但完整参数差异不能凭历史项目经验推断。
- 需要验证：`show_notifycation` 的 `placement` / `level` 完整可选值，`process56` 与 `process6` 的全部入参差异，`qn_login.login()` 的 `engine` 完整可选值，`close_ads` 内置广告名单与默认规则。
- 验证方式：按 `xbot-api-docs/docs/debug/market-extension-source.md` 定位当前项目 `xbot_extensions` 源码，查看 `__init__.py`、`_core.py`、`prototype.block.json` 等真实实现。
- 关联文档：`xbot-api-docs/docs/notification.md`、`xbot-api-docs/docs/extension-instructions.md`
