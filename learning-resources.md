---
sidebar_position: -10
---

# Learning resources

**Goal:** Learn by example and find answers quickly when building ONLYOFFICE plugins, macros, and custom AI functions.

Use this page as a hub to jump into ready‑made samples, guided tutorials, and practical use cases.


## Samples and examples

Explore working code you can copy, modify, and use as a reference.

### Plugin samples

Start from the full plugin samples collection:

- **All plugin samples:** [Browse plugin samples](./plugin-samples/plugin-samples.md)

#### By category

- **Integration plugins (YouTube, Translator, etc.)**
  - [YouTube](./plugin-samples/youtube.md) — embed and control YouTube videos as OLE objects.
  - [Translator](./plugin-samples/translator.md) — translate selected text via Google Translate.
  - [Telegram](./plugin-samples/telegram.md) — interact with Telegram chats from the editor.
  - [OCR](./plugin-samples/ocr.md) — recognize text in images and PDFs.
  - [LanguageTool](./plugin-samples/language-tool.md) — grammar and style checking.

- **Productivity plugins (Search, Autocomplete, etc.)**
  - [Search and replace](./plugin-samples/search-and-replace.md) — find and replace text in a document.
  - [Search and change text background color](./plugin-samples/search-and-change-text-background-color.md) — highlight matches dynamically.
  - [Count words and characters](./plugin-samples/count-words-and-characters.md) — quick text statistics.
  - [Autocomplete](./plugin-samples/autocomplete.md) — suggest text completions.
  - [Thesaurus](./plugin-samples/thesaurus.md) — find synonyms for selected words.

- **UI enhancement plugins**
  - [Symbol table](./plugin-samples/symbol-table.md) — insert special symbols via a custom UI.
  - [Extended comments](./plugin-samples/extended-comments.md) — richer comments experience.
  - [Photo editor](./plugin-samples/photo-editor.md) — edit images inside the editor.
  - [Clippy](./plugin-samples/clippy.md) and [Chess](./plugin-samples/chess.md) — playful UI extensions.
  - [Settings](./plugin-samples/settings.md) — manage plugin‑specific preferences.

- **Advanced plugins**
  - [AI](./plugin-samples/ai.md) — integrate AI features into the editor UI.
  - [Invoices](./plugin-samples/invoices.md) — generate and manage invoices.
  - [Templates](./plugin-samples/templates.md) — work with document templates.
  - [Speech](./plugin-samples/speech.md) — text‑to‑speech / speech‑related workflows.
  - [Get and paste HTML](./plugin-samples/get-and-paste-html.md) — insert structured HTML content.

#### By complexity

- **Beginner (5 examples)**
  - [Hello world](./plugin-samples/hello-world.md) — minimal plugin that inserts text.
  - [Move cursor](./plugin-samples/move-cursor.md) — basic interaction with the editor selection.
  - [Search and replace](./plugin-samples/search-and-replace.md) — simple text search integration.
  - [Count words and characters](./plugin-samples/count-words-and-characters.md) — document statistics.
  - [Add comment in cell](./plugin-samples/add-comment-in-cell.md) — spreadsheet cell comments.

- **Intermediate (8 examples)**
  - [YouTube](./plugin-samples/youtube.md) — embed external media.
  - [Translator](./plugin-samples/translator.md) — call external APIs and handle responses.
  - [Thesaurus](./plugin-samples/thesaurus.md) — contextual text lookup.
  - [Autocomplete](./plugin-samples/autocomplete.md) — dynamic suggestions in custom UI.
  - [Symbol table](./plugin-samples/symbol-table.md) — custom palette and insertion logic.
  - [Search and change text background color](./plugin-samples/search-and-change-text-background-color.md) — multi‑step content processing.
  - [Get and paste HTML](./plugin-samples/get-and-paste-html.md) — work with HTML snippets.
  - [Settings](./plugin-samples/settings.md) — persistent plugin configuration.

- **Advanced (5 examples)**
  - [AI](./plugin-samples/ai.md) — integrate AI services into plugin workflows.
  - [OCR](./plugin-samples/ocr.md) — process images and PDFs with external services.
  - [Invoices](./plugin-samples/invoices.md) — business document automation.
  - [Telegram](./plugin-samples/telegram.md) — connect messaging platforms.
  - [Templates](./plugin-samples/templates.md) — advanced work with document templates.

### Macro samples

Use macros to automate repetitive operations and document logic.

- **Macro samples collection:** [Browse macro samples](./macro-samples/macro-samples.md)

Focus by task type:

