# 影刀键盘鼠标操作方法整理

> 定位：影刀 / xbot 键盘鼠标操作速查文档。  
> 重点：区分**全局键鼠**和**元素级键鼠**，优先使用元素方法；只有拿不到元素时，再使用坐标和当前激活窗口。  
> 规则：字符串参数必须按文档中的值原样传入，例如 `button="left"`，不是 `Left` / `LEFT`。

---

## 1. 相关文件位置

| 路径 | 作用 |
|---|---|
| `C:\Program Files\ShadowBot\shadowbot-6.0.30\Resources\Code-Activity\Zh-CN\xbot\win32\__init__.py` | 全局键盘、鼠标移动、鼠标点击、滚轮、获取鼠标位置 |
| `C:\Program Files\ShadowBot\shadowbot-6.0.30\Resources\Code-Activity\Zh-CN\xbot\web\element.py` | 网页元素点击、双击、输入、剪切板输入、悬停 |
| `C:\Program Files\ShadowBot\shadowbot-6.0.30\Resources\Code-Activity\Zh-CN\xbot\win32\element.py` | Win32 元素点击、双击、输入、剪切板输入、悬停 |

---

## 2. 使用层级

| 层级 | 推荐使用场景 | 典型方法 | 说明 |
|---|---|---|---|
| 网页元素级 | 已经拿到网页元素 | `element.click()` / `element.input()` / `element.clipboard_input()` | 最推荐，稳定性通常比坐标好 |
| Win32 元素级 | 已经拿到窗口控件元素 | `element.click()` / `element.input()` / `element.clipboard_input()` | 适合普通 Windows 软件 |
| 全局键鼠 | 拿不到元素，只能操作当前激活窗口或坐标 | `win32.send_keys()` / `win32.mouse_move()` / `win32.mouse_click()` | 依赖焦点和坐标，稳定性较弱 |

推荐顺序：

```text
能拿到元素 → 用元素级方法
拿不到元素 → 用全局键鼠
中文或长文本 → 优先 clipboard_input()
快捷键或特殊按键 → 优先 send_keys()
```

---

## 3. 常用方法

### 3.1 全局键盘输入：`win32.send_keys()`

```python
from xbot import win32

win32.send_keys(
    keys="测试内容",
    send_key_delay=50,
    hardware_driver_input=False,
    delay_after=1,
    contains_hotkey=True,
    force_ime_eng=False,
)
```

| 参数 | 类型 | 默认值 | 说明 |
|---|---|---|---|
| `keys` | `str` | `""` | 要发送的键盘内容 |
| `send_key_delay` | `int` | `50` | 两次按键之间的间隔，单位 ms |
| `hardware_driver_input` | `bool` | `False` | 是否使用硬件驱动输入 |
| `delay_after` | `int` / `float` | `1` | 执行后等待秒数 |
| `contains_hotkey` | `bool` | `True` | 内容是否包含快捷键 |
| `force_ime_eng` | `bool` | `False` | 是否强制切换英文输入状态 |

常用示例：

```python
from xbot import win32

# 普通输入
win32.send_keys("hello", delay_after=0)

# 尽量避免中文输入法干扰
win32.send_keys("abc123", force_ime_eng=True, delay_after=0)

# 当前焦点输入 Enter
win32.send_keys("{ENTER}", delay_after=0)

# 当前焦点输入 Tab
win32.send_keys("{TAB}", delay_after=0)
```

注意：`hardware_driver_input=True` 时，只适合键盘可见字符；不要用它发送 `Tab`、`Ctrl`、`Enter`、`Shift` 等特殊按键。

---

### 3.2 全局鼠标移动：`win32.mouse_move()`

```python
from xbot import win32

win32.mouse_move(
    point_x=500,
    point_y=300,
    relative_to="screen",
    move_speed="instant",
    delay_after=1,
)
```

| 参数 | 类型 | 默认值 | 可选值 | 说明 |
|---|---|---|---|---|
| `point_x` | `int` | 必填 | 整数 | 横坐标 |
| `point_y` | `int` | 必填 | 整数 | 纵坐标 |
| `relative_to` | `str` | `"screen"` | `"screen"` / `"window"` / `"position"` | 坐标相对位置 |
| `move_speed` | `str` | `"instant"` | `"instant"` / `"fast"` / `"middle"` / `"slow"` | 鼠标移动速度 |
| `delay_after` | `int` / `float` | `1` | 秒数 | 执行后等待 |

常用示例：

```python
from xbot import win32

# 移动到屏幕坐标 500, 300
win32.mouse_move(500, 300, relative_to="screen", delay_after=0)

# 相对当前激活窗口移动
win32.mouse_move(100, 80, relative_to="window", delay_after=0)
```

---

### 3.3 全局鼠标点击：`win32.mouse_click()`

```python
from xbot import win32

win32.mouse_click(
    button="left",
    click_type="click",
    hardware_driver_click=False,
    keys="none",
    delay_after=1,
)
```

