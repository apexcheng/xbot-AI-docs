# 当前开发指南未覆盖的影刀 / xbot API

> 来源：`xbot_robot` 项目 Python 文件扫描
> 分析日期：2026-06-08
> 分析范围：仅 `xbot_robot` 目录
> 未扫描 app 同级依赖目录

---

## 结论摘要

- 本次扫描了 `xbot_robot` 下所有 `.py` 文件
- 未扫描 app 同级依赖目录（`site-packages`、`xbot_extensions` 安装目录等）
- 发现了 **6 类** 待补充 API / 模式
- 最值得优先补充的方向：`xbot.app.dialog.show_notifycation` 通知 API

---

## 高优先级：建议补充到 docs/

### 1. `xbot.app.dialog.show_notifycation` — 桌面通知

**出现位置**：`douyin_run.py`、`jd_run.py`、`pinduoduo_run.py`、`taobao_run.py` 及所有 `*_append.py`

**当前用途**：向影刀桌面端发送通知消息（成功、失败、进度提示）

**调用方式**：
```python
from xbot.app.dialog import show_notifycation

show_notifycation(
    message="✅ 采集完成",
    placement='top', # 显示位置：'top' / 'center' 等
    level='info'         # 级别：'info' / 'warning'
)
```

**典型使用场景**：
```python
# 成功后通知
show_notifycation(message="✅ 采集完成", placement='top', level='info')

# 失败后通知
show_notifycation(message="❌ 采集失败", placement='top', level='warning')

# 进度通知
show_notifycation(message=f"📌 第{page_num}页采集中", placement='top', level='info')
```

**开发指南覆盖情况**：**完全没有提及**

**建议补充位置**：`docs/browser.md` 或新建 `docs/notification.md`

**备注**：`placement` 和 `level` 的完整可选值需运行验证

---

### 2. `xbot_extensions.ad_killer.close_ads` — 广告关闭扩展

**出现位置**：所有 `*_run.py` 和 `*_append.py`

**调用方式**：
```python
from xbot_extensions.ad_killer import close_ads

# 方式一：使用内置广告名单
close_ads(网页对象=web_page, 广告Xpath="", 使用内置广告Xpath=True, 关闭方式="hidden")

# 方式二：自定义 XPath
close_ads(网页对象=web_page, 广告Xpath="//div[@class='ad']", 使用内置广告Xpath=False, 关闭方式="click")
```

**关闭方式**：`"hidden"`（隐藏，默认值）、`"click"`（点击关闭）

**开发指南覆盖情况**：在 `extension-instructions.md` 的 `ad_killer` 章节有说明，但**参数说明不够清晰**，且未覆盖：
- `关闭方式="hidden"` vs `"click"` 的实际行为差异
- 内置广告名单的使用方法

**建议补充位置**：`docs/extension-instructions.md` 的 ad_killer 章节

---

### 3. `browser.hover()` — 鼠标悬停

**出现位置**：`douyin_run.py`（hover 在注释或元素操作中使用）

**调用方式**：
```python
element.hover(simulative=True, delay_after=0.3)
```

**参数**：
- `simulative`：是否模拟人工移动（默认 `True`）
- `delay_after`：操作后等待秒数

**开发指南覆盖情况**：`docs/browser.md` 第 11 节有点击、双击、悬停、聚焦，但只列了方法名，**没有详细参数说明**

**建议补充位置**：`docs/browser.md` 第 11 节，为 `hover()` 添加参数表格

---

### 4. `xbot.web.create` 的 `load_timeout=30` vs 默认值20

**出现位置**：所有 `*_run.py`

**发现**：旧项目几乎所有 `web.create()` 都显式传 `load_timeout=30`，而文档默认值是 `20`

**开发指南覆盖情况**：`docs/browser.md` 第 5 节写了默认值 `20`，但未说明常见场景下的推荐值

**建议**：在 `web.create()` 示例中添加"采价/登录等需要等待较久的场景建议传 `load_timeout=30`"

---

## 中优先级：可后续补充

