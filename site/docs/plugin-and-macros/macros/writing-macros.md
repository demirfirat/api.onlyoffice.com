---
sidebar_position: -5
---

# Writing macros

This guide covers the syntax, common operations, and best practices for writing ONLYOFFICE macros using the Office JavaScript API.

## Basic syntax & structure

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

## Common operations

### Text manipulation

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

### Formatting

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

### Tables & data

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

### Document navigation

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

## Best practices

### Code organization

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

### Performance

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

### Error handling

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

## Code snippets library

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

## Generating macros using AI plugin

If you want to speed up the process, starting from version 9.0, you can use the AI plugin built into ONLYOFFICE to automatically generate ONLYOFFICE macros from their descriptions:

1. Configure the AI plugin. The detailed instructions on how to do this can be found [here](/docs/plugin-and-macros/ai/ai-plugin.md#configuring).
2. Open the **View** tab and click **Macros**.
3. Click the **AI** icon in the **Macros** window and select **Create from description**.

    ![AI plugin](/assets/images/plugins/ai-plugin.png#gh-light-mode-only)![AI plugin](/assets/images/plugins/ai-plugin.dark.png#gh-dark-mode-only)

4. Input a prompt into the **Create macros from description** window and click **Create**. The generated macro sample will be inserted into the **Macros** window.

    ![Create from description](/assets/images/plugins/create-from-description.png#gh-light-mode-only)![Create from description](/assets/images/plugins/create-from-description.dark.png#gh-dark-mode-only)

5. Review the code and make adjustments if necessary.

    > While the AI plugin can generate a complete and working macro, it's not always perfect. Make sure to review the output carefully and test it thoroughly, especially for complex macros.

6. Click ![Play icon](/assets/images/plugins/play.svg) to test the script.

---

## Subscribing to events

To subscribe to the specified event and call the callback function when the event fires, use the [attachEvent](../../office-api/usage-api/text-document-api/Api/Methods/attachEvent.md) method.

For example, to subscribe to an event when a hyperlink in a document is clicked, use the following lines:

```javascript
Api.attachEvent("asc_onHyperlinkClick", () => {
  console.log("HYPERLINK!!!");
})
```

When you click any hyperlink in a document, the **asc\_onHyperlinkClick** event will be executed and the *"HYPERLINK!!!"* message will appear in the console.

![Click hyperlink](/assets/images/plugins/click-hyperlink.png)

---

## Assigning macros

In the spreadsheet editor, you can assign a macro to the graphic object:

1. Right-click the graphic object.
2. Click **Assign Macro**.
3. Choose a macro in the appeared window. You can type the macro name in the corresponding field.
4. Click the **OK** button.

![Assign macro](/assets/images/plugins/assign-macro.png)

To run the macro, just click the graphic object and the script will be executed.