| 参数 | 类型 | 默认值 | 可选值 | 说明 |
|---|---|---|---|---|
| `button` | `str` | `"left"` | `"left"` / `"right"` | 鼠标左键或右键 |
| `click_type` | `str` | `"click"` | `"click"` / `"dbclick"` / `"down"` / `"up"` | 单击、双击、按下、弹起 |
| `hardware_driver_click` | `bool` | `False` | `True` / `False` | 是否使用硬件驱动点击 |
| `keys` | `str` | `"none"` | `"none"` / `"alt"` / `"ctrl"` / `"shift"` / `"win"` | 点击时按住的辅助键 |
| `delay_after` | `int` / `float` | `1` | 秒数 | 执行后等待 |

常用示例：

```python
from xbot import win32

# 左键单击
win32.mouse_click(button="left", click_type="click", delay_after=0)

# 左键双击
win32.mouse_click(button="left", click_type="dbclick", delay_after=0)

# 右键单击
win32.mouse_click(button="right", click_type="click", delay_after=0)

# Ctrl + 左键单击
win32.mouse_click(button="left", click_type="click", keys="ctrl", delay_after=0)
```

---

### 3.4 指定矩形区域点击：`win32.mouse_click_by_anchor()`

```python
from xbot import win32

win32.mouse_click_by_anchor(
    rectangle=(100, 200, 300, 40),
    anchor=("middleCenter", 0, 0),
    button="left",
    click_type="click",
    delay_after=1,
)
```

| 参数 | 说明 |
|---|---|
| `rectangle` | 目标矩形范围，格式为 `(x, y, width, height)` |
| `anchor` | 点击位置和偏移量，格式为 `("middleCenter", 0, 0)` |
| `button` | `"left"` / `"right"` |
| `click_type` | `"click"` / `"dbclick"` / `"down"` / `"up"` |

常见锚点：

```text
topLeft / topCenter / topRight
middleLeft / middleCenter / middleRight
bottomLeft / bottomCenter / bottomRight
random
```

---

### 3.5 全局鼠标滚轮：`win32.mouse_wheel()`

```python
from xbot import win32

win32.mouse_wheel(
    wheel_direction="down",
    wheel_times=3,
    keys="none",
    delay_after=1,
)
```

| 参数 | 类型 | 默认值 | 可选值 | 说明 |
|---|---|---|---|---|
| `wheel_direction` | `str` | `"down"` | `"up"` / `"down"` | 滚轮方向 |
| `wheel_times` | `int` | `1` | 正整数 | 滚动次数 |
| `keys` | `str` | `"none"` | `"none"` / `"alt"` / `"ctrl"` / `"shift"` / `"win"` | 滚轮时按住的辅助键 |
| `delay_after` | `int` / `float` | `1` | 秒数 | 执行后等待 |

示例：

```python
from xbot import win32

# 向下滚动 3 次
win32.mouse_wheel("down", 3, delay_after=0)

# 向上滚动 2 次
win32.mouse_wheel("up", 2, delay_after=0)
```

---

### 3.6 获取鼠标位置：`win32.get_mouse_position()`

```python
from xbot import win32

x, y = win32.get_mouse_position(relative_to="screen")
```

| 参数 | 默认值 | 可选值 | 说明 |
|---|---|---|---|
| `relative_to` | `"screen"` | `"screen"` / `"window"` | 相对屏幕或当前激活窗口 |

---

### 3.7 网页元素点击：`WebElement.click()` / `dblclick()`

```python
# 单击网页元素
element.click(
    button="left",
    simulative=True,
    keys="none",
    delay_after=1,
    move_mouse=False,
    anchor=None,
)

# 双击网页元素
element.dblclick(
    simulative=True,
    delay_after=1,
    move_mouse=False,
    anchor=None,
)
```

| 参数 | 默认值 | 说明 |
|---|---|---|
| `button` | `"left"` | 鼠标左键或右键 |
| `simulative` | `True` | 是否模拟人工点击 |
| `keys` | `"none"` | 辅助键，支持 `alt` / `ctrl` / `shift` / `win` |
| `move_mouse` | `False` | 是否显示鼠标移动轨迹 |
| `anchor` | `None` | 点击锚点和偏移量 |
| `delay_after` | `1` | 执行后等待 |

---

### 3.8 网页元素输入：`WebElement.input()`

```python
element.input(
    text="测试内容",
    simulative=True,
    cdp_input=False,
    driver_input=False,
    append=False,
    contains_hotkey=False,
    force_ime_ENG=False,
    send_key_delay=50,
    focus_timeout=1000,
    delay_after=1,
    click_before_input=True,
)
```

| 参数 | 默认值 | 说明 |
|---|---|---|
| `text` | 必填 | 输入内容 |
| `simulative` | `True` | 是否模拟人工输入 |
| `cdp_input` | `False` | 是否极速直写输入 |
| `driver_input` | `False` | 是否驱动输入 |
| `append` | `False` | 是否追加输入 |
| `contains_hotkey` | `False` | 输入内容是否包含快捷键 |
| `force_ime_ENG` | `False` | 是否强制切换英文输入状态 |
| `send_key_delay` | `50` | 按键间隔，单位 ms |
| `focus_timeout` | `1000` | 焦点超时时间，单位 ms |
| `click_before_input` | `True` | 输入前是否点击元素 |

