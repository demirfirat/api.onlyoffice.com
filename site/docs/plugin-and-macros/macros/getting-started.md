---
sidebar_position: -6
---

# Macros

ONLYOFFICE macros are JavaScript code snippets that automate document tasks using the [Office JavaScript API](../../office-api/get-started/overview.md). They run directly in the editor without requiring installation.

> **Note:** Macro functionality is available in ONLYOFFICE Docs version 7.0+. Starting from version 7.1, macros run in strict mode - variables must be declared before use, and access to `window`, `document` objects and `alert()` function is restricted.

---

## Getting Started

### What are macros?

**Key characteristics:**

- **Language:** JavaScript with Office JavaScript API
- **Execution:** Runs directly in the editor context
- **Security:** Sandboxed environment with no system access
- **Distribution:** Embedded in documents or shared as code
- **Development time:** 30 minutes - 2 hours

**Common use cases:**

- Automated text formatting and styling
- Batch find-replace operations
- Custom calculations in spreadsheets
- Table creation and formatting
- Document cleanup and standardization

---

### Your first macro (2-min tutorial)

This 2-minute tutorial shows how to create a macro that inserts formatted text.

#### Step 1: Open the macro editor

1. Open any document in ONLYOFFICE
2. Navigate to **Tools → Macros** (or **View → Macros**)
3. The macro editor panel appears on the right

#### Step 2: Write the macro

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

#### Step 3: Run the macro

Click the **Run** button (▶️) - the formatted text appears in your document.

#### Step 4: Save the macro

