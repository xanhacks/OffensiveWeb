---
title: "Window object"
description: "Overview of Window object"
lead: "Overview of Window object"
date: 2023-01-01T00:00:00+00:00
lastmod: 2023-01-01T00:00:00+00:00
draft: false
images: []
menu:
  docs:
    parent: "client-side"
weight: 620
toc: true
---

## Definition

The [Window](https://developer.mozilla.org/en-US/docs/Web/API/Window) interface represents a window containing a DOM document; the document property points to the DOM document loaded in that window.

- `Window.opener`: Reference to the window that opened this current window.
- `Window.parent`: Reference to the parent of the current window or subframe.
- `Window.top`: Reference to the topmost window in the window hierarchy.
- `Window.document`: Reference to the document that the window contains.
- `Window.location`: Gets/sets the location, or current URL, of the window object.

Example of usage:

```js
let win = window.open("https://example.com");
win.location.href = "https://xanhacks.xyz";
win.document.body
// <body class="" style="transition: background-color 0.3s ease 0s;">
```

## Permissions

| Property      | Same-Origin   | Cross-Site    |
|:------------- |:-------------:|:-------------:|
| location      | READ / WRITE  | WRITE *       |
| document      | READ / WRITE  |               |
| frames        | READ / WRITE  | Restricted ** |
| localStorage  | READ / WRITE  |               |
| cookies       | READ / WRITE  |               |

*: No permission on `window.opener.location` on Firefox<br>
**: Restricted to some properties like postMessage...

## User Interaction

> Firefox prevented this site from opening 1 pop-up window.

Browsers often block window openings when there have been no prior user interactions. To enable window opening, you can set up an onClick listener on the body element.

```js
document.body.onclick = () => {
  window.open("https://www.xanhacks.xyz/") 
}
```