### 5. `xbot.selector.SelectorStore` / `xbot.primitives.VariableDict` / `xbot.primitives.ResourceReader`

**出现位置**：`package.py`

**调用方式**：
```python
from xbot.selector import SelectorStore, ImageSelectorStore
from xbot.primitives import VariableDict, ResourceReader

# 获取选择器
selector = SelectorStore(name='my_selector')
image_selector = ImageSelectorStore(name='my_image_selector')

# 全局变量
glv = VariableDict()

# 资源文件读取
reader = ResourceReader()
```

**当前用途**：
- `SelectorStore` / `ImageSelectorStore`：获取影刀项目中定义的选择器
- `VariableDict`：读取影刀全局变量
- `ResourceReader`：读取项目中的资源文件（图片等）

**开发指南覆盖情况**：**完全没有提及**

**建议**：如 Agent 需要操作选择器或全局变量，应补充到 `docs/browser.md` 开头或新建文档

---

### 6. `browser.execute_javascript()` — JavaScript 执行

**出现位置**：`jd_run.py` 使用 `browser.execute_javascript()`

**调用方式**：
```python
result = browser.execute_javascript(
    """
    function (element, args) {
        return document.title;
    }
    """,
    argument=None,
    execution_world="ISOLATED"
)
```

**参数**：
- `code`：JS 函数字符串，必须是 function 形式
- `argument`：传给 JS 的参数（字符串或 JSON 字符串）
- `execution_world`：`"ISOLATED"`（插件隔离环境，大写）或 `"MAIN"`（网页主环境）

**开发指南覆盖情况**：`docs/browser.md` 第 16 节有说明，但未覆盖 `execution_world` 大小写敏感性

**建议**：`docs/browser.md` 补充大小写说明

---

### 7.登录扩展的 `process56` vs `process6` —京东登录变体

**出现位置**：
- `jd_run.py`、`jd_run_append.py` 使用 `process56`
- `douyin_run.py`、`pinduoduo_run.py` 使用 `process6`

**发现**：
- `process6` 是京东登录（通用）
- `process56` 是京麦登录（特定于京东京麦平台）

**开发指南覆盖情况**：`docs/extension-instructions.md` 第 2.4 节只列了 `process6`，**未提及 `process56`**

**建议补充位置**：`docs/extension-instructions.md` 的 activity_7bca6d 登录扩展节，补充 process56 京麦登录

**备注**：两个 process 的入参基本一致，都是 `username, password, tj_username, tj_password, ...`

---

### 8. `qn_login` 作为直接 Python 调用

**出现位置**：`taobao_run.py`、`module1.py`

**调用方式**：
```python
from xbot_extensions.activity_7bca6d import qn_login

page = qn_login.login(
    mode="普通模式",
    engine="图鉴",
    username="账号",
    password="密码",
    retry_count=3,
    token=""
)
```

**与 process7 的区别**：
- `process7` 是 Visual flow，通过 `xbot_visual.process.run()` 调用
- `qn_login.login()` 是直接 Python 调用，不经过 Visual 层

**开发指南覆盖情况**：**未提及 qn_login 的直接调用方式**

**建议补充位置**：`docs/extension-instructions.md` 的 activity_7bca6d 节

---

### 9. `xbot.print` 与 `xbot.sleep` —基础 xbot 模块

**出现位置**：所有 `*_run.py`

**调用方式**：
```python
import xbot
from xbot import print, sleep

xbot.print("消息")  # 或直接 print()
xbot.sleep(2)       # 等待 2 秒
sleep(2)
```

**开发指南覆盖情况**：`docs/browser.md` 等文档分散使用，但**没有统一说明 `xbot.print` vs Python 内置 `print`**

**建议**：在 `AGENTS.md` 或 `docs/browser.md` 开头说明：影刀编码版中 `from xbot import print` 导入的是影刀版 print（可能做特殊处理），建议直接用 Python 内置 print 或显式导入

---

### 10. `browser.dowload_url` — URL 下载文件

**出现位置**：`douyin_run.py`

