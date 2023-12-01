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
    parent: "others"
weight: 620
toc: true
---

## Definition

**Debugging** involves examining and testing software to understand its behavior, identify any anomalies or errors.

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

{{< details "Tutorial - GIF" >}}
![Restart frame](https://code.visualstudio.com/assets/docs/nodejs/nodejs-debugging/restartFrame.gif)
{{< /details >}}

#### Conditional breakpoint

Conditional breakpoints are breakpoints that only pause when an expression returns a truthy value. See [docs](https://code.visualstudio.com/docs/nodejs/nodejs-debugging#_conditional-breakpoints).

{{< details "Tutorial - GIF" >}}
![Conditional breakpoint](https://code.visualstudio.com/assets/docs/nodejs/nodejs-debugging/conditional-breakpoint.gif)
{{< /details >}}

#### Logpoints

Log a message or value when code hits a certain location. See [docs](https://code.visualstudio.com/docs/nodejs/nodejs-debugging#_logpoints).

{{< details "Tutorial - GIF" >}}
![Logpoints](https://code.visualstudio.com/assets/docs/nodejs/nodejs-debugging/logpoint-breakpoint.gif)
{{< /details >}}

### The arguments object

[arguments](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments) is an array-like object accessible inside functions that contains the values of the arguments passed to that function.

{{< details "Demo - Custom debug function" >}}
```js
function _debugFunc(args) {
	let funcName = (new Error()).stack.match(/at (\S+)/g)[1].slice(3); // handle strict mode

	console.log(`\n===> call ${funcName}(args.length = ${args.length})`);
	Array.from(args).forEach((arg, index) => {
		if (typeof arg === "string" && arg.length > 20) {
			console.log(` | args[${index}] = (${typeof arg}) ${arg.substring(0, 20) + "..."}`);
		} else if (typeof arg == "function") {
			console.log(` | args[${index}] = (${typeof arg}) ${arg.toString().substring(0, 20) + "..."}`);
		} else if (typeof arg == "object") {
			console.log(` | args[${index}] = (${typeof arg})`);
			console.log(arg);
		} else {
			console.log(` | args[${index}] = (${typeof arg}) ${arg}`);
		}
	});
}

function sum(a, b) {
	_debugFunc(arguments);
	return a + b;
}

function sayHello(name) {
	"use strict";
	_debugFunc(arguments);
	console.log(`Hello ${name} !`);
}

function multiply(obj) {
	_debugFunc(arguments);
	return obj.first * obj.second;
}

console.log(sum(3, 5));
sayHello("world");
console.log(multiply({first: 12, second: 2}));
```

```bash
$ node example.js
===> call sum(args.length = 2)
 | args[0] = (number) 3
 | args[1] = (number) 5
8

===> call sayHello(args.length = 1)
 | args[0] = (string) world
Hello world !

===> call multiply(args.length = 1)
 | args[0] = (object)
{ first: 12, second: 2 }
24
```
{{< /details >}}
