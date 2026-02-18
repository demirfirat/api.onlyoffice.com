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
