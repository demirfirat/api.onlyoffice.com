---
sidebar_position: -4
---

# 高级功能

本指南介绍 ONLYOFFICE 的高级宏功能，包括自定义电子表格函数、事件触发宏、用户输入与对话框，以及跨文档共享宏。

## 添加自定义函数

自定义函数使用 JavaScript 逻辑扩展电子表格公式集。定义后，它们的用法与内置函数相同（例如 `=DOUBLE(5)`）。

> **注意：** 自定义函数仅在电子表格编辑器中可用。

**注册自定义函数：**

```javascript
(function () {
  Api.addCustomFunction(
    "DOUBLE", // 函数名称（在单元格中使用）
    function (x) {
      // 实现
      return x * 2;
    },
    "Multiplies a value by 2", // 描述
    [{ name: "value", description: "The number to double" }], // 参数
  );
})();
```

**在单元格中使用函数：**

注册后，在任意单元格中输入 `=DOUBLE(5)`，结果 `10` 将立即显示。

**多个参数：**

```javascript
(function () {
  Api.addCustomFunction(
    "DISCOUNT",
    function (price, rate) {
      return price - (price * rate) / 100;
    },
    "Applies a percentage discount to a price",
    [
      { name: "price", description: "Original price" },
      { name: "rate", description: "Discount rate in percent" },
    ],
  );
})();
```

**对范围进行聚合：**

```javascript
(function () {
  Api.addCustomFunction(
    "COUNTWORDS",
    function (text) {
      if (!text) return 0;
      return String(text).trim().split(/\s+/).filter(Boolean).length;
    },
    "Counts the number of words in a text string",
    [{ name: "text", description: "The text to count words in" }],
  );
})();
```

> 自定义函数名称在单元格中**不区分大小写**，但在所有已注册函数中必须**唯一**。避免使用与内置 Excel/Calc 函数冲突的名称。

## 事件触发宏

除了手动运行宏，还可以使用 [attachEvent](../../office-api/usage-api/text-document-api/Api/Methods/attachEvent.md) 方法将宏附加到文档事件。每当事件触发时，宏会自动执行。

**语法：**

```javascript
Api.attachEvent("eventName", callbackFunction);
```

**常用事件：**

| 事件                        | 触发条件                                   |
| ---------------------------- | ----------------------------------------- |
| `asc_onHyperlinkClick`       | 文档中的超链接被点击    |
| `asc_onDocumentContentReady` | 文档内容加载完成 |
| `asc_onSelectionChanged`     | 用户更改当前选择    |

**记录超链接点击：**

```javascript
(function () {
  Api.attachEvent("asc_onHyperlinkClick", function () {
    console.log("A hyperlink was clicked.");
  });
})();
```

**响应选择变化：**

```javascript
(function () {
  Api.attachEvent("asc_onSelectionChanged", function () {
    var oDocument = Api.GetDocument();
    var oRange = oDocument.GetRangeBySelect();
    if (oRange) {
      console.log("Selected text: " + oRange.GetText());
    }
  });
})();
```

**文档就绪时运行代码：**

```javascript
(function () {
  Api.attachEvent("asc_onDocumentContentReady", function () {
    var oDocument = Api.GetDocument();
    var oParagraph = Api.CreateParagraph();
    oParagraph.AddText(
      "Document loaded at: " + new Date().toLocaleTimeString(),
    );
    oDocument.Push(oParagraph);
  });
})();
```

> 事件监听器仅在当前编辑器会话期间有效。除非将宏设置为启动时运行，否则在文档重新打开后不会保留。

## 用户输入与对话框

ONLYOFFICE 宏运行在沙盒环境中，无法访问原生浏览器对话框函数（`alert`、`prompt`、`confirm`）。使用以下 API 包装器与用户交互。

**显示消息：**

```javascript
(function () {
  var oDocument = Api.GetDocument();
  var oParagraph = Api.CreateParagraph();
  oParagraph.AddText("Operation completed successfully.");
  oDocument.Push(oParagraph);
})();
```

**从单元格读取输入（电子表格）：**

```javascript
(function () {
  var oWorksheet = Api.GetActiveSheet();
  var userValue = oWorksheet.GetRange("A1").GetValue();

  if (!userValue) {
    console.log("No value found in A1. Please enter input there first.");
    return;
  }

  var oRange = oWorksheet.GetRange("B1");
  oRange.SetValue("You entered: " + userValue);
})();
```

**通过专用输入单元格收集输入：**

```javascript
(function () {
  var oSheet = Api.GetActiveSheet();

  // 提示单元格
  oSheet.GetRange("A1").SetValue("Enter your name:");

  // 用户填写后从 B1 读取响应
  var name = oSheet.GetRange("B1").GetValue();
  if (name) {
    oSheet.GetRange("A2").SetValue("Hello, " + name + "!");
  }
})();
```

> 对于更复杂的对话框需求（模态窗口、表单、下拉菜单），请考虑构建[插件](../get-started/get-started.md)而不是宏。

## 跨文档共享宏

宏默认存储在文档内部。若要在其他文档中重用宏，请使用以下方法之一。

**复制粘贴代码：**

共享宏最简单的方式是复制 JavaScript 代码并将其粘贴到另一个文档的宏编辑器中。

1. 打开宏编辑器（**工具 → 宏**）。
2. 在左侧列表中选择宏。
3. 从编辑器复制所有代码。
4. 打开目标文档并将代码粘贴到新宏中。

**将宏存储为代码片段库：**

将常用宏保存在共享的 `.js` 文件或团队 Wiki 中。需要时，将相关片段粘贴到宏编辑器中。

```javascript
// --- 片段：formatHeaderParagraph ---
// 用法：调用 formatHeaderParagraph("Title Text") 插入样式化标题。
(function () {
  function formatHeaderParagraph(text) {
    var oParagraph = Api.CreateParagraph();
    oParagraph.AddText(text);
    oParagraph.SetBold(true);
    oParagraph.SetFontSize(32);
    oParagraph.SetJc("center");
    return oParagraph;
  }

  var oDocument = Api.GetDocument();
  oDocument.Push(formatHeaderParagraph("Report Title"));
})();
```

**使用文档模板：**

保存在文档模板（`.dotx`、`.ots`）中的宏可供所有从该模板创建的文档使用。

1. 创建包含宏的文档模板。
2. 将模板文件分发给团队。
3. 从模板创建的新文档将继承所有宏。

**使用 DocumentBuilder 自动化共享：**

对于大规模或服务器端分发，使用 [ONLYOFFICE DocumentBuilder](https://api.onlyoffice.com/docs/document-builder/get-started/overview/) 以编程方式将宏注入文档。

## 后续步骤

<table>
<tr>
<td width="50%" align="center">

### 相关指南

[编写宏 →](./writing-macros.md)

[调试宏 →](./debugging.md)

[自定义函数参考 →](./adding-custom-functions.md)

</td>
<td width="50%" align="center">

### 探索示例

[宏示例 →](../samples/macro-samples/macro-samples.md)

[API 参考 →](../../office-api/get-started/overview.md)

[插件开发 →](../get-started/get-started.md)

</td>
</tr>
</table>

如有疑问或反馈，请访问[开发者论坛](https://forum.onlyoffice.com/)。
