---
sidebar_position: -3
---

# Icons

When building a plugin for ONLYOFFICE, adding icons can significantly enhance usability and make your interface more intuitive.

## Folder structure

Before adding icons, it’s important to organize your plugin files properly.
A clean folder structure ensures that ONLYOFFICE can correctly locate your resources and that your plugin remains easy to maintain as it grows.

Your plugin should include a /resources directory to store all images, icons, and other static assets.
This folder is automatically accessible to ONLYOFFICE when the plugin is loaded.

Here’s a recommended layout:


``` ts
my-plugin/
├── config.json
├── index.html
├── plugin.js
└── resources/
    ├── dark/
    │   ├── icon.png
    │   ├── icon@1.25x.png
    │   ├── icon@1.5x.png
    │   ├── icon@1.75x.png
    │   ├── icon@2x.png
    │
    ├── light/
    │   ├── icon.png
    │   ├── icon@1.25x.png
    │   ├── icon@1.5x.png
    │   ├── icon@1.75x.png
    │   ├── icon@2x.png
    │
    └── store/
        ├── icons/
        │   ├── icon.png
        │   ├── icon.svg
        │   ├── icon@1.25x.png
        │   ├── icon@1.5x.png
        │   ├── icon@1.75x.png
        │   └── icon@2x.png
        └── screenshots/
            ├── screen_1.png
            ├── screen_2.png
            ├── screen_3.png
            ├── screen_4.png
            ├── screen_5.png
            └── screen_6.png
```
This hierarchy allows ONLYOFFICE to automatically choose the correct icon based on the theme, state, and scale.

## Defining the icon in config.json

Inside your config.json, define your icon using a smart URL pattern that adapts to themes and scaling:

``` json
{
"name": "My Plugin",
 
"guid": "asc.{UUID}",
 
"version": "1.0.0",
 
"description": "Example plugin with adaptive icons",
 
"icons": [
 
"resources/%theme-name%(classic|dark)/%theme-type%(light|dark)/icon%state%(normal|hover)%scale%(default|*).%extension%(png|svg)"
 
],
 
"isVisual": true,
 
"initDataType": "none",
 
"initOnSelectionChanged": false
}
```

This single line dynamically tells the editor where to look for the icon depending on:

* The theme name (classic or dark)
* The theme type (light or dark)
* The state (normal, hover)
* The scale (100%, 125%, 150%, etc.)
* The extension (png or svg)

## How it works

When the plugin loads, ONLYOFFICE:

1. Detects the active editor theme (light/dark).
2. Checks the screen scale (e.g. 125%).
3. Loads the corresponding icon variant.
4. If no perfect match is found, it picks the closest size available.

If the user is working in Dark Theme with a 150% zoom level, the editor automatically applies:

```
resources/dark/dark/icon@1.5x.png
```

## Minimal example

If you don’t need multiple scales or themes, you can keep it simple:

```
"icons": ["resources/icon.svg"]
```
That’s all you need — the SVG format is automatically scaled and works for both light and dark modes.

## Optional: adding icons to content controls

You can also use icons inside content control buttons introduced in version 9.0:

``` ts
let button = new Asc.ButtonContentControl();
 
button.icons = "/resources/check%scale%(default).png";
 
button.attachOnClick(function(contentControlId){
 
Asc.plugin.executeMethod("RemoveContentControl", [contentControlId]);
 
});
```
This allows you to add interactive buttons directly inside your document content.

## Tips for better icons

* Use SVG whenever possible it scales perfectly and looks crisp at any DPI.
* Keep your icon canvas size around 24×24 or 32×32 px.
* Always test your icons in light and dark themes.
* Use naming consistency, for example: icon_hover@1.25x.png.

> This string generates the objects in the old icon format (the [icon2](./configuration/configuration.md#variationsicons2) parameter):

``` ts
[
  {
    "theme": "classic",
    "style": "light",
    "100%": {normal: "resources/classic/light/icon.png", hover: "resources/classic/light/icon_hover.png"},
    "125%": {normal: "resources/classic/light/icon@1.25x.png", hover: "resources/classic/light/icon_hover@1.25x.png"},
    "150%": {normal: "resources/classic/light/icon@1.5x.png", hover: "resources/classic/light/icon_hover@1.5x.png"},
    "175%": {normal: "resources/classic/light/icon@1.75x.png", hover: "resources/classic/light/icon_hover@1.75x.png"},
    "200%": {normal: "resources/classic/light/icon@2x.png", hover: "resources/classic/light/icon_hover@2x.png"},
    "*": {normal: "resources/classic/light/icon.svg", hover: "resources/classic/light/icon_hover.svg"},
  },
  {
    "theme": "classic",
    "style": "dark",
    "100%": {normal: "resources/classic/dark/icon.png", hover: "resources/classic/dark/icon_hover.png"},
    "125%": {normal: "resources/classic/dark/icon@1.25x.png", hover: "resources/classic/dark/icon_hover@1.25x.png"},
    "150%": {normal: "resources/classic/dark/icon@1.5x.png", hover: "resources/classic/dark/icon_hover@1.5x.png"},
    "175%": {normal: "resources/classic/dark/icon@1.75x.png", hover: "resources/classic/dark/icon_hover@1.75x.png"},
    "200%": {normal: "resources/classic/dark/icon@2x.png", hover: "resources/classic/dark/icon_hover@2x.png"},
    "*": {normal: "resources/classic/dark/icon.svg", hover: "resources/classic/dark/icon_hover.svg"},
  },
  {
    "theme": "dark",
    "style": "light",
    "100%": {normal: "resources/dark/light/icon.png", hover: "resources/dark/light/icon_hover.png"},
    "125%": {normal: "resources/dark/light/icon@1.25x.png", hover: "resources/dark/light/icon_hover@1.25x.png"},
    "150%": {normal: "resources/dark/light/icon@1.5x.png", hover: "resources/dark/light/icon_hover@1.5x.png"},
    "175%": {normal: "resources/dark/light/icon@1.75x.png", hover: "resources/dark/light/icon_hover@1.75x.png"},
    "200%": {normal: "resources/dark/light/icon@2x.png", hover: "resources/dark/light/icon_hover@2x.png"},
    "*": {normal: "resources/dark/light/icon.svg", hover: "resources/dark/light/icon_hover.svg"},
  },
  {
    "theme": "dark",
    "style": "dark",
    "100%": {normal: "resources/dark/dark/icon.png", hover: "resources/dark/dark/icon_hover.png"},
    "125%": {normal: "resources/dark/dark/icon@1.25x.png", hover: "resources/dark/dark/icon_hover@1.25x.png"},
    "150%": {normal: "resources/dark/dark/icon@1.5x.png", hover: "resources/dark/dark/icon_hover@1.5x.png"},
    "175%": {normal: "resources/dark/dark/icon@1.75x.png", hover: "resources/dark/dark/icon_hover@1.75x.png"},
    "200%": {normal: "resources/dark/dark/icon@2x.png", hover: "resources/dark/dark/icon_hover@2x.png"},
    "*": {normal: "resources/dark/dark/icon.svg", hover: "resources/dark/dark/icon_hover.svg"},
  },
]
```

This format can still be used, but it is deprecated.
