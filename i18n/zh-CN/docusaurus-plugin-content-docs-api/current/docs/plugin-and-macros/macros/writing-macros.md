---
sidebar_position: -5
---

# 编写宏

本指南介绍使用 Office JavaScript API 编写 ONLYOFFICE 宏的语法、常见操作和最佳实践。

## 基本语法和结构

所有宏使用此模板：

```javascript
(function () {
  "use strict";

  // 您的代码在此处
})();
```

**基本 API 对象：**

```javascript
// 文档
var oDocument = Api.GetDocument();

// 段落
var oParagraph = Api.CreateParagraph();
oParagraph.AddText("Sample text");

// 表格
var oTable = Api.CreateTable(3, 4); // 3 列，4 行

// 选择
var oRange = oDocument.GetRangeBySelect();
```

## 常见操作

### 文本处理

**插入文本：**

```javascript
(function () {
  var oDocument = Api.GetDocument();
  var oParagraph = Api.CreateParagraph();
  oParagraph.AddText("New text");
  oDocument.Push(oParagraph);
})();
```

**查找和替换：**

```javascript
(function () {
  var oDocument = Api.GetDocument();
  oDocument.Search("old text", true, false, false);
  var oRange = oDocument.GetRangeBySelect();
  if (oRange) {
    oRange.SetText("new text");
  }
})();
```

**转换选定文本：**

```javascript
(function () {
  var oDocument = Api.GetDocument();
  var oRange = oDocument.GetRangeBySelect();
  if (oRange) {
    var text = oRange.GetText();
    oRange.SetText(text.toUpperCase());
  }
})();
```

### 格式化

**文本格式化：**

```javascript
(function () {
  var oParagraph = Api.CreateParagraph();
  oParagraph.AddText("Formatted text");
  oParagraph.SetBold(true);
  oParagraph.SetItalic(true);
  oParagraph.SetFontSize(24); // 半点（24 = 12pt）
  oParagraph.SetFontFamily("Arial");
  oParagraph.SetColor(255, 0, 0); // RGB
  Api.GetDocument().Push(oParagraph);
})();
```

**段落对齐：**

```javascript
(function () {
  var oParagraph = Api.CreateParagraph();
  oParagraph.AddText("Centered text");
  oParagraph.SetJc("center"); // "left", "center", "right", "justify"
  Api.GetDocument().Push(oParagraph);
})();
```

**高亮显示：**

```javascript
(function () {
  var oDocument = Api.GetDocument();
  var oRange = oDocument.GetRangeBySelect();
  if (oRange) {
    oRange.SetHighlight("yellow");
  }
})();
```

### 表格与数据

**创建并填充表格：**

```javascript
(function () {
  var oDocument = Api.GetDocument();
  var oTable = Api.CreateTable(3, 3);

  // 表头行
  oTable.GetCell(0, 0).GetContent().GetElement(0).AddText("Name");
  oTable.GetCell(0, 1).GetContent().GetElement(0).AddText("Age");
  oTable.GetCell(0, 2).GetContent().GetElement(0).AddText("City");

  // 数据行
  oTable.GetCell(1, 0).GetContent().GetElement(0).AddText("John");
  oTable.GetCell(1, 1).GetContent().GetElement(0).AddText("30");
  oTable.GetCell(1, 2).GetContent().GetElement(0).AddText("NYC");

  oDocument.Push(oTable);
})();
```

**格式化表格单元格：**

```javascript
(function () {
  var oTable = Api.CreateTable(2, 2);
  var oCell = oTable.GetCell(0, 0);

  // 背景色
  oCell.SetShd("clear", 200, 200, 200);

  // 单元格内容
  var oCellContent = oCell.GetContent().GetElement(0);
  oCellContent.AddText("Header");
  oCellContent.SetBold(true);

  Api.GetDocument().Push(oTable);
})();
```

**表格边框：**

```javascript
(function () {
  var oTable = Api.CreateTable(3, 3);

  // 参数：类型、大小、间距、R、G、B
  oTable.SetTableBorderTop("single", 8, 0, 0, 0, 0);
  oTable.SetTableBorderBottom("single", 8, 0, 0, 0, 0);
  oTable.SetTableBorderLeft("single", 8, 0, 0, 0, 0);
  oTable.SetTableBorderRight("single", 8, 0, 0, 0, 0);
  oTable.SetTableBorderInsideH("single", 8, 0, 0, 0, 0);
  oTable.SetTableBorderInsideV("single", 8, 0, 0, 0, 0);

  Api.GetDocument().Push(oTable);
})();
```

### 文档导航

**光标移动：**

```javascript
(function () {
  var oDocument = Api.GetDocument();
  oDocument.MoveCursorToStart();
  // 或者
  oDocument.MoveCursorToEnd();
})();
```

**选择所有内容：**

```javascript
(function () {
  var oDocument = Api.GetDocument();
  oDocument.SelectAllContent();
  var oRange = oDocument.GetRangeBySelect();
})();
```

**迭代段落：**

```javascript
(function () {
  var oDocument = Api.GetDocument();
  var nCount = oDocument.GetElementsCount();

  for (var i = 0; i < nCount; i++) {
    var oElement = oDocument.GetElement(i);
    if (oElement.GetClassType() === "paragraph") {
      console.log("Paragraph " + i + ": " + oElement.GetText());
    }
  }
})();
```

## 最佳实践

### 代码组织

**使用描述性名称：**

```javascript
// 好
var headerParagraph = Api.CreateParagraph();
var customerTable = Api.CreateTable(3, 5);

// 避免
var p = Api.CreateParagraph();
var t = Api.CreateTable(3, 5);
```

