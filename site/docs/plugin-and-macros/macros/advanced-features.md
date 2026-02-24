---
sidebar_position: -4
---

# Advanced features

This guide covers advanced macro capabilities in ONLYOFFICE, including custom spreadsheet functions, event-triggered macros, user input and dialogs, and sharing macros across documents.

## Adding custom functions

Custom functions extend the spreadsheet formula set with JavaScript logic. Once defined, they work like built-in functions (e.g., `=DOUBLE(5)`).

> **Note:** Custom functions are available in the spreadsheet editor only.

**Register a custom function:**

```javascript
(function () {
  Api.addCustomFunction(
    "DOUBLE", // Function name (used in cells)
    function (x) {
      // Implementation
      return x * 2;
    },
    "Multiplies a value by 2", // Description
    [{ name: "value", description: "The number to double" }], // Parameters
  );
})();
```

**Use the function in a cell:**

Once registered, type `=DOUBLE(5)` in any cell. The result `10` appears immediately.

**Multiple parameters:**

```javascript
(function () {
  Api.addCustomFunction(
    "DISCOUNT",
    function (price, rate) {
      return price - (price * rate) / 100;
    },
    "Applies a percentage discount to a price",
    [
      { name: "price", description: "Original price" },
      { name: "rate", description: "Discount rate in percent" },
    ],
  );
})();
```

**Aggregate over a range:**

```javascript
(function () {
  Api.addCustomFunction(
    "COUNTWORDS",
    function (text) {
      if (!text) return 0;
      return String(text).trim().split(/\s+/).filter(Boolean).length;
    },
    "Counts the number of words in a text string",
    [{ name: "text", description: "The text to count words in" }],
  );
})();
```

> Custom function names are **case-insensitive** in cells but must be **unique** across all registered functions. Avoid names that conflict with built-in Excel/Calc functions.

---

## Event-triggered macros

Instead of running a macro manually, you can attach it to a document event using the [attachEvent](../../office-api/usage-api/text-document-api/Api/Methods/attachEvent.md) method. The macro executes automatically whenever the event fires.

**Syntax:**

```javascript
Api.attachEvent("eventName", callbackFunction);
```

**Common events:**

| Event                        | Trigger                                   |
| ---------------------------- | ----------------------------------------- |
| `asc_onHyperlinkClick`       | A hyperlink in the document is clicked    |
| `asc_onDocumentContentReady` | The document content has finished loading |
| `asc_onSelectionChanged`     | The user changes the current selection    |

**Log hyperlink clicks:**

```javascript
(function () {
  Api.attachEvent("asc_onHyperlinkClick", function () {
    console.log("A hyperlink was clicked.");
  });
})();
```

**React to selection changes:**

```javascript
(function () {
  Api.attachEvent("asc_onSelectionChanged", function () {
    var oDocument = Api.GetDocument();
    var oRange = oDocument.GetRangeBySelect();
    if (oRange) {
      console.log("Selected text: " + oRange.GetText());
    }
  });
})();
```

**Run code when the document is ready:**

```javascript
(function () {
  Api.attachEvent("asc_onDocumentContentReady", function () {
    var oDocument = Api.GetDocument();
    var oParagraph = Api.CreateParagraph();
    oParagraph.AddText(
      "Document loaded at: " + new Date().toLocaleTimeString(),
    );
    oDocument.Push(oParagraph);
  });
})();
```

> Event listeners persist only for the current editor session. They are not saved between document opens unless the macro is set to run on startup.

---

## User input and dialogs

ONLYOFFICE macros run in a sandboxed environment without access to native browser dialog functions (`alert`, `prompt`, `confirm`). Use the API wrappers described below to interact with users.

**Display a message:**

```javascript
(function () {
  var oDocument = Api.GetDocument();
  var oParagraph = Api.CreateParagraph();
  oParagraph.AddText("Operation completed successfully.");
  oDocument.Push(oParagraph);
})();
```

**Read input from a cell (spreadsheet):**

```javascript
(function () {
  var oWorksheet = Api.GetActiveSheet();
  var userValue = oWorksheet.GetRange("A1").GetValue();

  if (!userValue) {
    console.log("No value found in A1. Please enter input there first.");
    return;
  }

  var oRange = oWorksheet.GetRange("B1");
  oRange.SetValue("You entered: " + userValue);
})();
```

**Collect input via a dedicated input cell:**

```javascript
(function () {
  var oSheet = Api.GetActiveSheet();

  // Prompt cell
  oSheet.GetRange("A1").SetValue("Enter your name:");

  // Read the response from B1 after the user fills it in
  var name = oSheet.GetRange("B1").GetValue();
  if (name) {
    oSheet.GetRange("A2").SetValue("Hello, " + name + "!");
  }
})();
```

> For richer dialog needs (modal windows, forms, dropdowns), consider building a [plugin](../get-started/get-started.md) instead of a macro.

---

## Sharing macros across documents

Macros are stored inside the document by default. To reuse a macro in other documents, use one of the following approaches.

**Copy-paste the code:**

The simplest way to share a macro is to copy the JavaScript code and paste it into the macro editor of another document.

1. Open the macro editor (**Tools → Macros**).
2. Select the macro in the list on the left.
3. Copy all code from the editor.
4. Open the target document and paste the code into a new macro.

**Store macros as a snippet library:**

Keep frequently used macros in a shared `.js` file or a team wiki. When needed, paste the relevant snippet into the macro editor.

```javascript
// --- Snippet: formatHeaderParagraph ---
// Usage: call formatHeaderParagraph("Title Text") to insert a styled header.
(function () {
  function formatHeaderParagraph(text) {
    var oParagraph = Api.CreateParagraph();
    oParagraph.AddText(text);
    oParagraph.SetBold(true);
    oParagraph.SetFontSize(32);
    oParagraph.SetJc("center");
    return oParagraph;
  }

  var oDocument = Api.GetDocument();
  oDocument.Push(formatHeaderParagraph("Report Title"));
})();
```

**Use a document template:**

Macros saved in a document template (`.dotx`, `.ots`) are available to all documents created from that template.

1. Create a document template containing your macros.
2. Distribute the template file to your team.
3. New documents created from the template inherit all macros.

**Automate sharing with DocumentBuilder:**

For large-scale or server-side distribution, use [ONLYOFFICE DocumentBuilder](https://api.onlyoffice.com/docs/document-builder/get-started/overview/) to inject macros programmatically into documents.

---

## Next steps

<table>
<tr>
<td width="50%" align="center">

### Related guides

[Writing Macros →](./writing-macros.md)

[Debugging Macros →](./debugging.md)

[Custom Functions Reference →](./adding-custom-functions.md)

</td>
<td width="50%" align="center">

### Explore examples

[Macro Samples →](../samples/macro-samples/macro-samples.md)

[API Reference →](../../office-api/get-started/overview.md)

[Plugin Development →](../get-started/get-started.md)

</td>
</tr>
</table>

---

For questions or feedback, visit the [developer forum](https://forum.onlyoffice.com/).
