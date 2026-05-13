---
sidebar_position: 2
---

# Macro samples

**Goal:** Automate repetitive operations and add document logic without writing a full plugin.

Start from the full collection or filter by the type of task you want to accomplish.

- **Full collection:** [Browse all macro samples](/samples/?doctype=docs&text=macros)

## Text formatting

Samples that manipulate text appearance, styles, and structure in text documents.

See the **Text document editor** examples in the macro collection:
- [Text document macros](/samples/?doctype=docs&text=macros#text-document-editor)

Typical tasks:

- Apply paragraph and character styles programmatically.
- Insert, replace, or remove text ranges.
- Change fonts, colors, and alignment in bulk.

## Table operations

Samples that create, modify, and format tables inside spreadsheets and documents.

See the **Spreadsheet editor** examples for ranges, formulas, and layout:
- [Spreadsheet macros](/samples/?doctype=docs&text=macros#spreadsheet-editor)

Typical tasks:

- Read and write cell values and ranges.
- Apply conditional formatting.
- Sort, filter, and aggregate data.

## Data processing

Combine spreadsheet and document macros to import, calculate, and report data.

Typical tasks:

- Pull data from external sources into a spreadsheet.
- Run calculations and generate summary rows.
- Export results to a formatted document or PDF.

Relevant samples:

- [Import CSV/TXT data](/docs/macros/samples/spreadsheet-editor/import-csv-or-txt-data.md)
- [Recalculate worksheet values](/docs/macros/samples/spreadsheet-editor/recalculate-worksheet-values.md)
- [Convert formulas into values](/docs/macros/samples/spreadsheet-editor/convert-formulas-into-values.md)
- [Sheet merger](/docs/macros/samples/spreadsheet-editor/sheet-merger.md)
- [Split data sheets](/docs/macros/samples/spreadsheet-editor/split-data-sheets.md)

## Automation scripts

Macros that orchestrate multi-step operations, react to document state, or glue several editors together.

Typical tasks:

- Run periodic clean-up or formatting passes on large documents.
- Generate, update, and distribute reports from template files.
- Chain several macros together into one automated workflow.

Relevant collections:

- [Text document macros](/samples/?doctype=docs&text=macros#text-document-editor)
- [Spreadsheet macros](/samples/?doctype=docs&text=macros#spreadsheet-editor)
- [Presentation macros](/samples/?doctype=docs&text=macros#presentation-editor)
- [PDF macros](/samples/?doctype=docs&text=macros#pdf-editor)

### Example: Sales report pipeline

Import raw data, clean it, flag anomalies, and generate a chart — all in one run.

1. [Import CSV/TXT data](/docs/macros/samples/spreadsheet-editor/import-csv-or-txt-data.md) — load the source data into a sheet.
2. [Sort range](/docs/macros/samples/spreadsheet-editor/sort-range.md) — order rows by date or amount.
3. [Highlight duplicates](/docs/macros/samples/spreadsheet-editor/highlight-duplicates.md) — surface duplicate entries before analysis.
4. [Conditional formatting rules](/docs/macros/samples/spreadsheet-editor/conditional-formatting-rules.md) — color-code values above or below thresholds.
5. [Add chart](/docs/macros/samples/spreadsheet-editor/add-chart.md) — visualize the cleaned data.

### Example: Document cleanup and publish

Normalize a document received from an external source, then prepare it for distribution.

1. [Remove extra spaces](/docs/macros/samples/document-editor/remove-extra-spaces.md) — strip stray whitespace throughout the document.
2. [Replace words in document](/docs/macros/samples/document-editor/replace-words-in-document.md) — standardize terminology and correct known mistakes.
3. [Reset text properties](/docs/macros/samples/document-editor/reset-text-properties.md) — clear inline overrides and restore base styles.
4. [Custom header and footer generation](/docs/macros/samples/document-editor/custom-header-footer.md) — apply the correct branding and metadata.
5. [Generate table of contents](/docs/macros/samples/document-editor/generate-table-of-contents.md) — rebuild navigation after all edits are done.

### Example: Presentation from spreadsheet data

Turn a data sheet into a branded slide deck without manual copy-paste.

1. [Sheet merger](/docs/macros/samples/spreadsheet-editor/sheet-merger.md) — consolidate data from multiple sheets into one.
2. [Add chart](/docs/macros/samples/spreadsheet-editor/add-chart.md) — generate charts from the consolidated data.
3. [Create slides from list items](/docs/macros/samples/presentation-editor/create-slides-from-list-items.md) — turn key data points into individual slides.
4. [Table to chart](/docs/macros/samples/presentation-editor/table-to-chart.md) — convert any remaining data tables into visual charts.
5. [Apply corporate branding](/docs/macros/samples/presentation-editor/apply-corporate-branding.md) — apply fonts, colors, and logo across all slides.