**调用方式**：
```python
file_path = browser.dowload_url(
    url="https://example.com/file.xlsx",
    file_folder=r"C:\Downloads",
    file_name="result.xlsx",
    overwrite=True,
    wait_complete=True
)
```

**注意**：方法名是 `dowload_url`（有拼写错误），不是 `download_url`

**开发指南覆盖情况**：`docs/browser.md` 第 19.4 节有说明，但容易被拼写误导

**建议**：已在 `docs/browser.md` 排错速查中说明，保持现状即可

---

## 低优先级：暂不处理

### 11. `xbot.app.dialog` 其他 API

**发现**：`xbot.app.dialog` 模块下可能有其他 API（如对话框等）

**暂不处理原因**：旧项目只用了 `show_notifycation`，其他 API 未使用且不确定稳定性

---

### 12. `browser.get_url()` / `browser.get_title()` — 基础方法

**调用方式**：
```python
url = browser.get_url()
title = browser.get_title()
```

**开发指南覆盖情况**：`docs/browser.md` 第 8 节有列出，但未详细说明

**暂不处理原因**：这些是基础方法，Agent 通常能自然推断用法

---

## 可沉淀为 examples 的代码模式

> 只描述模式，不复制大段业务代码

### 模式1：打开页面后等待元素出现

```python
browser = xbot.web.create(url, mode="chrome", load_timeout=30)
browser.wait_load_completed(timeout=30)

if not browser.wait_appear(my_selector, timeout=20):
    raise RuntimeError("目标元素未出现")

element = browser.find(my_selector, timeout=1)
element.click(delay_after=0.3)
```

### 模式 2：批量采集数据到列表

```python
rows = []
elements = browser.find_all_by_xpath('//div[@class="row"]', timeout=10)
for element in elements:
    row = {
        "店铺": element.find_by_xpath('.//span[@class="shop"]', timeout=5).get_text(),
        "商品": element.find_by_xpath('.//a[@class="title"]', timeout=5).get_text(),
        "价格": element.find_by_xpath('.//span[@class="price"]', timeout=5).get_text(),
    }
    rows.append(row)
```

### 模式 3：分页循环采集

```python
while True:
    # 采集当前页
    collect_current_page(browser, rows)

    # 尝试点下一页
    next_btn = browser.find_by_xpath('//button[@class="next"]', timeout=5)
    if not next_btn or not next_btn.is_displayed():
        break

    next_btn.click(delay_after=1)
    browser.wait_load_completed(timeout=20)
```

### 模式 4：采集失败后截图并通知

```python
try:
    # 采集逻辑
    element = browser.find_by_xpath('//div[@class="item"]', timeout=10)
except Exception:
    browser.screenshot(folder_path=r"C:\screenshots", file_name=f"error_{page_num}.png")
    show_notifycation(message="❌ 采集失败", placement='top', level='warning')
    raise
```

### 模式 5：关闭广告弹窗

```python
xbot_extensions.ad_killer.close_ads(
    网页对象=web_page,
    广告Xpath="",
    使用内置广告Xpath=True,
    关闭方式="hidden"
)
```

### 模式 6：登录后保存网页对象供后续使用

```python
# process7 等登录 flow 返回 web_page
web_page = xbot_extensions.activity_7bca6d.process7(
    mode="chrome", userid="账号", password="密码", ...
)

# 后续操作使用 web_page
web_page.find_by_xpath('//input[@name="q"]', timeout=20)
```

### 模式 7：追评数据筛选（日期计算）

```python
from datetime import datetime, timedelta

append_collect_stop_time = last_gather_time - timedelta(days=90)

for comment in comments:
    first_rate_time = parse_comment_time(comment['首次评价时间'])
    if first_rate_time >= append_collect_stop_time:
        # 采集追评
        ...
```

---

## 需要运行验证的内容

