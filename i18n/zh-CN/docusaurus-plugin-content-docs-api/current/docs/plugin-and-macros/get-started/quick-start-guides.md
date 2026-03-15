# 快速入门指南

本指南提供创建 ONLYOFFICE 插件和宏的分步说明，包含三个教程：基础插件实现、宏示例，以及使用现有插件模板的说明。

> **注意：** 本指南中描述的插件和宏 API 适用于 ONLYOFFICE Docs 7.0 及更高版本。如需使用最新功能（包括宏录制和增强的 AI 集成），建议使用 9.2 或更高版本。

---

## 插件开发教程

本教程演示如何创建一个向文档中插入文本的基础插件。

**预计时间：** 5-10 分钟
**前提条件：** 具备 HTML、CSS 和 JavaScript 基础知识

### 步骤 1：创建插件结构

创建名为 `hello-world-plugin` 的文件夹，包含以下文件：

#### 插件配置文件

创建名为 `config.json` 的文件，内容如下：

```json
{
  "name": "Hello World",
  "guid": "asc.{0000-0000-0000-0001}",
  "version": "1.0.0",
  "variations": [
    {
      "url": "index.html",
      "icons": ["icon.png"],
      "isViewer": false,
      "EditorsSupport": ["word", "cell", "slide"]
    }
  ]
}
```

**配置参数：**
- `name` — 插件菜单中显示的名称
- `guid` — 唯一标识符（生产环境中必须更改）
- `url` — 入口点 HTML 文件
- `EditorsSupport` — 支持的编辑器类型（word/cell/slide）

---

#### 插件界面文件

创建名为 `index.html` 的文件，内容如下：

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Hello World Plugin</title>
    <script src="https://onlyoffice.github.io/sdkjs-plugins/v1/plugins.js"></script>
    <style>
        body {
            font-family: Arial, sans-serif;
            padding: 20px;
            text-align: center;
        }
        button {
            background: #FF6F3D;
            color: white;
            border: none;
            padding: 12px 24px;
            font-size: 16px;
            border-radius: 4px;
            cursor: pointer;
        }
        button:hover {
            background: #E55A2B;
        }
    </style>
</head>
<body>
    <h2>Hello World Plugin</h2>
    <button onclick="insertText()">Insert Hello World</button>

    <script>
        function insertText() {
            window.Asc.plugin.executeMethod("PasteText", ["Hello World! 🎉"]);
        }
    </script>
</body>
</html>
```

---

### 步骤 2：添加图标（可选）

创建或下载一个 48x48 像素的 PNG 图标，命名为 `icon.png`，放置在插件文件夹中。此图标将显示在插件菜单中。

---

### 步骤 3：安装插件

安装步骤因 ONLYOFFICE 部署方式不同而有所差异：

#### ONLYOFFICE 桌面编辑器

1. 打开 ONLYOFFICE Desktop
2. 导航至 **插件 → 设置 → 添加插件**
3. 浏览到您的 `hello-world-plugin` 文件夹
4. 点击 **确定**

[桌面版详细安装指南 →](../tutorials/installing/onlyoffice-desktop-editors/)

#### ONLYOFFICE Docs（本地部署）

1. 将插件文件夹复制到服务器插件目录：
   - Linux：`/var/www/onlyoffice/documentserver/sdkjs-plugins/`
   - Windows：`C:\Program Files\ONLYOFFICE\DocumentServer\sdkjs-plugins\`
2. 重启文档服务器
3. 刷新浏览器

[Docs 详细安装指南 →](../tutorials/installing/onlyoffice-docs-on-premises/)

---

### 步骤 4：测试插件

1. 在 ONLYOFFICE 中打开文档
2. 点击工具栏中的 **插件**
3. 选择 **Hello World**
4. 点击 **Insert Hello World** 按钮
5. 验证文档中是否出现 "Hello World! 🎉"

---

### 步骤 5：扩展插件

以下示例演示常见的插件增强方式：

#### 添加用户输入

```html
<input type="text" id="customText" placeholder="输入您的文本">
<button onclick="insertCustomText()">插入自定义文本</button>

<script>
    function insertCustomText() {
        const text = document.getElementById('customText').value;
        window.Asc.plugin.executeMethod("PasteText", [text]);
    }
</script>
```

#### 插入格式化内容

```javascript
function insertFormattedText() {
    window.Asc.plugin.executeMethod("PasteHtml", [
        "<p style='color: blue; font-weight: bold;'>Formatted Hello World!</p>"
    ]);
}
```

#### 添加多个操作

```html
<button onclick="insertHeading()">插入标题</button>
<button onclick="insertList()">插入列表</button>

<script>
    function insertHeading() {
        window.Asc.plugin.executeMethod("PasteText", ["# My Heading\n"]);
    }

    function insertList() {
        window.Asc.plugin.executeMethod("PasteText", [
            "- Item 1\n- Item 2\n- Item 3\n"
        ]);
    }
