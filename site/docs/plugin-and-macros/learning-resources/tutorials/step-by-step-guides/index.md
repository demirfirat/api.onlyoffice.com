---
sidebar_position: 1
---

# Step-by-step guides

Follow guided flows that combine samples, APIs, and best practices to build complete solutions.

## Build a YouTube inserter

Learn how to embed and control YouTube videos inside ONLYOFFICE editors using OLE objects.

**What you'll build:** A plugin that opens a custom panel, accepts a YouTube URL, and inserts the video as an object that stays embedded in the document.

**Resources:**

1. Start from the [YouTube plugin sample](../../../samples/plugin-samples/youtube.md) to explore the finished code.
2. Follow the [Plugin development tutorial](../../../get-started/get-started.md) to scaffold your own version from scratch.
3. Reference the [Plugin structure](../../../structure/configuration/configuration.md) documentation to understand how `config.json`, `index.html`, and `plugin.js` fit together.

**Key concepts:** OLE objects · `Asc.plugin.executeMethod` · external URL handling

## Build a form validator

Learn how to validate PDF form fields automatically using macros.

**What you'll build:** A macro that checks form fields against validation rules, highlights invalid entries, and reports issues to the user.

**Resources:**

1. [Change validation rules](../../../samples/macro-samples/pdf-editor/change-validation-rules.md) — set rules programmatically.
2. [Limit number of characters](../../../samples/macro-samples/pdf-editor/limit-number-of-characters.md) — enforce input length.
3. [Set checkbox tip text](../../../samples/macro-samples/pdf-editor/set-checkbox-tip-text.md) — add guidance to form fields.

Combine these patterns into a single macro that checks form data and highlights issues.

**Key concepts:** PDF editor API · form field methods · macro composition

## Build a custom translator

Learn how to call an external translation API from a plugin and display results in a custom panel.

**What you'll build:** A plugin that reads selected text, sends it to your chosen translation provider, and inserts the result back into the document.

**Resources:**

1. Explore the [Translator plugin sample](../../../samples/plugin-samples/translator.md) to see a complete API integration.
2. Adapt it with your own translation provider by reusing the same UI and command patterns.

**Key concepts:** `fetch` in plugins · selected text API · custom panel UI

## Build an AI writing assistant

Learn how to combine the AI plugin base with custom AI functions to create a domain-specific writing tool.

**What you'll build:** A plugin panel that offers context-aware writing suggestions, rewrites, and summaries powered by custom AI functions.

**Resources:**

1. [AI plugin guide](../../../ai/ai-plugin.md) — understand the base integration.
2. [Custom AI function samples](../../../samples/custom-ai-functions-samples/custom-ai-functions-samples.md) — add domain-specific prompts and tools.
3. [AI plugin sample](../../../samples/plugin-samples/ai.md) — see a working end-to-end implementation.

**Key concepts:** AI function registration · prompt engineering · streaming responses
