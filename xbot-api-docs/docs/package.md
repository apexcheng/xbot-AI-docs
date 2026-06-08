# 影刀基础对象与全局变量

> 定位：影刀 RPA Python 编码版里的 `package` 运行时辅助对象。
> 说明：以下内容结合官方接口文档笔记整理，当前仓库未直接提取到 `package` 源码实现；若客户端版本不一致，以影刀客户端内置提示或官方文档为准。

---

## 1. 作用

`package` 主要用于访问当前应用中的：

- 元素库选择器
- 图像库选择器
- 资源文件
- 全局变量

常见写法：

```python
from . import package
```

---

## 2. `package.selector(name)`

### 作用

按“元素库里保存的名称”获取元素选择器。

### 常用场景

- 点击页面按钮
- 输入框定位
- 软件窗口控件定位
- 列表/表格元素定位

### 示例

```python
from . import package

query_btn = package.selector("按钮_查询")
query_btn.click()
```

### 注意事项

- 传入的是元素库名称，不是页面文本。
- 如果名称写错，会直接找不到选择器。
- 不要把网页中看到的文字当成元素库名称。

---

## 3. `package.image_selector(name)`

### 作用

按图像库名称获取图像选择器。

### 常用场景

- 等待图像出现
- 点击图像
- 校验图像是否存在

### 示例

```python
from . import package

logo = package.image_selector("登录成功标志")
```

### 注意事项

- 通常配合 `xbot.win32.Image` 使用。
- 图像选择器的稳定性依赖截图质量和页面状态。

---

## 4. `package.resources`

### 作用

访问应用资源文件。

### 常用场景

- 读取模板 Excel
- 读取配置文件
- 读取图片或临时数据文件

### 示例

```python
from . import package

file_path = package.resources["模板.xlsx"]
```

### 注意事项

- 具体读取方式可能随影刀版本变化。
- 若运行结果与当前版本不一致，标注“需运行验证”。

---

## 5. `package.variables`

### 作用

读写影刀应用运行时的全局变量。

### 常用场景

- 保存浏览器对象
- 保存账号、店铺、任务状态
- 在多个流程间传递数据
- 存储密钥、凭证等敏感配置

### 示例

```python
from . import package

package.variables["web_page"] = browser
web_page = package.variables["web_page"]
```

### 注意事项

- 使用前要在影刀编辑器中先创建对应变量名。
- 脚本结束后变量通常不会长期保留。
- 敏感信息优先放到全局变量，不建议硬编码。

---

## 6. 快速建议

- 需要引用元素时，用 `package.selector()`。
- 需要引用图片时，用 `package.image_selector()`。
- 需要跨流程传对象时，用 `package.variables`。
- 需要随应用打包的文件时，用 `package.resources`。