</script>
```

---

### 其他资源

**文档：**
- [插件结构与配置](../structure/configuration/configuration.md)
- [完整 API 参考](../interacting-with-editors/overview/overview.md)
- [UI 自定义选项](../customization/toolbar.md)

**示例：**
- [YouTube 插件](../samples/plugin-samples/youtube.md) — 嵌入视频
- [翻译插件](../samples/plugin-samples/translator.md) — 多语言支持
- [50+ 更多示例](../samples/plugin-samples/plugin-samples.md)

---

## 宏开发教程

本教程演示如何创建和执行用于文档自动化的宏。

**预计时间：** 2-5 分钟
**前提条件：** 具备基础 JavaScript 知识

### 步骤 1：访问宏编辑器

1. 在 ONLYOFFICE 中打开文档
2. 导航至 **工具 → 宏**
3. 宏编辑器将打开

---

### 步骤 2：创建基础宏

在宏编辑器中输入以下代码：

```javascript
(function() {
    // 获取当前文档
    var oDocument = Api.GetDocument();

    // 创建新段落
    var oParagraph = Api.CreateParagraph();
    oParagraph.AddText("This text was added by a macro! 🎯");

    // 应用格式
    oParagraph.SetBold(true);
    oParagraph.SetColor(0, 102, 204);

    // 插入到文档
    oDocument.Push(oParagraph);
})();
```

**代码说明：**
- `Api.GetDocument()` — 获取当前文档对象
- `Api.CreateParagraph()` — 创建新段落元素
- `AddText()` — 向段落添加文本内容
- `SetBold()` 和 `SetColor()` — 应用格式
- `Push()` — 将段落插入文档

---

### 步骤 3：执行宏

1. 点击宏编辑器中的 **运行** 按钮（▶️）
2. 格式化文本将出现在文档中

---

### 步骤 4：保存宏

1. 点击宏编辑器中的 **保存**
2. 输入描述性名称（例如 "插入蓝色文本"）
3. 通过 **工具 → 宏 → [宏名称]** 访问已保存的宏

---

### 宏示例

以下示例演示常见的宏使用场景：

#### 格式化标题

```javascript
(function() {
    var oDocument = Api.GetDocument();

    // 创建标题
    var oHeading = Api.CreateParagraph();
    oHeading.AddText("Chapter 1: Introduction");
    oHeading.SetBold(true);
    oHeading.SetFontSize(24);
    oHeading.SetColor(51, 51, 51);

    oDocument.Push(oHeading);
})();
```

#### 查找并高亮文本

```javascript
(function() {
    var oDocument = Api.GetDocument();
    var searchText = "important";

    // 搜索关键词
    oDocument.Search(searchText, true, false, false);

    // 高亮找到的文本
    var oRange = oDocument.GetRangeBySelect();
    if (oRange) {
        oRange.SetHighlight("yellow");
    }
})();
```

#### 创建表格

```javascript
(function() {
    var oDocument = Api.GetDocument();

    // 创建 3x3 表格
    var oTable = Api.CreateTable(3, 3);

    // 填充表头行
    oTable.GetCell(0, 0).GetContent().GetElement(0).AddText("Name");
    oTable.GetCell(0, 1).GetContent().GetElement(0).AddText("Age");
    oTable.GetCell(0, 2).GetContent().GetElement(0).AddText("City");

    // 将表格插入文档
    oDocument.Push(oTable);
})();
```

#### 计算文本统计信息

```javascript
(function() {
    var oDocument = Api.GetDocument();
    var oRange = oDocument.GetRangeBySelect();

    if (oRange) {
        var text = oRange.GetText();
        var wordCount = text.split(/\s+/).filter(Boolean).length;
        var charCount = text.length;

        // 插入统计信息
        var oParagraph = Api.CreateParagraph();
        oParagraph.AddText("\nStatistics:\n");
        oParagraph.AddText("Words: " + wordCount + "\n");
        oParagraph.AddText("Characters: " + charCount);

        oDocument.Push(oParagraph);
    }
})();
```

---

### 其他资源

**文档：**
- [完整宏指南](../macros/writing-macros.md)
- [自定义电子表格函数](../macros/adding-custom-functions.md)
- [调试宏](../macros/debugging.md)

**示例：**
- [宏示例集合](../samples/macro-samples/macro-samples.md)
- [转换 VBA 宏](../macros/converting-vba-macros.md)

---

## 使用插件模板

本节说明如何使用现有插件模板作为开发起点。

**预计时间：** 10-15 分钟
**前提条件：** Git（可选）、基础 Web 开发知识

### 步骤 1：选择模板

浏览官方 ONLYOFFICE 插件仓库：

[ONLYOFFICE 插件模板](https://github.com/ONLYOFFICE/sdkjs-plugins)

**可用模板：**
- **基础插件模板** — 自定义插件的最小结构
- **YouTube 插件** — 嵌入外部内容示例
- **翻译插件** — API 集成示例
- **OCR 插件** — 高级处理示例

---

### 步骤 2：克隆仓库

使用 Git：

```bash
# 克隆整个插件仓库
git clone https://github.com/ONLYOFFICE/sdkjs-plugins.git

