---
sidebar_position: 6
---

# 模板与样板代码

**目标：** 使用现成的结构快速开始，并将其适配为您自己的插件、宏和 AI 函数。

结合[示例](../samples-and-examples/index.md)和[教程](../tutorials/index.md)使用这些模板，快速从原型推进到生产就绪的解决方案，同时不破坏现有的插件、宏或 AI 函数 API。

## 插件模板

### 官方插件模板

使用 ONLYOFFICE 仓库中的官方插件模板作为起点：

- [ONLYOFFICE sdkjs-plugins 仓库](https://github.com/ONLYOFFICE/sdkjs-plugins)

每个模板提供：

- 预填所有必填字段的最小 `config.json`。
- 已包含插件 SDK 的即用 `index.html`。
- 预设 `init` 和 `button` 处理器存根的 `plugin.js` 脚手架。

### 如何使用插件模板

1. 克隆或下载仓库。
2. 复制与您的插件类型（面板、窗口、后台）匹配的模板文件夹。
3. 用您的插件名称、GUID 和描述更新 `config.json`。
4. 将 `index.html` 中的占位符 UI 替换为您自己的面板。
5. 使用 [Plugin API](https://onlyoffice.github.io/sdkjs-plugins/v1/plugins.js) 在 `plugin.js` 中实现您的逻辑。

详细演练请参见[快速入门指南](../../get-started/)中的**使用插件模板**部分。

## 宏代码片段

将宏示例集合中的代码片段作为构建自动化脚本的构建块重复使用。

### 推荐起点

| 任务                         | 要参考的示例                                                                           |
| ---------------------------- | ----------------------------------------------------------------------------------------- |
| 插入或替换文本       | [文档宏](../../samples/macro-samples/macro-samples.md#text-document-editor) |
| 处理电子表格范围 | [电子表格宏](../../samples/macro-samples/macro-samples.md#spreadsheet-editor)     |
| 自动化 PDF 表单字段     | [PDF 编辑器宏](../../samples/macro-samples/macro-samples.md#pdf-editor)              |
| 生成幻灯片内容       | [演示文稿宏](../../samples/macro-samples/macro-samples.md#presentation-editor)   |

### 代码片段结构

每个宏代码片段遵循以下最小模式：

```js
(function () {
  // 您的宏逻辑
  // 使用 Api.GetDocument()、Api.GetActiveSheet() 等
})();
```

将任何宏示例复制到此包装器中并修改主体。

## 自定义 AI 函数模式

将自定义 AI 函数示例作为常见 AI 任务的样板。

### 模式：内容生成

```js
// 注册一个根据提示生成文本的自定义 AI 函数
Api.RegisterAIFunction({
  name: "GenerateDraft",
  description: "Generate a draft paragraph based on a topic",
  parameters: { topic: "string" },
  handler: async function ({ topic }) {
    // 在此调用您的 AI 提供商
    return generatedText;
  },
});
```

- 完整示例：[文档编辑器 AI 函数](../../samples/custom-ai-tools-samples/custom-ai-tools-samples.md#text-document-editor)

### 模式：数据分析

```js
// 注册一个解释电子表格公式的自定义 AI 函数
Api.RegisterAIFunction({
  name: "ExplainFormula",
  description: "Explain what a spreadsheet formula does in plain language",
  parameters: { formula: "string" },
  handler: async function ({ formula }) {
    // 在此调用您的 AI 提供商
    return explanation;
  },
});
```

- 完整示例：[电子表格编辑器 AI 函数](../../samples/custom-ai-tools-samples/custom-ai-tools-samples.md#spreadsheet-editor)

### 模式：文档智能

```js
// 注册一个从文档部分提取关键点的自定义 AI 函数
Api.RegisterAIFunction({
  name: "ExtractKeyPoints",
  description: "Extract key points from the selected document section",
  parameters: { content: "string" },
  handler: async function ({ content }) {
    // 在此调用您的 AI 提供商
    return keyPoints;
  },
});
```

- 完整示例：[演示文稿编辑器 AI 函数](../../samples/custom-ai-tools-samples/custom-ai-tools-samples.md#presentation-editor)
