---
sidebar_position: 1
---

# Build a YouTube inserter

Learn how to accept a YouTube URL from the user and embed the video as a live OLE object inside an ONLYOFFICE document.

**What you'll build:** A plugin that opens a small modal dialog, accepts a YouTube video URL, validates it, fetches a thumbnail, and calls `AddOleObject` to embed a clickable video preview in the document. Double-clicking the object reopens the dialog so the user can swap the URL.

## Prerequisites

- A working ONLYOFFICE plugin development environment — see [Plugin development tutorial](/docs/plugins/structure/getting-started.md).
- Basic familiarity with `config.json`, `index.html`, and the plugin JS file — see [Plugin structure](/docs/plugins/structure/configuration/configuration.md).
- A YouTube account is not required; any public video URL will work.

## Step 1 — Scaffold the plugin

Create the plugin folder with the standard files:

```
youtube/
├── config.json
├── index.html
└── youtube.js
```

The YouTube plugin uses a **modal dialog** (`isModal: true`) with an OLE data type so the editor can round-trip the stored URL whenever the embedded object is double-clicked.

```json
{
  "name": "YouTube",
  "guid": "asc.{38E022EA-AD92-45FC-B22B-49DF39746DB4}",
  "version": "1.0.4",
  "variations": [
    {
      "description": "Easily embed YouTube videos into your documents.",
      "url": "index.html",
      "icons": ["resources/img/icon.png", "resources/img/icon@2x.png"],
      "isViewer": true,
      "isDisplayedInViewer": false,
      "EditorsSupport": ["word", "cell", "slide"],
      "isVisual": true,
      "isModal": true,
      "isInsideMode": false,
      "initDataType": "ole",
      "isUpdateOleOnResize": false,
      "buttons": [
        { "text": "Ok",     "primary": true,  "isViewer": false },
        { "text": "Cancel", "primary": false, "isViewer": true  }
      ],
      "size": [350, 90]
    }
  ]
}
```

Key settings to notice:

| Setting | Value | Why |
|---|---|---|
| `initDataType` | `"ole"` | Tells the editor to pass the stored OLE data back to the plugin on re-open |
| `isModal` | `true` | Opens as a small floating dialog instead of a side panel |
| `isUpdateOleOnResize` | `false` | Prevents the editor from re-requesting a new thumbnail when the user resizes the embedded object |
| `isViewer` | `true` | Allows the plugin to appear in view-only mode (the Cancel button is visible; OK is hidden) |

## Step 2 — Build the dialog UI

In `index.html`, create a minimal form: a text input for the URL and the standard OK / Cancel buttons (which are provided by the plugin frame itself — you only need to wire up the input):

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <title>YouTube</title>
    <style>
      body {
        font-family: Arial, sans-serif;
        margin: 8px;
        display: flex;
        align-items: center;
        gap: 6px;
      }
      #url-input {
        flex: 1;
        padding: 4px 6px;
        font-size: 13px;
        border: 1px solid #ccc;
        border-radius: 3px;
      }
    </style>
  </head>
  <body>
    <label for="url-input">YouTube URL:</label>
    <input id="url-input" type="text" placeholder="https://www.youtube.com/watch?v=..." />
    <script src="youtube.js"></script>
  </body>
</html>
```

The `size: [350, 90]` in `config.json` keeps the dialog compact — just enough for the label and input field.

## Step 3 — Handle init and OLE round-trip

In `youtube.js`, implement `window.Asc.plugin.init`. The editor calls `init` both when a user opens the plugin fresh and when they double-click an existing OLE object. Use `window.Asc.plugin.info` to detect which case you are in and pre-populate the input:

```js
window.Asc.plugin.init = function () {
  // info.data contains the stored OLE data string on re-open; empty on first open
  var stored = window.Asc.plugin.info.data;
  if (stored) {
    document.getElementById("url-input").value = stored;
  }
};
```

`window.Asc.plugin.info.data` is the raw string you pass as `"data"` when calling `AddOleObject` or `EditOleObject`. Storing the original YouTube URL there makes it easy to retrieve on re-open.

## Step 4 — Extract the video ID and build a thumbnail URL

YouTube's thumbnail CDN follows a predictable pattern. Parse the video ID from whatever URL format the user pastes:

```js
function getVideoId(url) {
  // Handles both youtube.com/watch?v=ID and youtu.be/ID formats
  var match = url.match(
    /(?:youtube\.com\/watch\?(?:.*&)?v=|youtu\.be\/)([A-Za-z0-9_-]{11})/
  );
  return match ? match[1] : null;
}