# 导航到特定插件
cd sdkjs-plugins/youtube
```

也可以从 GitHub 以 ZIP 文件形式下载特定插件。

---

### 步骤 3：自定义配置

#### 修改 `config.json`

更新以下必填字段：

```json
{
  "name": "My Custom Plugin",
  "guid": "asc.{YOUR-UNIQUE-GUID-HERE}",
  "version": "1.0.0",
  "description": "Description of what your plugin does",
  "variations": [
    {
      "url": "index.html",
      "icons": ["resources/icon.png"],
      "isViewer": false,
      "EditorsSupport": ["word", "cell", "slide"]
    }
  ]
}
```

**生成唯一 GUID：**
- 使用在线 GUID 生成器
- 使用 Node.js：`node -e "console.log(require('crypto').randomUUID())"`

---

#### 修改界面

更新 `index.html` 以符合您的需求：

```html
<body>
    <h2>My Custom Plugin</h2>
    <p>Customize this interface for your use case</p>

    <!-- 在此处添加自定义 UI 元素 -->
    <button onclick="myCustomFunction()">My Action</button>

    <script>
        function myCustomFunction() {
            // 自定义逻辑
            window.Asc.plugin.executeMethod("PasteText", ["Custom text"]);
        }
    </script>
</body>
```

---

#### 更新样式

在 `styles.css` 或内联样式中自定义外观：

```css
body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: #f5f5f5;
    padding: 20px;
}

button {
    background: #4CAF50;
    color: white;
    border: none;
    padding: 10px 20px;
    border-radius: 4px;
    cursor: pointer;
}

button:hover {
    background: #45a049;
}
```

---

### 步骤 4：测试自定义插件

1. 按照[安装说明](#步骤-3安装插件)安装插件
2. 打开 ONLYOFFICE 并测试您的修改
3. 使用浏览器开发者工具（F12）进行调试

---

### 步骤 5：添加自定义功能

#### 外部 API 集成示例

```javascript
async function fetchDataFromAPI() {
    try {
        const response = await fetch('https://api.example.com/data');
        const data = await response.json();

        // 处理并插入数据
        window.Asc.plugin.executeMethod("PasteText", [JSON.stringify(data)]);
    } catch (error) {
        console.error('API Error:', error);
    }
}
```

#### 用户偏好设置示例

```javascript
// 保存用户偏好
function savePreference(key, value) {
    localStorage.setItem(key, value);
}

// 加载用户偏好
function loadPreference(key) {
    return localStorage.getItem(key);
}
```

---

### 步骤 6：打包与分发

插件分发的准备步骤：

1. **测试：** 在所有支持的编辑器（Word、Cell、Slide）中验证功能
2. **文档：** 创建用户文档
3. **打包：** 创建所有插件文件的 ZIP 压缩包
4. **分发选项：**
   - 提交到 [ONLYOFFICE 应用市场](https://www.onlyoffice.com/app-directory/en)
   - 分享到 GitHub
   - 部署到您组织的服务器

[发布指南 →](../tutorials/publishing.md)

---

### 最佳实践

**必要步骤：**
- 更改 GUID 以避免与现有插件冲突
- 更改时更新版本号
- 在所有支持的编辑器类型中测试
- 保持总文件大小在 5MB 以下
- 对大型库使用 CDN

**常见问题：**
- 使用重复 GUID（导致插件冲突）
- 跨编辑器类型测试不足
- 硬编码 URL（请使用相对路径）
- 未处理外部 API 的 CORS 问题

---

## 后续步骤

完成这些教程后，请参阅以下资源：

<table>
<tr>
<td width="33%" align="center">

### 文档

[API 参考 →](../interacting-with-editors/overview/overview.md)

[插件结构 →](../structure/configuration/configuration.md)

[最佳实践 →](../tutorials/developing/for-web-editors/)

</td>
<td width="33%" align="center">

### 交互工具

[演练场 →](playground/)

[浏览示例 →](../samples/plugin-samples/plugin-samples.md)

[UI 组件 →](https://onlyoffice.github.io/storybook/static/)

</td>
<td width="33%" align="center">

### 教程

[插件教程 →](../structure/getting-started.md)

[宏指南 →](../macros/getting-started.md)

[AI 集成 →](../ai/ai-plugin.md)

</td>
</tr>
</table>

---

## 支持

- [开发者论坛](https://forum.onlyoffice.com/) — 社区支持
- [GitHub Issues](https://github.com/ONLYOFFICE/sdkjs-plugins) — 问题报告
- [Stack Overflow](https://stackoverflow.com/questions/tagged/onlyoffice) — Q&A 档案
- [文档](../more-information/faq/) — 常见问题与指南
