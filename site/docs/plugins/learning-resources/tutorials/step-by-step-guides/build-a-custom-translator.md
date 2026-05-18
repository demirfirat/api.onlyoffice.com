---
sidebar_position: 2
---

# Build a custom translator

Learn how to read selected text from a document, send it to an external translation API, and insert the result back — all from inside an ONLYOFFICE plugin panel.

**What you'll build:** A plugin that lives in a side panel, watches for text selection changes, sends the selected text to a translation API (Google Translate is used as the example), displays the translated result, and lets the user paste it back into the document with one click.

## Prerequisites

- A working ONLYOFFICE plugin development environment — see [Plugin development tutorial](/docs/plugins/structure/getting-started.md).
- Basic familiarity with `config.json`, `index.html`, and the plugin JS file — see [Plugin structure](/docs/plugins/structure/configuration/configuration.md).
- A translation API key if you are targeting production. For development, you can start without one by using a public endpoint.

## Step 1 — Scaffold the plugin

Create the plugin folder:

```
translator/
├── config.json
├── index.html
└── translate.js
```

The translator is a **side-panel plugin** (`isInsideMode: true`, `isModal: false`) that re-fires whenever the user changes their text selection (`initOnSelectionChanged: true`). Set `initDataType` to `"text"` so the editor automatically passes the raw selected text into `window.Asc.plugin.info.data` on every selection change.

```json
{
  "name": "Translator",
  "guid": "asc.{7327FC95-16DA-41D9-9AF2-0E7F449F6800}",
  "version": "1.0.6",
  "variations": [
    {
      "description": "Translate the selected text into other languages with Google Translate.",
      "url": "index.html",
      "icons": ["resources/img/icon.png", "resources/img/icon@2x.png"],
      "isViewer": true,
      "EditorsSupport": ["word", "cell", "slide", "pdf"],
      "isVisual": true,
      "isModal": false,
      "isInsideMode": true,
      "initDataType": "text",
      "initOnSelectionChanged": true
    }
  ]
}
```

Key settings to notice:

| Setting | Value | Why |
|---|---|---|
| `initDataType` | `"text"` | Editor pre-populates `info.data` with the current selection as plain text |
| `initOnSelectionChanged` | `true` | `init` is called again every time the user changes their selection |
| `isInsideMode` | `true` | Plugin renders in the side panel rather than a floating window |
| `isModal` | `false` | Non-blocking — the user can keep editing while the panel is open |

## Step 2 — Build the panel UI

`index.html` needs a language selector, a read-only output area for the translation, and a button to paste the result back into the document:

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <title>Translator</title>
    <style>
      body {
        font-family: Arial, sans-serif;
        margin: 10px;
        display: flex;
        flex-direction: column;
        gap: 8px;
      }
      select, textarea, button {
        width: 100%;
        box-sizing: border-box;
        font-size: 13px;
      }
      textarea {
        height: 120px;
        resize: vertical;
        padding: 6px;
        border: 1px solid #ccc;
        border-radius: 3px;
      }
      button {
        padding: 6px;
        background: #4CAF50;
        color: white;
        border: none;
        border-radius: 3px;
        cursor: pointer;
      }
      #status {
        font-size: 11px;
        color: #888;
      }
    </style>
  </head>
  <body>
    <label for="lang-select">Translate to:</label>
    <select id="lang-select">
      <option value="en">English</option>
      <option value="de">German</option>
      <option value="fr">French</option>
      <option value="es">Spanish</option>
      <option value="pt">Portuguese</option>
      <option value="zh">Chinese (Simplified)</option>
      <option value="ja">Japanese</option>
      <option value="ru">Russian</option>
    </select>

    <textarea id="result" readonly placeholder="Translation will appear here…"></textarea>

    <button id="paste-btn" onclick="pasteResult()">Paste into document</button>
    <span id="status"></span>

    <script src="translate.js"></script>
  </body>
</html>
```

## Step 3 — Detect selection changes and fetch the translation

In `translate.js`, implement `window.Asc.plugin.init`. Because `initOnSelectionChanged` is `true` in `config.json`, this function is called every time the selection changes. The currently selected text is available in `window.Asc.plugin.info.data`:

```js
var lastText = "";

window.Asc.plugin.init = function () {
  // With initDataType:"text", info.data holds the current selection as plain text.
  // If the selection is empty or unchanged, skip the API call.
  var text = (window.Asc.plugin.info.data || "").trim();
  if (!text || text === lastText) return;
  lastText = text;

  translate(text, document.getElementById("lang-select").value);
};

