---
title: "Debugging"
description: "Tutorials on different ways to debug your programs to do better testing."
lead: "Tutorials on different ways to debug your programs to do better testing."
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

#### Getting started

You can attach VSCode to a Node.js process in only two steps. See [docs](https://code.visualstudio.com/docs/nodejs/nodejs-debugging#_attaching-to-nodejs).

1. **Launch the process in debug mode:**

- Run and attach: `node --inspect index.js`
- Wait for the debugger to attach before running: `node --inspect-brk index.js`

2. **Attach VSCode to the process:**

Press `CTRL+Shift+P` to open a Quick Pick menu, then select `Debug: Attach to Node Process`.

![Quick Pick menu](https://i.imgur.com/8Zovg4u.png)

Choose the process you want to debug. Then, the `Run and Debug` window will appears:

![Run and Debug](https://i.imgur.com/QjZS1d3.png)

#### Restart frame

The Node debugger supports restarting execution at a stack frame. See [docs](https://code.visualstudio.com/docs/nodejs/nodejs-debugging#_restart-frame).

![Restart frame](https://code.visualstudio.com/assets/docs/nodejs/nodejs-debugging/restartFrame.gif)

#### Conditional breakpoint

Conditional breakpoints are breakpoints that only pause when an expression returns a truthy value. See [docs](https://code.visualstudio.com/docs/nodejs/nodejs-debugging#_conditional-breakpoints).

![Conditional breakpoint](https://code.visualstudio.com/assets/docs/nodejs/nodejs-debugging/conditional-breakpoint.gif)

#### Logpoints

Log a message or value when code hits a certain location. See [docs](https://code.visualstudio.com/docs/nodejs/nodejs-debugging#_logpoints).

![Logpoints](https://code.visualstudio.com/assets/docs/nodejs/nodejs-debugging/logpoint-breakpoint.gif)
