# 影刀 Python 编码版代码风格总结

> 来源：`xbot_robot` 项目代码分析
> 分析日期：2026-06-08
> 仅分析 `xbot_robot` 目录，不分析同级依赖目录

---

## 总体原则

- 代码优先可读，流程优先直观
- 不过度封装，不提前优化未知问题
- 只处理当前业务明确需要的场景
- 保持现有项目风格，修改时只做最小必要改动
- 不把旧项目业务逻辑迁移到开发指南

---

## 代码组织习惯

### 文件结构

```
xbot_robot/
├── __init__.py          # 极简，仅 import package
├── package.py           # 全局变量、selector、resource 读取入口
├── *_run.py             # 各平台主流程（douyin_run、jd_run、pinduoduo_run、taobao_run）
├── *_run_append.py      # 追评采集流程
├── *_collect_tools.py   # 采集工具函数（comment_collect_tools、dingtalk_ai_table）
├── *_file_tools.py      # 文件操作工具
├── qeury_jd_sku.py # JD SKU 查询与缓存
├── rate_collect_stats.py # 采集统计
├── collect_comment_cache.py # 评论缓存
├── tools.py             # 工具函数
└── main.pybx # 编译后的主流程
```

### 模块职责

- `*_run.py`：主入口 main(args)，负责启动浏览器、登录、循环采数据
- `*_collect_tools.py`：业务逻辑，ERP 查询、数据写入钉钉表
- `*_file_tools.py`：文件上传下载
- `package.py`：glv 全局变量读取、selector 获取
- `*_cache.py`：本地文件缓存
- `tools.py`：纯工具函数，无 xbot 依赖

###复用方式

- 跨文件复用通过 `from .xxx import *` 或 `from .xxx import func`
- 主流程 `*_run.py` 通过 `from .comment_collect_tools import *` 引入所有采集函数
- 跨平台共用同一套 comment_collect_tools，但各平台独立登录逻辑

---

## 变量命名习惯

### 中文命名

- 变量名用中文拼音或英文直译：`data`、`page_num`、`page_size`
- 业务字段用中文：`店铺`、`平台`、`商品标题`、`评价内容`
- 函数名用英文或拼音：`get_collect_cache_file`、`make_collect_cache_key`

### 全局变量

```python
from .package import variables as glv

# glv 存储：配置、凭证、缓存、统计
glv['client_id']
glv['client_secret']
glv['COLLECT_STATS']  # 统计字典
```

### 业务变量

```python
collect_cache = {}  # 采集缓存
append_collect_stop_time = last_gather_time - timedelta(days=90)  # 追评截止时间
```

---

## 主流程写法

### 标准主流程结构（douyin_run.py）

```python
def main(args):
    # 1. 参数校验
    if not args平台:
        return

    # 2. 激活浏览器
    activate_browser = __import__('process25', fromlist=['']).activate_browser
    activate_browser.main(args)

    # 3. 创建浏览器
    browser = xbot.web.create(url, mode='chrome', load_timeout=30)

    # 4. 登录（平台相关）
    login_taobao = __import__('process7', ...).login_taobao
    web_page = login_taobao.main(账号, 密码, ...)

    # 5. 关闭广告
    xbot_extensions.ad_killer.close_ads(web_page, ...)

    # 6. 循环页面
    for page_num in range(1, max_pages + 1):
        # 循环行
        for row in rows:
            # 采集数据
            # 写入钉钉
        browser.wait_load_completed()

    # 7. 统计通知
    xbot.app.dialog.show_notifycation(message="✅ 完成", placement='top', level='info')
```

### 循环结构

- 页面循环：`for page_num in range(1, max_pages + 1)`
- 行循环：`for row in elements`
- 重试逻辑：通常写在循环内部或单独函数中，不做外层大循环

---

## 函数拆分习惯

### 拆分原则

- 重复出现2 次以上、或逻辑复杂且明确需要时，才抽函数
- 不为少量逻辑拆很多函数
- 主流程顺序式写法，不强制抽函数

### 典型函数拆分

```python
# 工具函数（tools.py）- 纯 Python，无 xbot 依赖
def get_append_rate_time(text: str) -> datetime: ...

# 采集工具（comment_collect_tools.py）- 有 xbot 调用
def write_comment_to_dingtalk_ai_table(record): ...

# 缓存工具（collect_comment_cache.py）- 有 xbot 调用
def save_collect_cache(): ...
```

