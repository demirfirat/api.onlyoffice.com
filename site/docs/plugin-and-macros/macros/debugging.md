---
sidebar_position: -3
---

# Debugging macros

This guide explains how to find and fix problems in ONLYOFFICE macros, covering console logging, error messages, common mistakes, and testing strategies.

## Console logging

`console.log()` is the primary tool for inspecting values and tracing macro execution. Output appears in the **Console** tab of the macro editor.

**Log a simple value:**

```javascript
(function () {
  var oDocument = Api.GetDocument();
  var nCount = oDocument.GetElementsCount();
  console.log("Element count: " + nCount);
})();
```

**Log inside a loop:**

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

**Log object properties:**

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

**Add checkpoints to trace execution flow:**

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

> Use numbered or labeled checkpoints (e.g., `[1]`, `[INIT]`) to quickly identify where execution stops when a macro fails silently.

---

## Error messages explained

When a macro fails, an error message appears in the console or as a notification in the editor. The table below lists the most common messages and their causes.

| Error message                                  | Cause                                                                                                                          |
| ---------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| `Cannot read property '...' of null`           | Calling a method on an object that is `null`, for example when `GetRangeBySelect()` returns `null` because nothing is selected |
| `... is not a function`                        | Calling a method that does not exist on an object, or calling `undefined` as a function                                        |
| `... is not defined`                           | Using a variable before declaring it (required in strict mode)                                                                 |
| `RangeError: Maximum call stack size exceeded` | Infinite recursion — a function calls itself without a base case                                                               |
| `SyntaxError: Unexpected token`                | A typo in the code — missing bracket, comma, or quote                                                                          |
| `Access denied`                                | Attempting to access a restricted object such as `window` or `document`                                                        |

**Null check before method call:**

```javascript
(function () {
  var oDocument = Api.GetDocument();
  var oRange = oDocument.GetRangeBySelect();

  // Always check for null before using the result
  if (!oRange) {
    console.log(
      "Error: No text is selected. Please select text and run again.",
    );
    return;
  }

  console.log("Selected: " + oRange.GetText());
})();
```

**Wrap risky code in try/catch:**

```javascript
(function () {
  try {
    var oDocument = Api.GetDocument();
    var oTable = oDocument.GetElement(0);
    var oCell = oTable.GetCell(0, 0); // Fails if element is not a table
    console.log(oCell.GetContent().GetText());
  } catch (e) {
    console.log("Error: " + e.message);
  }
})();
```

---

## Common mistakes

**Forgetting to declare variables:**

All variables must be declared with `var`, `let`, or `const`. Undeclared variables throw a `ReferenceError` in strict mode.

```javascript
// Incorrect — throws ReferenceError in strict mode
(function () {
  "use strict";
  oDocument = Api.GetDocument(); // Missing var
})();

// Correct
(function () {
  "use strict";
  var oDocument = Api.GetDocument();
})();
```

**Using `GetRangeBySelect()` without a selection:**

`GetRangeBySelect()` returns `null` when no text is selected. Always guard against this.

```javascript
// Incorrect — throws if nothing is selected
(function () {
  var oRange = Api.GetDocument().GetRangeBySelect();
  oRange.SetText("Hello"); // Crash if oRange is null
})();

// Correct
(function () {
  var oRange = Api.GetDocument().GetRangeBySelect();
  if (oRange) {
    oRange.SetText("Hello");
  }
})();
```

**Calling APIs unavailable in the current editor:**

Some API methods are specific to one editor type (document, spreadsheet, or presentation). Calling a spreadsheet-only method in the document editor returns `undefined` or throws an error.

```javascript
// This only works in the spreadsheet editor
(function () {
  var oSheet = Api.GetActiveSheet(); // undefined in document/presentation editor
  if (!oSheet) {
    console.log("Error: This macro only works in the spreadsheet editor.");
    return;
  }
  oSheet.GetRange("A1").SetValue("Hello");
})();
```

**Off-by-one index errors in tables:**

`GetCell(row, col)` uses zero-based indices. Accessing a row or column beyond the table size throws an error.

```javascript
(function () {
  var oTable = Api.CreateTable(3, 3); // 3 columns, 3 rows (indices 0-2)

  // Incorrect — index 3 does not exist
  // var oCell = oTable.GetCell(3, 0);

  // Correct — iterate within bounds
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

**Modifying the document while iterating over elements:**

Adding or removing elements during a `for` loop changes `GetElementsCount()` mid-iteration, causing elements to be skipped or processed twice. Collect targets first, then modify.

```javascript
// Correct — collect first, then modify
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

---

## Testing strategies

**Test with minimal data first:**

Before running a macro on a large document, create a small test document with only a few elements. This makes it faster to reproduce issues and inspect results.

**Add guard clauses at the top:**

Check preconditions before the main logic runs. This prevents cryptic errors deep in the code.

```javascript
(function () {
  var oDocument = Api.GetDocument();

  // Guard: document must have at least one element
  if (oDocument.GetElementsCount() === 0) {
    console.log("The document is empty. Add content and run again.");
    return;
  }

  // Guard: a text selection is required
  var oRange = oDocument.GetRangeBySelect();
  if (!oRange) {
    console.log("Select some text before running this macro.");
    return;
  }

  // Main logic
  oRange.SetText(oRange.GetText().toUpperCase());
})();
```

**Use a dry-run mode:**

Add a `DRY_RUN` flag to preview what the macro would do without applying changes.

```javascript
(function () {
  var DRY_RUN = true; // Set to false to apply changes

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

**Test edge cases:**

| Scenario             | What to check                                                          |
| -------------------- | ---------------------------------------------------------------------- |
| Empty document       | Does the macro handle zero elements gracefully?                        |
| No selection         | Does it exit cleanly when `GetRangeBySelect()` returns `null`?         |
| Mixed content        | Does it skip non-paragraph elements (tables, images) without crashing? |
| Very large documents | Does it complete in a reasonable time?                                 |
| Special characters   | Does it handle Unicode, emoji, or RTL text correctly?                  |

**Isolate sections with helper functions:**

Break your macro into small, testable functions. Test each function independently before combining them.

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

---

## Next steps

<table>
<tr>
<td width="50%" align="center">

### Related guides

[Writing Macros →](./writing-macros.md)

[Advanced Features →](./advanced-features.md)

[Best practices →](./writing-macros.md#best-practices)

</td>
<td width="50%" align="center">

### Explore examples

[Macro Samples →](../samples/macro-samples/macro-samples.md)

[API Reference →](../../office-api/get-started/overview.md)

[Getting Started →](./getting-started.md)

</td>
</tr>
</table>

---

For questions or feedback, visit the [developer forum](https://forum.onlyoffice.com/).