- **Text formatting**
  - See the **Text document editor** examples in the macro collection:  
    [Text document macros](./macro-samples/macro-samples.md#text-document-editor)
- **Table operations**
  - See the **Spreadsheet editor** examples for ranges, formulas, and layout:  
    [Spreadsheet macros](./macro-samples/macro-samples.md#spreadsheet-editor)
- **Data processing**
  - Use spreadsheet and document macros together for imports, calculations, and reports.
- **Automation scripts**
  - Combine several macros into workflows to generate content, update styles, and produce summaries.

### Custom AI function samples

Extend the AI agent with domain‑specific logic.

- **AI function samples collection:** [Browse custom AI function samples](./custom-ai-functions-samples/custom-ai-functions-samples.md)

Examples by goal:

- **Content generation**
  - Text document editor samples for drafting, rewriting, and summarizing content.
- **Data analysis**
  - Spreadsheet editor samples that analyze data, explain formulas, or generate insights.
- **Document intelligence**
  - Presentation and document samples that detect structure, extract key points, and enrich content.


## Tutorials (step‑by‑step guides)

Follow guided flows that combine samples, APIs, and best practices.

### Step‑by‑step guides

- **Build a YouTube inserter**
  - Start from the [YouTube plugin sample](./plugin-samples/youtube.md).
  - Use the [Plugin Development Tutorial](../get-started/quick-start-guides.md#plugin-development-tutorial) to scaffold and package your own version.

- **Build a form validator**
  - Use PDF editor macros to validate form fields, for example:
    - [Change validation rules](./macro-samples/pdf-editor/change-validation-rules.md)
    - [Limit number of characters](./macro-samples/pdf-editor/limit-number-of-characters.md)
    - [Set checkbox tip text](./macro-samples/pdf-editor/set-checkbox-tip-text.md)
  - Combine these patterns into a single macro that checks form data and highlights issues.

- **Build a custom translator**
  - Explore the [Translator plugin sample](./plugin-samples/translator.md) to see a complete API integration.
  - Adapt it with your own translation provider by reusing the same UI and command patterns.

- **Build an AI writing assistant**
  - Start with the [AI plugin guide](../ai/ai-plugin.md) to understand the base integration.
  - Extend it with [custom AI function samples](./custom-ai-functions-samples/custom-ai-functions-samples.md) for domain‑specific prompts and tools.

### Video tutorials (if available)

Short videos complement the written guides:

- **Plugin basics (≈5 min)** — from installation to first plugin run.
- **Advanced features (≈15 min)** — working with external APIs, OLE objects, and complex UIs.
- **Publishing workflow (≈8 min)** — packaging, testing, and publishing plugins to production.

> **Note:** When new official video tutorials are published, links from this section will point directly to the corresponding videos.

## Use case gallery

See how samples can be combined into complete solutions.

- **Business automation**
  - Use the [Invoices plugin](./plugin-samples/invoices.md) together with spreadsheet macros to generate, calculate, and send invoices automatically.
  - Combine templates and macros to create document packages (contracts, offers, reports) in one click.

- **Educational tools**
  - Mix [Translator](./plugin-samples/translator.md), [Thesaurus](./plugin-samples/thesaurus.md), and [Speech](./plugin-samples/speech.md) to create language‑learning scenarios.
  - Use text‑document macros to generate exercises, glossaries, and reading‑comprehension questions.

- **Content creation**
  - Combine the [Photo editor](./plugin-samples/photo-editor.md), [Symbol table](./plugin-samples/symbol-table.md), and [AI plugin](./plugin-samples/ai.md) for rich content‑creation environments.
  - Use custom AI functions for drafting, rewriting, and adding structure to long texts.

- **Data processing**
  - Rely on spreadsheet macros (sorting, filtering, aggregations) plus AI functions for explaining formulas and results.
  - Use plugins like [Get and paste HTML](./plugin-samples/get-and-paste-html.md) to pull data from other systems into documents.

- **Team collaboration**
  - Combine [Extended comments](./plugin-samples/extended-comments.md) with Telegram integration ([Telegram plugin](./plugin-samples/telegram.md)) to keep discussions close to documents.
  - Use macros and AI functions to generate meeting notes, summaries, and action lists from edited content.

## Templates & boilerplates

Start faster with ready‑made structures you can adapt.

- **Plugin templates**
  - Use the official plugin templates from the ONLYOFFICE repository:  
    [ONLYOFFICE plugin templates](https://github.com/ONLYOFFICE/sdkjs-plugins)
  - Combine them with the **Using Plugin Templates** section in the [Quick Start Guides](../get-started/quick-start-guides.md#using-plugin-templates) to customize configuration, UI, and styles.

- **Macro snippets**
  - Reuse code fragments from the [macro samples collection](./macro-samples/macro-samples.md) as building blocks for your own automation scripts.

- **Custom AI function patterns**
  - Take the [custom AI function samples](./custom-ai-functions-samples/custom-ai-functions-samples.md) as boilerplates for content generation, analysis, and document intelligence.

Use these templates together with the samples and tutorials above to move from prototype to production‑ready solutions quickly, without breaking existing plugin, macro, or AI function APIs.

