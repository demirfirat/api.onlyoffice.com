---
sidebar_position: 3
---

# Build a photo editor plugin

Learn how to embed an interactive image editor inside ONLYOFFICE and write the result back to the document.

**What you'll build:** A plugin that opens the selected image in an in-panel photo editor, lets the user crop, rotate, and apply filters, then replaces the original image in the document with the edited version.

## Prerequisites

- A working ONLYOFFICE plugin development environment — see [Plugin development tutorial](/docs/plugins/structure/getting-started.md).
- Basic familiarity with `config.json`, `index.html`, and `code.js` — see [Plugin structure](/docs/plugins/structure/configuration/configuration.md).

## Step 1 — Scaffold the plugin

Create the plugin folder with the standard three files:

```
photo-editor/
├── config.json
├── index.html
└── code.js
```

Set `isViewer: false` and `isSystem: false` in `config.json`. Add a toolbar button that users click to open the editor panel:

```json
{
  "name": "Photo editor",
  "guid": "asc.{07FD8DFA-DFE0-4089-AL24-0730933CC80A}",
  "version": "1.0",
  "variations": [
    {
      "description": "Photo editor",
      "url": "index.html",
      "icons": ["resources/img/icon.png", "resources/img/icon@2x.png"],
      "isViewer": false,
      "EditorsSupport": ["word", "cell", "slide"],
      "isVisibleOnlyForEditorWithChanges": false,
      "isDisplayedInViewer": false,
      "initDataType": "none",
      "initData": "",
      "isModal": false,
      "isInsideMode": true,
      "initOnSelectionChanged": true,
      "buttons": []
    }
  ]
}
```

## Step 2 — Detect the selected image

In `code.js`, listen for `window.Asc.plugin.init` and use `executeMethod` to get the selected image data:

```js
window.Asc.plugin.init = function () {
  window.Asc.plugin.executeMethod("GetSelectedImage", [], function (data) {
    if (data && data.ImageUrl) {
      // pass the URL to the editor panel
      sendToPanel({ type: "load-image", url: data.ImageUrl });
    }
  });
};
```

`GetSelectedImage` returns an object with `ImageUrl` (a base64 data URI or a server URL) and the image dimensions.

## Step 3 — Build the editor panel

In `index.html`, embed [Filerobot Image Editor](https://scaleflex.github.io/filerobot-image-editor/) (MIT-licensed) via CDN, or use the Canvas API for simpler transforms. A minimal setup with Filerobot:

```html
<!DOCTYPE html>
<html>
  <head>
    <script src="https://cdn.scaleflex.it/plugins/filerobot-image-editor/4.3.5/filerobot-image-editor.min.js"></script>
  </head>
  <body>
    <div id="editor-container"></div>
    <script src="plugin-panel.js"></script>
  </body>
</html>
```

In `plugin-panel.js`, initialise the editor when the plugin sends `load-image`:

```js
window.addEventListener("message", function (e) {
  if (e.data.type === "load-image") {
    const config = {
      src: e.data.url,
      onSave: function (editedImageObject) {
        // send the result back to code.js
        window.parent.postMessage({ type: "save-image", dataUrl: editedImageObject.imageBase64 }, "*");
      },
    };
    FilerobotImageEditor.open(config);
  }
});
```

## Step 4 — Replace the image in the document

Back in `code.js`, receive the edited image and use `executeMethod` to replace the selected image:

```js
window.addEventListener("message", function (e) {
  if (e.data.type === "save-image") {
    window.Asc.plugin.executeMethod("PutImageBase64", [e.data.dataUrl], function () {
      window.Asc.plugin.close();
    });
  }
});
```

`PutImageBase64` accepts a base64-encoded PNG or JPEG string and replaces the currently selected image object in the document.

## Step 5 — Test the plugin

1. Package the plugin folder as a zip and install it via **Plugins → Plugin Manager → Upload plugin**.
2. Insert any image into a document.
3. Click the image to select it, then open the plugin panel.
4. Crop or filter the image and click **Save** — the original is replaced in place.

## Going further

- Add support for inserting a new image (not just replacing) using `executeMethod("InsertImage", ...)`.
- Expose filter presets (vintage, black-and-white, sharpen) as quick-action buttons in the panel toolbar.
- Store user preferences (default crop ratio, last-used filter) in `localStorage` so they persist across sessions.

**Resources:**

1. [Photo editor plugin sample](/docs/plugins/samples/photo-editor.md) — reference implementation you can run immediately.
2. [Plugin structure](/docs/plugins/structure/configuration/configuration.md) — full `config.json` field reference.
3. [executeMethod reference](/docs/plugins/interacting-with-editors/overview/how-to-call-methods.md) — complete list of editor methods available to plugins.

**Key concepts:** `GetSelectedImage` · `PutImageBase64` · in-panel iframe · base64 image data
