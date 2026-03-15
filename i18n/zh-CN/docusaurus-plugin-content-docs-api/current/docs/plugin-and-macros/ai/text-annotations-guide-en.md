---
sidebar_position: 1
---

# 文本注释 API 指南

ONLYOFFICE 9.2.0 引入了用于处理文本注释的 API 扩展——这些视觉下划线可高亮显示文本段落，并允许您为其关联元数据。

本指南介绍三个新 API 方法，并通过一个用于 AI 助手文本分析的实用插件示例演示其用法。

## 新 API 方法

1. *[`AnnotateParagraph`](https://api.onlyoffice.com/docs/plugin-and-macros/interacting-with-editors/text-document-api/Methods/AnnotateParagraph/)* — 向指定段落添加注释。
2. *[`SelectAnnotationRange`](https://api.onlyoffice.com/docs/plugin-and-macros/interacting-with-editors/text-document-api/Methods/SelectAnnotationRange/)* — 使用指定注释选中文档中的文本。
3. *[`RemoveAnnotationRange`](https://api.onlyoffice.com/docs/plugin-and-macros/interacting-with-editors/text-document-api/Methods/RemoveAnnotationRange/)* — 从文档中移除特定注释范围。

## 通过插件开发学习新方法

插件的详细使用说明请参见[此处](https://github.com/ONLYOFFICE-PLUGINS/onlyoffice.github.io/blob/feature/AI/sdkjs-plugins/content/ai/scripts/text-annotations/custom-annotations/README.md)。

该插件为文本添加注释，使用户能够创建 AI 助手，对内容进行分析，并高亮显示符合助手提示词中定义的特定条件的文本段落。共有三种选项：

1. *提示（Hint）* — 显示说明文字。
2. *替换（Replace）* — 建议替换文本。
3. *替换 + 提示（Replace + Hint）* — 建议替换文本并在下方显示说明（可包含链接）。

创建或编辑助手的界面包含三个字段：

1. *名称（Name）*
2. *类型（Type）*（提示、替换、替换 + 提示）
3. *提示词（Prompt）* — 用户定义条件的主要字段。应尽量具体，以获得准确结果。

还有一个隐藏字段，包含助手的唯一 ID。助手以字符串化对象的形式保存到 `localStorage`：

```js
const assistant = {
  id: string,
  name: string,
  type: number, // 0 - 提示, 1 - 替换, 2 - 替换 + 提示
  query: string, // 提示词 - 用户查询
}
```

## 创建助手

- 名称：*日期校正器（Date Corrector）*
- 类型：*替换 + 提示（Replace + Hint）*
- 查询：*识别文本中 1900 年至 2000 年间所有不正确的日期并建议更正。若日期正确则忽略。请提供附带来源链接的说明。*

### 运行助手

助手运行时，用户的查询将附加额外规则，以确保 AI 响应遵循所需的 JSON 格式。例如，为提供替换内容和说明，必须识别目标文本片段。

```js
let prompt = `You are a multi-disciplinary text analysis assistant.
    Your task is to find text fragments that match the user's criteria.`;
    // ...
prompt += `Response format - return ONLY this JSON array with no additional text:
    [{
        "origin": "exact text fragment that matches the query",
        "suggestion": "suggested replacement (plain text)",
        "reason": "detailed explanation why it matches the criteria",
        "difference":"visual representation showing exact changes between origin and suggestion"
        "occurrence": 1,
        "confidence": 0.95
    }]
    \n\n`;
prompt += "USER REQUEST:\n```" + assistant.query + "\n```\n\n"; // 用户查询
prompt += "TEXT TO ANALYZE:\n```\n" + paragraph_text + "\n```\n\n";
    // ....
```

您可以分析整篇文档（所有段落），也可以只分析选中的片段。本示例重点处理选中的段落。

可通过订阅 [`onParagraphText`](https://api.onlyoffice.com/docs/plugin-and-macros/interacting-with-editors/text-document-api/Events/onParagraphText/) 事件获取段落文本。

```js
window.Asc.plugin.attachEditorEvent("onParagraphText", (data) => {
    const {paragraphId, recalcId, text, annotations} = data;
    console.log("Paragraph updated:", paragraphId);
    annotations.forEach(a => {
        console.log(`Annotation ${a.id}: ${a.name} at ${a.start} (${a.length} chars)`);
    });
});
```

虽然上述示例可访问所有段落，但只需要当前选中段落的 ID。可使用 [`GetAllParagraphs`](https://api.onlyoffice.com/docs/office-api/usage-api/text-document-api/ApiRange/Methods/GetAllParagraphs/) 和 [`GetInternalId`](https://api.onlyoffice.com/docs/office-api/usage-api/text-document-api/ApiParagraph/Methods/GetInternalId/) 方法实现：

```js
const range = Api.GetDocument().GetRangeBySelect();
const paragraphs = range.GetAllParagraphs();
const ids = paragraphs.map(p => p.GetInternalId());
```

AI 响应以如下格式返回：

```js
let aiAnswer = {
    origin: "匹配查询的文本片段",
    suggestion: "建议的替换内容",
    reason: "该片段满足查询条件的详细说明",
    difference: "原始文本与建议替换内容的差异（以 HTML 格式显示，便于阅读）"
    // --//--
    occurrence: "该匹配在段落中出现的次数（1 次、2 次等）"
    confidence: "0 到 1 之间的值，表示正确选择的置信度百分比"
}
```

向 AI 发送请求后，将收到包含所有识别到的匹配及其详细属性的响应。

### 添加注释

要在文档中显示这些结果，请使用 [`AnnotateParagraph`](https://api.onlyoffice.com/docs/plugin-and-macros/interacting-with-editors/text-document-api/Methods/AnnotateParagraph/) 方法。

```js
window.Asc.plugin.executeMethod("AnnotateParagraph", [{
    type: "highlightText", // 目前只支持此值
    name: "customAssistant_" + assistantId, // 助手 ID
    paragraphId: "p1", // 从段落信息中获取的值
    recalcId: "r12", // 从段落信息中获取的值
    ranges: [ // 根据 aiAnswer.origin 和 aiAnswer.occurrence 计算
        { start: 5, length: 10, id: "a1" }
        // start 是匹配内容在段落中第一个字符的索引
    ]
}]);
```

### 处理注释

添加注释后，需要处理用户交互。点击注释应触发弹出窗口，显示原始文本、建议替换内容和简短说明，以及**接受**和**拒绝**按钮。这通过三个特定事件实现：

1. [`onBlurAnnotation`](https://api.onlyoffice.com/docs/plugin-and-macros/interacting-with-editors/text-document-api/Events/onBlurAnnotation/) — 当注释失去焦点时触发。
2. [`onClickAnnotation`](https://api.onlyoffice.com/docs/plugin-and-macros/interacting-with-editors/text-document-api/Events/onClickAnnotation/) — 当用户点击注释时触发。
3. [`onFocusAnnotation`](https://api.onlyoffice.com/docs/plugin-and-macros/interacting-with-editors/text-document-api/Events/onFocusAnnotation/) — 当注释获得焦点时触发。

三个事件均返回 `{name, paragraphId, ranges}`。本实现使用两个事件：`onClickAnnotation`（显示弹出窗口）和 `onBlurAnnotation`（隐藏弹出窗口）。

```js
let popup = new window.Asc.PluginWindow();

let variation = {
    url : 'annotationPopup.html',
    isVisual : true,
    buttons : [{ text:'Accept', primary: true }, { text:'Reject', primary: false }],
    isModal : false,
    description: 'Proposal for replacement',
    EditorsSupport : ["word"],
    size : [300, 200],
    fixedSize : true,
    isTargeted : true // 表示窗口应出现在注释附近
};

window.Asc.plugin.attachEditorEvent("onClickAnnotation", (annotatRanges) => {
    const {name, paragraphId, ranges} = annotatRanges;
    // --//--
    popup.show(variation);
});

window.Asc.plugin.attachEditorEvent("onBlurAnnotation", (annotatRanges) => {
    // --//--
    popup.close();
});
```

为完成整个工作流，用户必须能够接受 AI 建议或拒绝它。

### 替换注释文本

```js
/**
 * @param {Object} annotatRanges - 从 onClickAnnotation 事件接收的对象
 * @param {string} annotatRanges.name
 * @param {string} annotatRanges.paragraphId
 * @param {Array<string>} annotatRanges.ranges
 * @param {string} suggestion - AI 建议的替换文本
 */
async function onAccept(annotatRanges, suggestion) {

    const annotation = {
        paragraphId: annotatRanges.paragraphId,
        rangeId: annotatRanges.ranges[0], // 只替换第一个元素
        name: name
    };

    // 在文档中选中需要更改的注释文本
    await new Promise(resolve =>
        window.Asc.plugin.executeMethod(
            "SelectAnnotationRange",
            [annotation],
            resolve
        );
    );

    // 替换选中片段的文本并清除选择
    Asc.scope.suggestion = aiAnswer.suggestion;
    await new Promise(resolve => {
        Asc.plugin.callCommand(
            () => {
                Api.ReplaceTextSmart([Asc.scope.suggestion]);
                Api.GetDocument().RemoveSelection();
            }
            false, // 定义是否在执行后立即关闭窗口
            true, // 定义文档是否将被重新计算
            resolve
        );
    });

    // 移除已完成使命的注释
    await new Promise(resolve =>
        window.Asc.plugin.executeMethod(
            "RemoveAnnotationRange",
            [annotation],
            resolve
        );
    );

    // 将焦点返回编辑器
    await new Promise(resolve =>
        window.Asc.plugin.executeMethod(
            "FocusEditor",
            [],
            resolve
        );
    );
},
```

### 移除注释

```js
/**
 * @param {Object} annotatRanges - 从 onClickAnnotation 事件接收的对象
 * @param {string} annotatRanges.name
 * @param {string} annotatRanges.paragraphId
 * @param {Array<string>} annotatRanges.ranges
 */
async function onReject(annotatRanges)
{
    // 移除注释
    await new Promise(resolve =>
        window.Asc.plugin.executeMethod(
            "RemoveAnnotationRange",
            [{
                paragraphId: annotatRanges.paragraphId,
                rangeId: annotatRanges.ranges[0],
                name: name
            }],
            resolve
        );
    );
};
```

以上步骤概述了使用 ONLYOFFICE API 创建智能文本分析插件并实现自动搜索替换功能的过程。

## 使用场景

1. **抄袭检测**
   查询："查找独特性低于 90% 的片段。"
2. **AI 文本检测**
   查询："识别文本中哪些部分可能由 AI 生成。"
3. **法律分析**
   查询："验证文本是否符合特定法律法规。只标记不合规部分，忽略合规文本。"
4. **文体分析**
   识别官僚化术语并建议更清晰、更易读的替代表达。
