---
sidebar_position: -6
---

# 宏

ONLYOFFICE 宏是使用 [Office JavaScript API](../../office-api/get-started/overview.md) 自动化文档任务的 JavaScript 代码片段。无需安装，直接在编辑器中运行。

> **注意：** 宏功能适用于 ONLYOFFICE Docs 7.0 及更高版本。从 7.1 版本开始，宏在严格模式下运行——变量必须在使用前声明，对 `window`、`document` 对象和 `alert()` 函数的访问受到限制。

## 入门

### 什么是宏？

**主要特点：**

- **语言：** 使用 Office JavaScript API 的 JavaScript
- **执行：** 直接在编辑器上下文中运行
- **安全性：** 沙盒环境，无法访问系统
- **分发：** 嵌入文档或作为代码共享
- **开发时间：** 30 分钟 - 2 小时

**常见使用场景：**

- 自动化文本格式化和样式设置
- 批量查找替换操作
- 电子表格中的自定义计算
- 表格创建和格式化
- 文档清理和标准化

### 您的第一个宏（2 分钟教程）

此 2 分钟教程展示如何创建插入格式化文本的宏。

#### 步骤 1：打开宏编辑器

1. 在 ONLYOFFICE 中打开任意文档
2. 导航至 **工具 → 宏**（或 **视图 → 宏**）
3. 宏编辑器面板将出现在右侧

#### 步骤 2：编写宏

```javascript
(function () {
  var oDocument = Api.GetDocument();
  var oParagraph = Api.CreateParagraph();
  oParagraph.AddText("Hello from my first macro!");
  oParagraph.SetBold(true);
  oParagraph.SetColor(0, 102, 204);
  oDocument.Push(oParagraph);
})();
```

#### 步骤 3：运行宏

点击**运行**按钮（▶️）——格式化文本将出现在文档中。

#### 步骤 4：保存宏

1. 点击 "Macro 1" 旁边的 ![Dots icon](/assets/images/plugins/dots.svg#gh-light-mode-only)![Dots icon](/assets/images/plugins/dots.dark.svg#gh-dark-mode-only)
2. 选择**重命名**并输入描述性名称
3. 点击**确定**

### 宏编辑器介绍

![Macros window](/assets/images/plugins/macro-window.png#gh-light-mode-only)![Macros window](/assets/images/plugins/macro-window.dark.png#gh-dark-mode-only)

**界面组成：**

- **宏列表**（左侧）— 所有已保存的宏
- **代码编辑器**（中间）— 带语法高亮的 JavaScript 编辑器
- **控件**（顶部）：
  - ![Play icon](/assets/images/plugins/play.svg) **运行** — 执行宏
  - ![Plus icon](/assets/images/plugins/plus.svg#gh-light-mode-only)![Plus icon](/assets/images/plugins/plus.dark.svg#gh-dark-mode-only) **新建** — 创建新宏
  - ![Dots icon](/assets/images/plugins/dots.svg#gh-light-mode-only)![Dots icon](/assets/images/plugins/dots.dark.svg#gh-dark-mode-only) **选项** — 重命名、复制或删除

**管理宏：**

添加：点击 ![Plus icon](/assets/images/plugins/plus.svg#gh-light-mode-only)![Plus icon](/assets/images/plugins/plus.dark.svg#gh-dark-mode-only)

重命名/复制/删除：点击宏名称旁边的 ![Dots icon](/assets/images/plugins/dots.svg#gh-light-mode-only)![Dots icon](/assets/images/plugins/dots.dark.svg#gh-dark-mode-only)

### 宏与插件的选择

| 功能              | 宏                | 插件             |
| -------------------- | --------------------- | ------------------- |
| **安装**     | 不需要          | 必需            |
| **用户界面**   | 无 UI                 | 完整自定义 UI      |
| **外部 API**    | 不支持         | 支持           |
| **开发时间** | 30 分钟 - 2 小时      | 2-7 天            |
| **技能水平**      | 初级              | 中级        |
| **分发**     | 复制粘贴、模板 | 应用市场、GitHub |

**使用宏的场景：**

- 无需 UI 的个人自动化
- 快速重复性任务
- 基于模板的操作
- 无需外部服务集成

**使用插件的场景：**

- 自定义 UI（表单、按钮、面板）
- 外部 API 集成
- 组织范围的分发
- 高级功能（图像处理、实时协作）

[了解更多关于插件的信息 →](../get-started/get-started.md)

## 其他资源

**文档：**

- [完整 API 参考](../../office-api/get-started/overview.md)
- [编写宏指南](./writing-macros.md)
- [自定义电子表格函数](./adding-custom-functions.md)
- [转换 VBA 宏](./converting-vba-macros.md)

**示例：**

- [宏示例集合](../samples/macro-samples/macro-samples.md)
- [插件示例](../samples/plugin-samples/plugin-samples.md)

**社区：**

- [开发者论坛](https://forum.onlyoffice.com/)
- [GitHub 仓库](https://github.com/ONLYOFFICE/DocumentBuilder)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/onlyoffice)

## 后续步骤

<table>
<tr>
<td width="50%" align="center">

### 深入学习

[编写宏 →](./writing-macros.md)

[API 参考 →](../../office-api/get-started/overview.md)

[自定义函数 →](./adding-custom-functions.md)

</td>
<td width="50%" align="center">

### 探索示例

[宏示例 →](../samples/macro-samples/macro-samples.md)

[插件示例 →](../samples/plugin-samples/plugin-samples.md)

[快速入门指南 →](../get-started/quick-start-guides.md)

</td>
</tr>
</table>

如有疑问或反馈，请访问[开发者论坛](https://forum.onlyoffice.com/)。