### 不拆分的例子

- 主流程通常不抽函数，直接顺序写
- 简单数据转换直接在循环内处理

---

## 浏览器操作习惯

### 创建浏览器

```python
browser = xbot.web.create(
    url="https://xxx.com",
    mode="chrome",
    load_timeout=30
)
```

### 等待页面加载

```python
browser.wait_load_completed(timeout=30)
```

### 查找元素

```python
element = browser.find_by_xpath('//div[@class="item"]', timeout=20)
elements = browser.find_all_by_xpath('//div[@class="row"]', timeout=10)
```

### 点击操作

```python
element.click(delay_after=0.3)
element.hover(simulative=True, delay_after=0.3)
```

### 关闭广告

```python
xbot_extensions.ad_killer.close_ads(
    网页对象=web_page,
    广告Xpath="",
    使用内置广告Xpath=True,
    关闭方式="hidden"
)
```

---

## 表格操作习惯

- 本项目未直接使用 xbot.excel，采集结果直接写入钉钉表
- 若使用表格，优先 `sheet.set_range()` 批量写入，不逐单元格写入

---

## 键盘鼠标操作习惯

-元素操作优先用 `element.click()` / `element.input()`
- 中文或长文本输入优先 `clipboard_input()`
- 坐标点击仅在无法获取元素时使用

---

## 异常处理习惯

### 标准模式

```python
try:
    # 业务逻辑
except Exception:
    print(f"❌ 错误：{traceback.format_exc()}")
    xbot.app.dialog.show_notifycation(
        message="❌ 采集失败",
        placement='top',
        level='warning'
    )
```

### 特点

- 只捕获明确需要处理的异常
- 不添加复杂重试框架
- 异常时打印 traceback + 发送通知
- 不做全局异常捕获

---

## print / 日志使用习惯

### print 用法

```python
print(f"第{page_num}页，共{total}条")
print(f"✅ 采集成功：{order_id}")
print(f"❌ 失败：{error}")
print(f"📌 重试第{retry}次")
```

### 日志格式

- f-string 格式化
-  Emoji 状态标记：✅ ❌ ⭐ 📌 🏬 📊
- 分页进度用 `第N页/共M页`
- 不使用日志框架（logging）

---

## 通知消息习惯

### 影刀通知

```python
from xbot.app.dialog import show_notifycation

# 成功通知
show_notifycation(message="✅ 采集完成", placement='top', level='info')

# 警告通知
show_notifycation(message="❌ 部分失败", placement='top', level='warning')

# 进度通知
show_notifycation(message=f"📌 第{page_num}页", placement='top', level='info')
```

### 参数

- `message`：消息内容，支持 Emoji
- `placement`：`'top'` / `'center'` 等
- `level`：`'info'` / `'warning'`

---

## Agent 写代码时应遵守

1. **简单直接**：不引入复杂抽象，一件事做完了再管下一件
2. **业务流程优先**：变量名、流程、判断逻辑要直观
3. **不过度封装**：只有重复2+ 次或逻辑复杂时才抽函数
4. **中文优先**：变量名、注释用中文或英文直译，不用奇怪的缩写
5. **异常只处理明确的**：不写 `except Exception`捕获一切
6. **print替代日志**：用 print f-string 做进度标记，不用 logging
7. **优先元素操作**：能用 `element.click()` 就不要用坐标
8. **中文输入用剪贴板**：`clipboard_input()`
9. **批量写表格**：`set_range()` 而非循环 `set_cell()`
10. **修改只做最小改动**：不要因为觉得"以后可能用到"就加代码

---

## 不建议继承到新项目的内容

1. **跨文件 `from .xxx import *`**：隐式依赖多，建议显式 import
2. **动态 import登录模块**：`__import__('process7', ...)` 写法不直观，建议直接 import
3. **未分类的全局 atexit 钩子**：`collect_comment_cache.py` 用 atexit 做自动保存，小项目可以，大项目建议显式管理
4. **硬编码业务字段**：如店铺名、平台代码等，不应沉淀为通用模式
5. **未使用 types 或注解**：纯动态 Python，建议保持一致风格，不强制加类型