**分解为函数：**

```javascript
(function () {
  function createHeader(text) {
    var oParagraph = Api.CreateParagraph();
    oParagraph.AddText(text);
    oParagraph.SetBold(true);
    oParagraph.SetFontSize(28);
    return oParagraph;
  }

  var oDocument = Api.GetDocument();
  oDocument.Push(createHeader("Document Title"));
})();
```

### 性能

**最小化循环中的 API 调用：**

```javascript
// 高效
var oDocument = Api.GetDocument(); // 只调用一次
for (var i = 0; i < 100; i++) {
  var oParagraph = Api.CreateParagraph();
  oParagraph.AddText("Line " + i);
  oDocument.Push(oParagraph);
}
```

### 错误处理

**检查空值：**

```javascript
(function () {
  var oDocument = Api.GetDocument();
  var oRange = oDocument.GetRangeBySelect();

  if (oRange) {
    var text = oRange.GetText();
    oRange.SetText(text.toUpperCase());
  } else {
    console.log("No text selected");
  }
})();
```

## 代码片段库

**全部查找并替换：**

```javascript
(function () {
  var oDocument = Api.GetDocument();
  var searchText = "old";
  var replaceText = "new";
  var count = 0;

  oDocument.MoveCursorToStart();

  while (oDocument.Search(searchText, true, false, false)) {
    var oRange = oDocument.GetRangeBySelect();
    if (oRange) {
      oRange.SetText(replaceText);
      count++;
    }
  }

  console.log("Replaced " + count + " instances");
})();
```

**字数统计：**

```javascript
(function () {
  var oDocument = Api.GetDocument();
  var oRange = oDocument.GetRangeBySelect();

  if (oRange) {
    var text = oRange.GetText();
    var words = text.split(/\s+/).filter(Boolean);

    var oParagraph = Api.CreateParagraph();
    oParagraph.AddText("\nWord count: " + words.length);
    oDocument.Push(oParagraph);
  }
})();
```

**带交替行的格式化表格：**

```javascript
(function () {
  var oDocument = Api.GetDocument();
  var oTable = Api.CreateTable(3, 5);

  // 交替行颜色
  for (var row = 0; row < 5; row++) {
    for (var col = 0; col < 3; col++) {
      var oCell = oTable.GetCell(row, col);
      if (row % 2 === 0) {
        oCell.SetShd("clear", 240, 240, 240);
      }
    }
  }

  // 边框
  oTable.SetTableBorderTop("single", 8, 0, 0, 0, 0);
  oTable.SetTableBorderBottom("single", 8, 0, 0, 0, 0);
  oTable.SetTableBorderInsideH("single", 8, 0, 0, 0, 0);
  oTable.SetTableBorderInsideV("single", 8, 0, 0, 0, 0);

  oDocument.Push(oTable);
})();
```

**对所有段落应用格式：**

```javascript
(function () {
  var oDocument = Api.GetDocument();
  var nCount = oDocument.GetElementsCount();

  for (var i = 0; i < nCount; i++) {
    var oElement = oDocument.GetElement(i);
    if (oElement.GetClassType() === "paragraph") {
      oElement.SetFontFamily("Arial");
      oElement.SetFontSize(22);
      oElement.SetJc("justify");
    }
  }
})();
```

## 使用 AI 插件生成宏 {#generating-macros-using-ai-plugin}

如果您希望加快生成宏的过程，从 9.0 版本开始，可以使用 ONLYOFFICE 内置的 AI 插件，通过描述自动生成 ONLYOFFICE 宏：

1. 配置 AI 插件。详细说明可见于[此处](/docs/plugin-and-macros/ai/ai-plugin.md#configuring)。
2. 打开**视图**选项卡，点击**宏**。
3. 在**宏**窗口中点击 **AI** 图标，选择**根据描述创建**。

    ![AI plugin](/assets/images/plugins/ai-plugin.png#gh-light-mode-only)![AI plugin](/assets/images/plugins/ai-plugin.dark.png#gh-dark-mode-only)

4. 在**根据描述创建宏**窗口中输入提示词，点击**创建**。生成的宏示例将被插入到**宏**窗口中。

    ![Create from description](/assets/images/plugins/create-from-description.png#gh-light-mode-only)![Create from description](/assets/images/plugins/create-from-description.dark.png#gh-dark-mode-only)

5. 检查代码，如有必要进行调整。

    > 尽管 AI 插件可以生成完整且可运行的宏，但并不总是完美的。请务必仔细检查输出内容并进行测试，尤其是对于复杂的宏。

6. 点击 ![Play icon](/assets/images/plugins/play.svg) 测试脚本。

## 订阅事件

要订阅指定事件并在事件触发时调用回调函数，请使用 [attachEvent](../../office-api/usage-api/text-document-api/Api/Methods/attachEvent.md) 方法。

例如，要订阅文档中超链接点击事件，请使用以下代码：

```javascript
Api.attachEvent("asc_onHyperlinkClick", () => {
  console.log("HYPERLINK!!!");
})
```

当您点击文档中任意超链接时，**asc_onHyperlinkClick** 事件将被触发，控制台中将显示 *"HYPERLINK!!!"* 消息。

![Click hyperlink](/assets/images/plugins/click-hyperlink.png)

## 分配宏

在电子表格编辑器中，可以将宏分配给图形对象：

1. 右键点击图形对象。
2. 点击**分配宏**。
3. 在弹出的窗口中选择宏，或在相应字段中输入宏名称。
4. 点击**确定**按钮。

![Assign macro](/assets/images/plugins/assign-macro.png)

要运行宏，只需点击该图形对象，即可执行对应脚本。
