---
title: "Debugging"
description: "Overview of Cross-Site Scripting"
lead: "Overview of Cross-Site Scripting"
date: 2023-01-01T00:00:00+00:00
lastmod: 2023-01-01T00:00:00+00:00
draft: false
images: []
menu:
  docs:
    parent: "topics"
weight: 620
toc: true
---

## Node.js

### VSCode

You can attach VSCode to a Node.js process ([docs](https://code.visualstudio.com/docs/nodejs/nodejs-debugging#_attaching-to-nodejs)) in only two steps.

1. **Launch the process in debug mode:**

- Run and attach: `node --inspect index.js`
- Wait for the debugger to attach before running: `node --inspect-brk index.js`

2. **Attach VSCode to the process:**

Press `CTRL+Shift+P` to open a Quick Pick menu, then select `Debug: Attach to Node Process`.

![Quick Pick menu](https://i.imgur.com/8Zovg4u.png)

Choose the process you want to debug. Then, the `Run and Debug` window will appears:

![Run and Debug](https://i.imgur.com/QjZS1d3.png)
