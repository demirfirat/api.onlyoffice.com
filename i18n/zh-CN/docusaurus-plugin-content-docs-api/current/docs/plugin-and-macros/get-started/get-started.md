# 入门指南

本指南提供 ONLYOFFICE 扩展功能的概述，包括插件、宏和 AI 集成。使用本指南了解可用选项，并为您的需求选择合适的方案。

> **注意：** 插件和宏功能适用于 ONLYOFFICE Docs 7.0 及更高版本。如需使用最新功能（包括宏录制和增强的 AI 集成），建议使用 9.2 或更高版本。

---

## 入门选项

### 快速开始

通过分步教程立即开始开发：

- [快速入门指南](quick-start-guides.md) — 完整演练
- [交互式演练场](playground/) — 无需安装即可测试代码

### 概述与概念

了解扩展功能和实现方式：

- [扩展类型](#扩展类型) — 可用的扩展方法
- [选择方案](#选择方案) — 决策框架
- [功能对比](#功能对比) — 详细对比表

## 扩展类型

ONLYOFFICE 支持三种扩展方法，每种方法针对特定使用场景设计：

### 插件

插件是嵌入 ONLYOFFICE 编辑器界面的 HTML/CSS/JavaScript 应用程序。它们提供自定义 UI 元素，并可与外部服务集成。

**使用场景：**
- 嵌入外部内容（[YouTube](../samples/plugin-samples/youtube.md)、媒体库）
- 第三方服务集成（[翻译器](../samples/plugin-samples/translator.md)、[Zotero](../samples/plugin-samples/zotero.md)、CRM 系统）
- 高级文档处理（[OCR](../samples/plugin-samples/ocr.md)、图像处理、数据可视化）
- 自定义工作流（表单构建器、审批系统、模板）

**开发要求：**
- **时间：** 2-7 天
- **技能水平：** 中级
- **技术：** HTML、CSS、JavaScript
- **分发：** [ONLYOFFICE 应用市场](https://www.onlyoffice.com/app-directory/en) 或私有部署

### 宏

宏是直接在文档中执行的 JavaScript 代码片段。无需安装，在文档上下文中运行。

**使用场景：**
- 自动化格式化（应用复杂样式）
- 批量操作（查找替换、批量更新）
- 自定义计算（专用公式、数据转换）
- 文档清理（元数据移除、格式标准化）

**开发要求：**
- **时间：** 30 分钟 - 2 小时
- **技能水平：** 初级
- **技术：** JavaScript
- **分发：** 复制粘贴或嵌入文档模板

### AI 集成

AI 启用的插件将标准插件功能与 AI 服务集成相结合，用于高级文档处理。

**使用场景：**
- 写作辅助（语法检查、文体建议、内容生成）
- 文档摘要（关键点提取、摘要生成）
- 上下文感知翻译（保留格式和语气）
- 数据分析（电子表格分析、报告生成）

**开发要求：**
- **时间：** 3-10 天
- **技能水平：** 高级
- **技术：** 插件 API、AI 提供商集成（OpenAI、Claude 或自定义服务）
- **额外要求：** AI 服务的 API 密钥

[在应用市场浏览 50+ 示例](https://www.onlyoffice.com/app-directory/en)

## 选择方案

使用以下决策树选择合适的扩展方法：

```
开始
    │
    ├─ 您是否需要自定义用户界面（按钮、表单、面板）？
    │   │
    │   ├─ 是 → 您是否需要连接外部服务（API、数据库）？
    │   │   │
    │   │   ├─ 是 → 使用插件
    │   │   │         （完整功能：UI + 外部集成）
    │   │   │
    │   │   └─ 否 → 使用插件
    │   │             （以 UI 为主：自定义面板和控件）
    │   │
    │   └─ 否 → 您是否需要连接外部服务？
    │       │
    │       ├─ 是 → 使用插件
    │       │         （无 UI 的后台集成）
    │       │
    │       └─ 否 → 使用宏
    │                 （无 UI 的文档自动化）
    │
    └─ 您是否需要 AI 驱动的功能？
        │
        └─ 使用 AI 启用插件
           （具备 AI 提供商集成的插件功能）
```

详细信息请参阅[功能对比](#功能对比)表。

## 功能对比

下表对比了每种扩展方法的功能：

| 功能 | 插件 | 宏 | AI 启用插件 |
|---------|---------|--------|-------------------|
| **实现** | 嵌入编辑器的 HTML/CSS/JS 应用 | 文档中的 JavaScript 代码 | 集成 AI 提供商的插件 |
| **安装** | 必需（应用市场或手动） | 不需要（嵌入文档） | 必需（与插件相同） |
| **用户界面** | 完整自定义 UI 支持 | 无 UI | 完整自定义 UI 支持 |
| **外部 API** | 支持（REST、GraphQL 等） | 不支持 | 支持（需要 AI 服务） |
| **离线功能** | 取决于实现 | 完全离线 | 需要网络连接 |
| **技能水平** | 中级 | 初级 | 高级 |
| **开发时间** | 2-7 天 | 30 分钟 - 2 小时 | 3-10 天 |
| **分发** | 应用市场、GitHub、私有部署 | 复制粘贴、模板 | 应用市场、私有部署 |
| **主要用例** | 可复用工具、集成 | 个人自动化 | AI 驱动功能 |
| **框架支持** | 支持 React、Vue、Angular | 仅 Vanilla JavaScript | 支持任何框架 |

## 示例

以下示例展示真实世界的实现：

### 插件示例

**[YouTube 嵌入](../samples/plugin-samples/youtube.md)**
- 在文档中嵌入 YouTube 视频
- 提供预览和播放控制
- 支持播放列表和时间戳

**[翻译器](../samples/plugin-samples/translator.md)**
- 将选定文本翻译成 100+ 种语言
- 保留格式和文档结构
- 与多个翻译 API 集成

**[OCR](../samples/plugin-samples/ocr.md)**
- 从图像中提取文字
- 支持多种语言
- 将提取的文字直接插入文档

**[Zotero](../samples/plugin-samples/zotero.md)**
- 连接 Zotero 引用库
- 插入引用和参考文献
- 支持多种引用格式（APA、MLA、Chicago）

[浏览 50+ 插件示例 →](../samples/plugin-samples/plugin-samples.md)

### 宏示例

**表格格式化**
- 为表格应用交替行颜色和边框

**基于模式的文本替换**
- 使用正则表达式执行复杂的查找替换操作

**自定义电子表格函数**
- 实现业务特定计算

**文档清理**
- 移除元数据并标准化格式

[浏览宏示例 →](../samples/macro-samples/macro-samples.md)

### AI 集成示例

**语法与文体检查器**
- 提供实时语法和文体建议
- 提供上下文感知的更正
- 支持多种写作风格（正式、随意、学术）

**文档摘要器**
- 生成执行摘要
- 提取行动项和关键点
- 允许自定义摘要长度

**数据分析助手**
- 处理关于电子表格数据的自然语言查询
- 生成洞察和可视化
- 创建自动化报告

[浏览 AI 函数示例 →](../samples/custom-ai-functions-samples/custom-ai-functions-samples.md)

## 常见问题

### 插件可以访问 ONLYOFFICE 之外的文件吗？

不可以。插件在沙盒浏览器环境中运行，具有以下功能：
- 访问当前文档内容
- 向外部 API 发送 HTTP 请求（如果已配置）
- 使用浏览器 localStorage 存储数据

插件不能访问本地文件系统或其他应用程序。

### 插件需要服务器吗？

**开发阶段：** 不需要服务器。插件可以完全使用本地文件在客户端开发。

**生产阶段：** 服务器需求取决于插件功能：
- **纯客户端插件**（无外部 API）：不需要服务器
- **API 集成：** 需要后端服务器或无服务器函数
- **AI 功能：** 需要 API 端点（自托管或第三方服务）

### 插件可以商业化吗？

可以。可用的商业化选项：
- 通过自有网站销售许可证
- 免费增值模式（基础免费，高级付费）
- 付费支持和定制服务
- 独立分发付费插件（ONLYOFFICE 应用市场目前托管免费插件）

### 支持前端框架吗？

支持。任何前端框架都可以用于插件开发。要求：
- 最终输出必须是标准的 HTML/CSS/JS
- Bundle 大小建议不超过 5MB
- 插件必须在 iframe 环境中正确加载

常用构建工具：Vite、Webpack

### 如何调试插件？

使用浏览器开发者工具调试：
1. 右键单击插件 UI
2. 选择"检查元素"
3. 使用控制台、网络和源代码选项卡

[详细调试指南 →](../tutorials/developing/for-web-editors.md)

### 主要限制是什么？

主要约束：
- 插件包大小限制：50MB（大型库使用 CDN）
- 某些浏览器 API 在 iframe 上下文中被阻止（使用插件 API 替代）
- 繁重操作可能影响编辑器性能（使用 Web Workers）
- 外部 API 适用 CORS 限制
- 外部 API 需要网络连接（实现离线回退）
- 功能可用性因编辑器类型而异（检查配置中的 `EditorsSupport`）

[查看所有常见问题 →](../more-information/faq/)

## 故障排除

### 插件问题

| 问题 | 解决方案 |
|---------|----------|
| **插件不可见** | • 验证 `config.json` 包含唯一 GUID<br/>• 检查文件路径是否正确<br/>• 重启 ONLYOFFICE |
| **JavaScript 错误** | • 打开浏览器开发者工具（F12）<br/>• 检查控制台错误消息<br/>• 验证 `plugins.js` 已加载 |
| **API 方法失败** | • 确认 ONLYOFFICE 版本支持该方法<br/>• 在 [API 参考](../interacting-with-editors/overview/overview.md)中验证方法语法<br/>• 检查编辑器类型兼容性 |

### 宏问题

| 问题 | 解决方案 |
|---------|----------|
| **宏不执行** | • 验证宏在工具 → 设置中已启用<br/>• 检查宏编辑器中的语法错误<br/>• 确认 API 方法名称正确 |
| **意外结果** | • 添加 `console.log()` 语句进行调试<br/>• 单独测试每个操作<br/>• 执行前验证文档状态 |

**其他支持：**
- [开发者论坛](https://forum.onlyoffice.com/) — 社区支持
- [GitHub Issues](https://github.com/ONLYOFFICE/sdkjs-plugins) — 问题报告
- [Stack Overflow](https://stackoverflow.com/questions/tagged/onlyoffice) — Q&A 档案

## 资源

### 开发工具

- [交互式演练场](playground/) — 无需安装即可测试代码
- [UI 组件库](https://onlyoffice.github.io/storybook/static/) — 预构建组件
- [插件模板](https://github.com/ONLYOFFICE/sdkjs-plugins) — 入门项目
- 浏览器开发者工具 — 调试工具

### 示例代码

- [插件示例](../samples/plugin-samples/plugin-samples.md) — 50+ 可用示例
- [宏示例](../samples/macro-samples/macro-samples.md) — 常见自动化脚本
- [AI 函数示例](../samples/custom-ai-functions-samples/custom-ai-functions-samples.md) — AI 集成模式

### 文档

- [API 参考](../interacting-with-editors/overview/overview.md) — 完整 API 文档
- [插件结构](../structure/configuration/configuration.md) — 配置指南
- [宏指南](../macros/writing-macros.md) — 宏开发
- [AI 集成](../ai/ai-plugin.md) — AI 插件设置

### 社区

- [论坛](https://forum.onlyoffice.com/) — 提问和分享项目
- [GitHub](https://github.com/ONLYOFFICE/sdkjs-plugins) — 源代码、问题、贡献
- [Stack Overflow](https://stackoverflow.com/questions/tagged/onlyoffice) — 技术问答
- [更新日志](../more-information/changelog/) — API 更新

## 后续步骤

### 开始开发

- [快速入门指南](quick-start-guides.md)
- [插件教程](../structure/getting-started.md)
- [宏教程](../macros/getting-started.md)
- [AI 集成](../ai/ai-plugin.md)

### 探索

- [打开演练场](playground/)
- [浏览示例](https://github.com/ONLYOFFICE/sdkjs-plugins)
- [观看教程](https://www.youtube.com/onlyoffice)

### 深入学习

- [API 参考](../interacting-with-editors/overview/overview.md)
- [最佳实践](../tutorials/developing/for-web-editors/)
- [发布指南](../tutorials/publishing.md)

如有疑问或反馈，请访问[开发者论坛](https://forum.onlyoffice.com/)。