常用示例：

```python
# 覆盖输入
element.input("测试内容", delay_after=0)

# 追加输入
element.input("追加内容", append=True, delay_after=0)

# 输入快捷键内容
element.input("^a", contains_hotkey=True, delay_after=0)
```

---

### 3.9 网页元素剪切板输入：`WebElement.clipboard_input()`

```python
element.clipboard_input(
    text="中文长文本",
    append=False,
    focus_timeout=1000,
    delay_after=1,
    send_key_delay=50,
    click_before_input=True,
)
```

推荐场景：

| 场景 | 推荐原因 |
|---|---|
| 中文输入 | 减少输入法干扰 |
| 长文本输入 | 比逐字模拟输入更稳 |
| 复制粘贴型表单 | 更接近人工 Ctrl + V 输入 |

---

### 3.10 Win32 元素点击和输入

Win32 元素方法和网页元素类似：

```python
# 点击
element.click(button="left", delay_after=0)

# 双击
element.dblclick(delay_after=0)

# 普通输入
element.input("测试内容", delay_after=0)

# 剪切板输入，推荐中文和长文本
element.clipboard_input("中文长文本", delay_after=0)
```

---

## 4. 常用模板

### 4.1 推荐：找到元素后输入中文

```python
# 网页元素 / Win32 元素都优先考虑剪切板输入
element.clipboard_input("需要输入的中文内容", delay_after=0)
```

---

### 4.2 找到元素后点击

```python
# 默认点击元素中心
element.click(delay_after=0)

# 点击元素左上角偏移 10, 10 的位置
element.click(anchor=("topLeft", 10, 10), delay_after=0)
```

---

### 4.3 只能坐标点击时

```python
from xbot import win32

win32.mouse_move(500, 300, relative_to="screen", delay_after=0)
win32.mouse_click(button="left", click_type="click", delay_after=0)
```

---

### 4.4 当前焦点输入并回车

```python
from xbot import win32

win32.send_keys("查询内容", delay_after=0)
win32.send_keys("{ENTER}", delay_after=0)
```

---

### 4.5 滚动页面或列表

```python
from xbot import win32

# 先把鼠标移到目标区域，再滚动
win32.mouse_move(800, 500, delay_after=0)
win32.mouse_wheel("down", 5, delay_after=0)
```

---

## 5. 注意事项

### 5.1 优先元素，不优先坐标

坐标点击依赖分辨率、缩放、窗口位置、页面滚动位置，稳定性较弱。

推荐：

```python
element.click()
element.clipboard_input("内容")
```

不优先：

```python
win32.mouse_move(500, 300)
win32.mouse_click()
```

---

### 5.2 中文、长文本优先 `clipboard_input()`

普通模拟输入容易受中文输入法影响。中文、长文本、带换行内容，优先用：

```python
element.clipboard_input(text)
```

---

### 5.3 全局 `send_keys()` 依赖当前焦点

`win32.send_keys()` 是给当前激活窗口发送键盘内容。执行前要确认：

```text
目标窗口已激活
目标输入框已有焦点
没有弹窗遮挡
```

---

### 5.4 字符串可选值区分大小写

正确：

```python
button="left"
click_type="click"
relative_to="screen"
move_speed="instant"
keys="ctrl"
```

错误：

```python
button="Left"       # 错
click_type="Click"  # 错
relative_to="Screen"  # 错
```

---

### 5.5 `delay_after=0` 可减少等待

默认很多方法执行后会等 1 秒。批量操作时，可以显式设置：

```python
element.click(delay_after=0)
win32.send_keys("abc", delay_after=0)
```

---

## 6. 排错速查

| 问题 | 优先检查 |
|---|---|
| 输入到错误位置 | 当前窗口是否激活、输入框是否有焦点 |
| 中文输入乱码或丢字 | 改用 `clipboard_input()` |
| 坐标点击偏移 | 检查分辨率、缩放、窗口位置、浏览器缩放 |
| `send_keys()` 无效果 | 检查焦点、弹窗遮挡、是否在远程桌面锁屏状态 |
| 双击没触发 | 试 `click_type="dbclick"` 或 `element.dblclick()` |
| 滚轮无效果 | 鼠标先移动到目标滚动区域 |
| 特殊按键无效 | 不要用 `hardware_driver_input=True` 发送特殊按键 |
| 点击被遮挡 | 检查浮层、弹窗、页面加载状态 |

---

## 7. 最小推荐写法

### 网页 / Win32 元素输入

```python
element.clipboard_input("中文内容", delay_after=0)
```

### 网页 / Win32 元素点击

```python
element.click(delay_after=0)
```

### 坐标点击

```python
from xbot import win32

win32.mouse_move(500, 300, delay_after=0)
win32.mouse_click(delay_after=0)
```

### 当前焦点键盘输入

```python
from xbot import win32

win32.send_keys("输入内容", delay_after=0)
```