function getThumbnailUrl(videoId) {
  // hqdefault gives a 480×360 image — good enough as an OLE preview
  return "https://img.youtube.com/vi/" + videoId + "/hqdefault.jpg";
}
```

## Step 5 — Insert or update the OLE object

Wire up the OK button via `window.Asc.plugin.button`. The editor calls this function with `id === 0` when the user clicks OK and `id === 1` (or closes the dialog) for Cancel:

```js
window.Asc.plugin.button = function (id) {
  if (id !== 0) {
    // User clicked Cancel — just close
    window.Asc.plugin.close();
    return;
  }

  var url   = document.getElementById("url-input").value.trim();
  var vidId = getVideoId(url);

  if (!vidId) {
    alert("Please enter a valid YouTube URL.");
    return;
  }

  var thumbUrl  = getThumbnailUrl(vidId);
  var objectId  = window.Asc.plugin.info.objectId;  // non-null when editing an existing OLE

  // Shared OLE properties
  var oleParams = {
    "data"      : url,          // stored and returned in info.data on re-open
    "imgSrc"    : thumbUrl,     // preview image shown in the document
    "guid"      : window.Asc.plugin.info.guid,
    "width"     : 70,           // display width  in mm
    "height"    : 70,           // display height in mm
    "widthPix"  : 60 * 36000,  // internal pixel width
    "heightPix" : 60 * 36000   // internal pixel height
  };

  if (objectId) {
    // Editing an existing embedded video — update it in place
    oleParams["objectId"] = objectId;
    window.Asc.plugin.executeMethod("EditOleObject", [oleParams], function () {
      window.Asc.plugin.close();
    });
  } else {
    // First-time insert
    window.Asc.plugin.executeMethod("AddOleObject", [oleParams], function () {
      window.Asc.plugin.close();
    });
  }
};
```

**Why store the URL in `"data"`?** When the user double-clicks the embedded object, the editor re-opens the plugin and populates `info.data` with that string. This lets you pre-fill the input field so the user can change the URL without retyping it from scratch.

## Step 6 — Test the plugin

1. Zip the `youtube/` folder and install it via **Plugins → Plugin Manager → Upload plugin**.
2. Open the plugin from the **Plugins** tab.
3. Paste a YouTube URL (e.g. `https://www.youtube.com/watch?v=jHuwwVliZ5Q`) and click **OK**.
4. A thumbnail image appears in the document as an embedded object.
5. Double-click the object — the dialog reopens with the original URL pre-filled.
6. Change the URL and click **OK** again — the thumbnail updates in place.

## Going further

- Detect the `isViewer` flag via `window.Asc.plugin.info.isViewer` to hide the input and show a "View only" message in viewer mode.
- Add a live preview thumbnail below the input field that updates as the user types.
- Support playlist URLs by detecting `list=` parameters and falling back to a generic YouTube thumbnail.
- Localise the plugin by adding a `translations/` folder following the [localization guide](/docs/plugins/structure/localization.md).

**Resources:**

1. [YouTube plugin sample](/docs/plugins/samples/youtube.md) — reference implementation you can run immediately.
2. [Plugin structure](/docs/plugins/structure/configuration/configuration.md) — full `config.json` field reference.
3. [executeMethod ("AddOleObject")](/docs/plugins/interacting-with-editors/document-api/Methods/AddOleObject.md) — inserts a new OLE object.
4. [executeMethod ("EditOleObject")](/docs/plugins/interacting-with-editors/document-api/Methods/EditOleObject.md) — updates an existing OLE object in place.

**Key concepts:** OLE objects · `AddOleObject` · `EditOleObject` · `initDataType: "ole"` · `info.data` · thumbnail CDN