1. Click ![Dots icon](/assets/images/plugins/dots.svg#gh-light-mode-only)![Dots icon](/assets/images/plugins/dots.dark.svg#gh-dark-mode-only) next to "Macro 1"
2. Select **Rename** and enter a descriptive name
3. Click **OK**

---

### Macro editor walkthrough

![Macros window](/assets/images/plugins/macro-window.png#gh-light-mode-only)![Macros window](/assets/images/plugins/macro-window.dark.png#gh-dark-mode-only)

**Interface components:**

- **Macro list** (left) — All saved macros
- **Code editor** (center) — JavaScript editor with syntax highlighting
- **Controls** (top):
  - ![Play icon](/assets/images/plugins/play.svg) **Run** — Execute macro
  - ![Plus icon](/assets/images/plugins/plus.svg#gh-light-mode-only)![Plus icon](/assets/images/plugins/plus.dark.svg#gh-dark-mode-only) **New** — Create new macro
  - ![Dots icon](/assets/images/plugins/dots.svg#gh-light-mode-only)![Dots icon](/assets/images/plugins/dots.dark.svg#gh-dark-mode-only) **Options** — Rename, copy, or delete

**Managing macros:**

To add: Click ![Plus icon](/assets/images/plugins/plus.svg#gh-light-mode-only)![Plus icon](/assets/images/plugins/plus.dark.svg#gh-dark-mode-only)

To rename/copy/delete: Click ![Dots icon](/assets/images/plugins/dots.svg#gh-light-mode-only)![Dots icon](/assets/images/plugins/dots.dark.svg#gh-dark-mode-only) next to macro name

---

### When to use macros vs plugins

| Feature              | Macros                | Plugins             |
| -------------------- | --------------------- | ------------------- |
| **Installation**     | Not required          | Required            |
| **User Interface**   | No UI                 | Full custom UI      |
| **External APIs**    | Not supported         | Supported           |
| **Development Time** | 30 min - 2 hours      | 2-7 days            |
| **Skill Level**      | Beginner              | Intermediate        |
| **Distribution**     | Copy-paste, templates | Marketplace, GitHub |

**Use macros for:**

- Personal automation without UI
- Quick repetitive tasks
- Template-based operations
- No external service integration needed

**Use plugins for:**

- Custom UI (forms, buttons, panels)
- External API integration
- Organization-wide distribution
- Advanced features (image processing, real-time collaboration)

[Learn more about plugins →](../get-started/get-started.md)

---

## Writing Macros

### Basic syntax & structure

All macros use this template:

```javascript
(function () {
  "use strict";

  // Your code here
})();
```

**Essential API objects:**

```javascript
// Document
var oDocument = Api.GetDocument();

// Paragraph
var oParagraph = Api.CreateParagraph();
oParagraph.AddText("Sample text");

// Table
var oTable = Api.CreateTable(3, 4); // 3 columns, 4 rows

// Selection
var oRange = oDocument.GetRangeBySelect();
```

---

### Common operations

#### Text manipulation

**Insert text:**

```javascript
(function () {
  var oDocument = Api.GetDocument();
  var oParagraph = Api.CreateParagraph();
  oParagraph.AddText("New text");
  oDocument.Push(oParagraph);
})();
```

**Find and replace:**

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

**Transform selected text:**

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

---

#### Formatting

**Text formatting:**

```javascript
(function () {
  var oParagraph = Api.CreateParagraph();
  oParagraph.AddText("Formatted text");
  oParagraph.SetBold(true);
  oParagraph.SetItalic(true);
  oParagraph.SetFontSize(24); // Half-points (24 = 12pt)
  oParagraph.SetFontFamily("Arial");
  oParagraph.SetColor(255, 0, 0); // RGB
  Api.GetDocument().Push(oParagraph);
})();
```

**Paragraph alignment:**

```javascript
(function () {
  var oParagraph = Api.CreateParagraph();
  oParagraph.AddText("Centered text");
  oParagraph.SetJc("center"); // "left", "center", "right", "justify"
  Api.GetDocument().Push(oParagraph);
})();
```

**Highlighting:**

```javascript
(function () {
  var oDocument = Api.GetDocument();
  var oRange = oDocument.GetRangeBySelect();
  if (oRange) {
    oRange.SetHighlight("yellow");
  }
})();
```

---

#### Tables & data

**Create and populate table:**

```javascript
(function () {
  var oDocument = Api.GetDocument();
  var oTable = Api.CreateTable(3, 3);

  // Header row
  oTable.GetCell(0, 0).GetContent().GetElement(0).AddText("Name");
  oTable.GetCell(0, 1).GetContent().GetElement(0).AddText("Age");
  oTable.GetCell(0, 2).GetContent().GetElement(0).AddText("City");

  // Data row
  oTable.GetCell(1, 0).GetContent().GetElement(0).AddText("John");
  oTable.GetCell(1, 1).GetContent().GetElement(0).AddText("30");
  oTable.GetCell(1, 2).GetContent().GetElement(0).AddText("NYC");

  oDocument.Push(oTable);
})();
```

**Format table cells:**

```javascript
(function () {
  var oTable = Api.CreateTable(2, 2);
  var oCell = oTable.GetCell(0, 0);

  // Background color
  oCell.SetShd("clear", 200, 200, 200);

  // Cell content
  var oCellContent = oCell.GetContent().GetElement(0);
  oCellContent.AddText("Header");
  oCellContent.SetBold(true);

  Api.GetDocument().Push(oTable);
})();
```

**Table borders:**

```javascript
(function () {
  var oTable = Api.CreateTable(3, 3);

  // Parameters: type, size, space, R, G, B
  oTable.SetTableBorderTop("single", 8, 0, 0, 0, 0);
  oTable.SetTableBorderBottom("single", 8, 0, 0, 0, 0);
  oTable.SetTableBorderLeft("single", 8, 0, 0, 0, 0);
  oTable.SetTableBorderRight("single", 8, 0, 0, 0, 0);
  oTable.SetTableBorderInsideH("single", 8, 0, 0, 0, 0);
  oTable.SetTableBorderInsideV("single", 8, 0, 0, 0, 0);

  Api.GetDocument().Push(oTable);
})();
```

---

#### Document navigation

**Cursor movement:**

```javascript
(function () {
  var oDocument = Api.GetDocument();
  oDocument.MoveCursorToStart();
  // or
  oDocument.MoveCursorToEnd();
})();
```

**Select all content:**

```javascript
(function () {
  var oDocument = Api.GetDocument();
  oDocument.SelectAllContent();
  var oRange = oDocument.GetRangeBySelect();
})();
```

**Iterate paragraphs:**

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

---

### Best practices

#### Code organization

**Use descriptive names:**

```javascript
// Good
var headerParagraph = Api.CreateParagraph();
var customerTable = Api.CreateTable(3, 5);

// Avoid
var p = Api.CreateParagraph();
var t = Api.CreateTable(3, 5);
```

**Break into functions:**

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

#### Performance

**Minimize API calls in loops:**

```javascript
// Efficient
var oDocument = Api.GetDocument(); // Called once
for (var i = 0; i < 100; i++) {
  var oParagraph = Api.CreateParagraph();
  oParagraph.AddText("Line " + i);
  oDocument.Push(oParagraph);
}
```

#### Error handling

**Check for null values:**

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

---

### Code snippets library

**Find and replace all:**

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

**Word count:**

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

**Formatted table with alternating rows:**

```javascript
(function () {
  var oDocument = Api.GetDocument();
  var oTable = Api.CreateTable(3, 5);

  // Alternate row colors
  for (var row = 0; row < 5; row++) {
    for (var col = 0; col < 3; col++) {
      var oCell = oTable.GetCell(row, col);
      if (row % 2 === 0) {
        oCell.SetShd("clear", 240, 240, 240);
      }
    }
  }

  // Borders
  oTable.SetTableBorderTop("single", 8, 0, 0, 0, 0);
  oTable.SetTableBorderBottom("single", 8, 0, 0, 0, 0);
  oTable.SetTableBorderInsideH("single", 8, 0, 0, 0, 0);
  oTable.SetTableBorderInsideV("single", 8, 0, 0, 0, 0);

  oDocument.Push(oTable);
})();
```

**Apply formatting to all paragraphs:**

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

---

## Additional Resources

**Documentation:**

- [Complete API Reference](../../office-api/get-started/overview.md)
- [Writing Macros Guide](./writing-macros.md)
- [Custom Spreadsheet Functions](./adding-custom-functions.md)
- [Converting VBA Macros](./converting-vba-macros.md)

**Examples:**

- [Macro Samples Collection](../samples/macro-samples/macro-samples.md)
- [Plugin Samples](../samples/plugin-samples/plugin-samples.md)

**Community:**

- [Developer Forum](https://forum.onlyoffice.com/)
- [GitHub Repository](https://github.com/ONLYOFFICE/DocumentBuilder)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/onlyoffice)

---

## Next Steps

<table>
<tr>
<td width="50%" align="center">

### Learn More

[Writing Macros →](./writing-macros.md)

[API Reference →](../../office-api/get-started/overview.md)

[Custom Functions →](./adding-custom-functions.md)

</td>
<td width="50%" align="center">

### Explore Examples

[Macro Samples →](../samples/macro-samples/macro-samples.md)

[Plugin Samples →](../samples/plugin-samples/plugin-samples.md)

[Quick Start Guides →](../get-started/quick-start-guides.md)

</td>
</tr>
</table>

---

For questions or feedback, visit the [developer forum](https://forum.onlyoffice.com/).
