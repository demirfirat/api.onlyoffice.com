---
sidebar_position: -3
---

# 调试宏

本指南介绍如何查找和修复 ONLYOFFICE 宏中的问题，涵盖控制台日志记录、错误消息、常见错误和测试策略。

## 控制台日志记录

`console.log()` 是检查值和追踪宏执行的主要工具。输出显示在宏编辑器的**控制台**选项卡中。

**记录简单值：**

```javascript
(function () {
  var oDocument = Api.GetDocument();
  var nCount = oDocument.GetElementsCount();
  console.log("Element count: " + nCount);
})();
```

**在循环中记录：**

```javascript
(function () {
  var oDocument = Api.GetDocument();
  var nCount = oDocument.GetElementsCount();

  for (var i = 0; i < nCount; i++) {
    var oElement = oDocument.GetElement(i);
    console.log("Element " + i + " type: " + oElement.GetClassType());
  }
})();
```

**记录对象属性：**

```javascript
(function () {
  var oDocument = Api.GetDocument();
  var oRange = oDocument.GetRangeBySelect();

  if (oRange) {
    console.log("Selected text: " + oRange.GetText());
    console.log("Text length: " + oRange.GetText().length);
  } else {
    console.log("Nothing is selected.");
  }
})();
```

**添加检查点追踪执行流程：**

```javascript
(function () {
  console.log("[1] Macro started");

  var oDocument = Api.GetDocument();
  console.log("[2] Document retrieved");

  var oParagraph = Api.CreateParagraph();
  console.log("[3] Paragraph created");

  oParagraph.AddText("Debug checkpoint test");
  oDocument.Push(oParagraph);
  console.log("[4] Macro finished");
})();
```

> 使用编号或标记的检查点（例如 `[1]`、`[INIT]`），在宏静默失败时快速定位执行停止的位置。

## 使用调试器

您可以使用 `debugger` 语句在任意位置暂停宏执行。当浏览器开发者工具打开时，这会充当断点——执行在该行暂停，您可以检查变量值、调用栈，并逐步执行代码。

### 使用 `debugger` 设置断点

在您希望暂停执行的行插入 `debugger` 语句：

<!-- This code is related to macros. -->

<!-- eslint-skip -->

```ts
debugger;
let doc = Api.GetDocument();
let paragraph = doc.GetElement(0);
paragraph.AddText("Hello world!");
```

在调试模式下运行脚本：

1. 打开**视图**选项卡并点击**宏**。
2. 在脚本中插入 `debugger` 语句。
3. 打开开发者工具：
   - **对于 ONLYOFFICE Docs**：按 **F12** 打开浏览器开发者控制台。
   - **对于桌面编辑器**：请参阅[在 ONLYOFFICE 桌面编辑器中以调试模式运行的说明](../../desktop-editors/usage-api/debugging/running-in-debug-mode-on-windows.md)。
4. 点击 ![Play icon](/assets/images/plugins/play.svg) 运行脚本。

> `debugger` 语句仅在开发者工具打开时有效。否则，浏览器会静默忽略它。