// Re-translate when the user picks a different target language
document.getElementById("lang-select").addEventListener("change", function () {
  if (lastText) translate(lastText, this.value);
});
```

## Step 4 — Call the translation API

The translator plugin in the official sample uses Google Translate. Implement a `translate()` helper that calls the API and writes the result to the panel:

```js
var API_KEY = "YOUR_GOOGLE_TRANSLATE_API_KEY"; // replace with your key

function translate(text, targetLang) {
  setStatus("Translating…");
  document.getElementById("result").value = "";

  var url =
    "https://translation.googleapis.com/language/translate/v2" +
    "?key=" + encodeURIComponent(API_KEY) +
    "&q="   + encodeURIComponent(text) +
    "&target=" + encodeURIComponent(targetLang) +
    "&format=text";

  fetch(url)
    .then(function (response) {
      if (!response.ok) throw new Error("HTTP " + response.status);
      return response.json();
    })
    .then(function (json) {
      var translated = json.data.translations[0].translatedText;
      document.getElementById("result").value = translated;
      setStatus("");
    })
    .catch(function (err) {
      setStatus("Error: " + err.message);
    });
}

function setStatus(msg) {
  document.getElementById("status").textContent = msg;
}
```

> **Swapping providers:** Replace the `fetch` call and response parsing to use a different API (DeepL, Azure Translator, LibreTranslate, etc.). The rest of the plugin code stays the same.

## Step 5 — Read the selected text explicitly (optional alternative)

If you need more control over what is selected — for example, to handle table cells differently — you can call `GetSelectedText` directly instead of relying on `initDataType: "text"`. This is useful when you need to preserve paragraph separators or handle spreadsheet selections:

```js
window.Asc.plugin.executeMethod(
  "GetSelectedText",
  [{ "Numbering": false, "Math": false, "ParaSeparator": "\n" }],
  function (selectedText) {
    if (selectedText) {
      translate(selectedText, document.getElementById("lang-select").value);
    }
  }
);
```

The `GetSelectedText` method returns the selected text as a string with configurable separators for paragraphs, table rows, and cells.

## Step 6 — Paste the translation back into the document

When the user clicks **Paste into document**, call `PasteText`. The method inserts the given string at the current cursor position (or replaces the selection if text is selected):

```js
function pasteResult() {
  var translated = document.getElementById("result").value;
  if (!translated) return;

  window.Asc.plugin.executeMethod("PasteText", [translated], function () {
    // Optionally close the panel after pasting
    // window.Asc.plugin.close();
  });
}
```

`PasteText` inserts plain text. If your translation provider returns HTML (some do when `format=html` is requested), use `PasteHtml` instead.

## Step 7 — Test the plugin

1. Zip the `translator/` folder and install it via **Plugins → Plugin Manager → Upload plugin**.
2. Open any document and type a few sentences.
3. Select some text and open the **Translator** plugin from the **Plugins** tab.
4. The translation appears automatically in the panel within a second or two.
5. Change the target language from the dropdown — the panel retranslates immediately.
6. Click **Paste into document** — the translation is inserted at the cursor position.

## Going further

- Add a **source language** dropdown and pass `source` to the API to prevent auto-detection errors.
- Cache recent translations in a `Map` keyed by `text + targetLang` to avoid redundant API calls when the user deselects and reselects the same word.
- Support bulk translation of entire paragraphs by calling `GetSelectedText` with `ParaSeparator: "\n\n"` and splitting on double newlines.
- Add a character count and warn the user when the selection exceeds the API's limit (5 000 characters for Google Translate's free tier).

**Resources:**

1. [Translator plugin sample](/docs/plugins/samples/translator.md) — reference implementation with full Google Translate integration.
2. [Plugin structure](/docs/plugins/structure/configuration/configuration.md) — full `config.json` field reference.
3. [executeMethod ("GetSelectedText")](/docs/plugins/interacting-with-editors/document-api/Methods/GetSelectedText.md) — retrieve selection with configurable separators.
4. [executeMethod ("PasteText")](/docs/plugins/interacting-with-editors/document-api/Methods/PasteText.md) — insert text at the current cursor position.

**Key concepts:** `initDataType: "text"` · `initOnSelectionChanged` · `GetSelectedText` · `PasteText` · `fetch` in plugins · side-panel plugin