1. **`show_notifycation` 的 `placement` 完整可选值**：当前只看到 `'top'`，其他值需要源码确认
2. **`show_notifycation` 的 `level` 完整可选值**：当前只看到 `'info'`、`'warning'`
3. **`xbot.selector.SelectorStore` 的详细用法**：name 参数对应什么，如何获取项目中定义的选择器
4. **`xbot.primitives.VariableDict` 的详细用法**：是否对应影刀可视化界面的全局变量
5. **`browser.hover()` 的 `simulative` 参数**：是否默认 `True`，模拟的是什么
6. **`close_ads` 的内置广告名单**：`ad_conf.py` 中有哪些域名，默认包含哪些广告
7. **`process56` vs `process6` 的入参差异**：两者是否可以互换，还是有特定场景区分
8. **`qn_login.login()` 的 `engine` 参数可选值**：除了 `"图鉴"` 还有哪些选项

---

## 不建议写入通用文档的内容

以下内容只属于旧项目业务，不应沉淀到 xbot-api-docs：

1. **平台特定字段名**：如 `店铺`、`平台`、`商品标题` 等是业务字段，不是通用 API
2. **追评截止日期计算**：`last_gather_time - timedelta(days=90)` 是业务逻辑
3. **ERP 凭证来源**：`package.variables` 中存储的 `client_id`、`client_secret` 是业务配置
4. **特定店铺判断逻辑**：如 `天猫-bow旗舰店` 等店铺名判断
5. **文件缓存路径逻辑**：`jd_sku_cache.json` 等是业务缓存文件名
6. **各平台登录账号参数**：如 `京麦账号`、`图鉴账号` 是业务参数，不是通用 API

---

## 建议下一步补充到 xbot-api-docs 的前 5 个内容

| 优先级 | 内容 | 建议补充位置 |理由 |
|---|---|---|---|
| **1** | `xbot.app.dialog.show_notifycation` 桌面通知 | `docs/browser.md` 或新建 `docs/notification.md` |几乎所有脚本都用，但文档完全没有 |
| **2** | `xbot_extensions.ad_killer.close_ads` 参数细化 | `docs/extension-instructions.md` ad_killer 节 | 参数说明不清晰，Agent 容易用错 |
| **3** | `browser.hover()` 参数说明 | `docs/browser.md` 第 11 节 | 当前只有方法名，没有参数表 |
| **4** | `process56` 京麦登录 vs `process6` 京东登录 | `docs/extension-instructions.md` activity_7bca6d 节 | 当前只列 process6，漏了 process56 |
| **5** | `qn_login` 直接 Python 调用方式 | `docs/extension-instructions.md` activity_7bca6d 节 | 提供了不经过 Visual flow 的直接调用途径 |

---

## 附录：xbot_robot 扫描到的完整 API 调用列表

### xbot 主模块

```python
import xbot
from xbot import print, sleep, web
from xbot.app.dialog import show_notifycation
from xbot.selector import SelectorStore, ImageSelectorStore
from xbot.primitives import VariableDict, ResourceReader
```

### xbot.web

```python
xbot.web.create(url, mode='chrome', load_timeout=30)
browser.wait_load_completed(timeout=30)
browser.find_by_xpath(xpath, timeout=20)
browser.find_all_by_xpath(xpath, timeout=10)
browser.get_url()
browser.close()
browser.hover(simulative=True, delay_after=0.3)
browser.click(delay_after=0.3)
browser.dowload_url(url, file_folder, file_name, overwrite, wait_complete)
browser.execute_javascript(code, argument, execution_world)
browser.screenshot(folder_path, file_name, full_size)
```

### xbot.app.dialog

```python
xbot.app.dialog.show_notifycation(message, placement, level)
```

### xbot_visual

```python
import xbot_visual
```

### xbot_extensions

```python
from xbot_extensions.activity_7bca6d import process7 as login_taobao
from xbot_extensions.activity_7bca6d import process6 as login_jd
from xbot_extensions.activity_7bca6d import process21 as login_pdd
from xbot_extensions.activity_7bca6d import process56 as login_jd_jm
from xbot_extensions.activity_7bca6d import qn_login
from xbot_extensions.activity_df0688e4 import select_order_dteail
from xbot_extensions.activity_df0688e4 import translation
from xbot_extensions.activity_5b77c4ce import general_table_action
from xbot_extensions.ad_killer import close_ads
```