![Debugger](/assets/images/plugins/debugger.png#gh-light-mode-only)![Debugger](/assets/images/plugins/debugger.dark.png#gh-dark-mode-only)

### 将值记录到控制台

如果您只需要检查特定值而不暂停执行，请使用 `console.log()`。将您要检查的值或消息作为参数传入，然后按 **F12** 在浏览器开发者控制台中查看输出：

<!-- This code is related to macros. -->

<!-- eslint-skip -->

```ts
console.log(123);
let doc = Api.GetDocument();
let paragraph = doc.GetElement(0);
paragraph.AddText("Hello world!");
```

## 错误消息说明

宏失败时，错误消息会显示在控制台或编辑器通知中。下表列出了最常见的消息及其原因。

| 错误消息                                  | 原因                                                                                                                          |
| ---------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| `Cannot read property '...' of null`           | 对 `null` 对象调用方法，例如当没有选中内容时 `GetRangeBySelect()` 返回 `null` |
| `... is not a function`                        | 对对象调用不存在的方法，或将 `undefined` 作为函数调用                                        |
| `... is not defined`                           | 在声明前使用变量（严格模式下必须声明）                                                                 |
| `RangeError: Maximum call stack size exceeded` | 无限递归——函数在没有基本情况的情况下调用自身                                                               |
| `SyntaxError: Unexpected token`                | 代码中的拼写错误——缺少括号、逗号或引号                                                                          |
| `Access denied`                                | 尝试访问受限对象，如 `window` 或 `document`                                                        |

**调用方法前进行空值检查：**

```javascript
(function () {
  var oDocument = Api.GetDocument();
  var oRange = oDocument.GetRangeBySelect();

  // 使用结果前始终检查空值
  if (!oRange) {
    console.log(
      "Error: No text is selected. Please select text and run again.",
    );
    return;
  }

  console.log("Selected: " + oRange.GetText());
})();
```

**将高风险代码包裹在 try/catch 中：**

```javascript
(function () {
  try {
    var oDocument = Api.GetDocument();
    var oTable = oDocument.GetElement(0);
    var oCell = oTable.GetCell(0, 0); // 如果元素不是表格则失败
    console.log(oCell.GetContent().GetText());
  } catch (e) {
    console.log("Error: " + e.message);
  }
})();
```

## 常见错误

**忘记声明变量：**

所有变量必须用 `var`、`let` 或 `const` 声明。未声明的变量在严格模式下会抛出 `ReferenceError`。

```javascript
// 错误——在严格模式下抛出 ReferenceError
(function () {
  "use strict";
  oDocument = Api.GetDocument(); // 缺少 var
})();

// 正确
(function () {
  "use strict";
  var oDocument = Api.GetDocument();
})();
```

**在没有选择内容时使用 `GetRangeBySelect()`：**

当没有选中文本时，`GetRangeBySelect()` 返回 `null`。始终对此进行防护。

```javascript
// 错误——如果没有选中内容则崩溃
(function () {
  var oRange = Api.GetDocument().GetRangeBySelect();
  oRange.SetText("Hello"); // 如果 oRange 为 null 则崩溃
})();

// 正确
(function () {
  var oRange = Api.GetDocument().GetRangeBySelect();
  if (oRange) {
    oRange.SetText("Hello");
  }
})();
```

**调用当前编辑器中不可用的 API：**

某些 API 方法特定于一种编辑器类型（文档、电子表格或演示文稿）。在文档编辑器中调用仅电子表格可用的方法会返回 `undefined` 或抛出错误。

```javascript
// 这只在电子表格编辑器中有效
(function () {
  var oSheet = Api.GetActiveSheet(); // 在文档/演示文稿编辑器中为 undefined
  if (!oSheet) {
    console.log("Error: This macro only works in the spreadsheet editor.");
    return;
  }
  oSheet.GetRange("A1").SetValue("Hello");
})();
```

**表格中的索引越界错误：**

`GetCell(row, col)` 使用从零开始的索引。访问超出表格大小的行或列会抛出错误。

```javascript
(function () {
  var oTable = Api.CreateTable(3, 3); // 3 列，3 行（索引 0-2）

  // 错误——索引 3 不存在
  // var oCell = oTable.GetCell(3, 0);

  // 正确——在范围内迭代
  for (var row = 0; row < 3; row++) {
    for (var col = 0; col < 3; col++) {
      oTable
        .GetCell(row, col)
        .GetContent()
        .GetElement(0)
        .AddText(row + "," + col);
    }
  }

  Api.GetDocument().Push(oTable);
})();
```

**在迭代元素时修改文档：**

在 `for` 循环期间添加或删除元素会在迭代中改变 `GetElementsCount()`，导致元素被跳过或被处理两次。先收集目标，然后再修改。

```javascript
// 正确——先收集，然后修改
(function () {
  var oDocument = Api.GetDocument();
  var nCount = oDocument.GetElementsCount();
  var targets = [];

  for (var i = 0; i < nCount; i++) {
    var oElement = oDocument.GetElement(i);
    if (oElement.GetClassType() === "paragraph") {
      targets.push(oElement);
    }
  }

  for (var j = 0; j < targets.length; j++) {
    targets[j].SetBold(true);
  }
})();
```

## 测试策略

**先用最少量数据测试：**

在大型文档上运行宏之前，先创建一个只有少量元素的小型测试文档。这样可以更快地重现问题并检查结果。

**在顶部添加保护子句：**

在主逻辑运行前检查前提条件，防止代码深处出现难以理解的错误。

```javascript
(function () {
  var oDocument = Api.GetDocument();

  // 保护：文档必须至少有一个元素
  if (oDocument.GetElementsCount() === 0) {
    console.log("The document is empty. Add content and run again.");
    return;
  }

  // 保护：需要文本选择
  var oRange = oDocument.GetRangeBySelect();
  if (!oRange) {
    console.log("Select some text before running this macro.");
    return;
  }

  // 主逻辑
  oRange.SetText(oRange.GetText().toUpperCase());
})();
```

**使用试运行模式：**

添加 `DRY_RUN` 标志，在不应用更改的情况下预览宏将执行的操作。

```javascript
(function () {
  var DRY_RUN = true; // 设置为 false 以应用更改

  var oDocument = Api.GetDocument();
  var nCount = oDocument.GetElementsCount();

  for (var i = 0; i < nCount; i++) {
    var oElement = oDocument.GetElement(i);
    if (oElement.GetClassType() === "paragraph") {
      var text = oElement.GetText();
      if (DRY_RUN) {
        console.log('[DRY RUN] Would bold paragraph: "' + text + '"');
      } else {
        oElement.SetBold(true);
      }
    }
  }
})();
```

**测试边界情况：**

| 场景             | 检查内容                                                          |
| -------------------- | ---------------------------------------------------------------------- |
| 空文档       | 宏是否能优雅地处理零元素？                        |
| 无选择         | 当 `GetRangeBySelect()` 返回 `null` 时是否能干净退出？         |
| 混合内容        | 是否能跳过非段落元素（表格、图像）而不崩溃？ |
| 非常大的文档 | 是否能在合理时间内完成？                                 |
| 特殊字符   | 是否能正确处理 Unicode、emoji 或从右到左文本？                  |

**使用辅助函数隔离代码段：**

将宏分解为小型、可测试的函数。在组合之前单独测试每个函数。

```javascript
(function () {
  function getParagraphs(oDocument) {
    var result = [];
    var nCount = oDocument.GetElementsCount();
    for (var i = 0; i < nCount; i++) {
      var oElement = oDocument.GetElement(i);
      if (oElement.GetClassType() === "paragraph") {
        result.push(oElement);
      }
    }
    return result;
  }

  function boldAll(paragraphs) {
    for (var i = 0; i < paragraphs.length; i++) {
      paragraphs[i].SetBold(true);
    }
    console.log("Bolded " + paragraphs.length + " paragraph(s).");
  }

  var oDocument = Api.GetDocument();
  var paragraphs = getParagraphs(oDocument);
  boldAll(paragraphs);
})();
```

## 后续步骤

<table>
<tr>
<td width="50%" align="center">

### 相关指南

[编写宏 →](./writing-macros.md)

[高级功能 →](./advanced-features.md)

[最佳实践 →](./writing-macros.md#best-practices)

</td>
<td width="50%" align="center">

### 探索示例

[宏示例 →](../samples/macro-samples/macro-samples.md)

[API 参考 →](../../office-api/get-started/overview.md)

[入门指南 →](./getting-started.md)

</td>
</tr>
</table>

如有疑问或反馈，请访问[开发者论坛](https://forum.onlyoffice.com/)。
