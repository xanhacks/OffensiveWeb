---
title: "Javascript"
description: "Overview of the Javascript programming language"
lead: "Overview of the Javascript programming language"
date: 2023-01-01T00:00:00+00:00
lastmod: 2023-01-01T00:00:00+00:00
draft: false
images: []
menu:
  docs:
    parent: "javascript"
weight: 620
toc: true
---

## Javascript

**JavaScript** is a dynamic, high-level programming language widely used for web development, enabling interactive and dynamic content on websites and applications, both on the client and server side.

## let and var

```js
let toto = "xyz";
window.toto; // undefined

var tata = "abc";
window.tata; // "abc"
```

## Properties

### Conditional Property Access

> Known as "optional chaining"

Check the left-hand side reference to see if it's null'ish (`null` or `undefined`). If so, the rest of the property access expression is short-circuited (skipped), and `undefined` is returned as the result (even if it was `null` that was actually encountered!). Otherwise, `?.` will access the property just as a normal `.` operator would.

```js
let profile = { address: { city: "Paris" } };
profile?.address?.city      // "Paris"

let emptyProfile = { address: null };
emptyProfile.address.city   // Uncaught TypeError: emptyProfile.address is null
emptyProfile?.address?.city // undefined
```

### Deleting Properties

```js
let user = {name: "xanhacks", password: "admin"};
user.hasOwnProperty("password")  // true

user.password = undefined;
user.hasOwnProperty("password"); // true

delete user.password
user.hasOwnProperty("password"); // false
```

## Non-Alphanumeric

Inspirated from [JSFuck](https://github.com/aemkei/jsfuck/blob/main/jsfuck.js).

Fuzzing can help you find some payloads, here is a little script that might help you:

```js
const charset = ["[", "]", "(", ")", "+", "!"];

function execAndCheck(code) {
	try {
		if (eval(code).includes("e")) {
			console.log(code)
		}
	} catch {}
}

function fuzzing(code, charset, max_length) {
	if (code.length > max_length) return;
	execAndCheck(code);

	charset.forEach((char) => {
		return fuzzing(code + char, charset, max_length);
	});
}

fuzzing("", charset, 7);
console.log("fuzzing done!");
```

Example of payloads where the return value contains the letter `e`:

```
> []+![]
'false'
> []+!+[]
'true'
> []+!![]
'true'
> ![]+[]
'false'
...
```

Create the "alert" string using "true" and "false":

```js
(![]+[])[+!![]] => "false"[1] => "a"
(![]+[])[+!![]+(+!![])] => "false"[2] => "l"
(!![]+[])[+!![]+(+!![])+(+!![])] => "true"[3] => "e"
(!![]+[])[+!![]] => "true"[1] => "r"
(!![]+[])[+![]] => "true"[0] => "t"

"alert" => (![]+[])[+!![]]+(![]+[])[+!![]+(+!![])]+(!![]+[])[+!![]+(+!![])+(+!![])]+(!![]+[])[+!![]]+(!![]+[])[+![]]
```

Code execution by accessing the constructor of a function:

```js
[]["at"] // shortest function name
> function at()

[]["at"]["constructor"] // unable to create "__proto__" or "prototype" string
> function Function()

[]["at"]["constructor"]("alert()")() // evaluates code
> *pop an alert()*
```

Create string "at" and "constructor":

```js
[][(![]+[])[+!![]]+(!![]+[])[+![]]]
> function at()

[]["at"]+""
> function at() {\n    [native code]\n}
([]["at"]+![])
> function at() {\n    [native code]\n}false

([]["at"]+"")[3]
> "c"
// same for o, n, (, )
```

Python script that generates the payload for an `alert`:

```python
E = {
	"a": "(![]+[])[+!![]]",
	"l": "(![]+[])[+!![]+!![]]",
	"e": "(!![]+[])[+!![]+!![]+!![]]",
	"r": "(!![]+[])[+!![]]",
	"t": "(!![]+[])[+![]]",
	"s": "(![]+[])[+!![]+!![]+!![]]",
	"u": "(!![]+[])[+!![]+!![]]"
}
E["alert"] = E["a"] + "+" + E["l"] + "+" + E["e"] + "+" + E["r"] + "+" + E["t"]
E["at"] = E["a"] + "+" + E["t"]

E["c"] = "([][" + E["at"] + "]+![])[+!![]+!![]+!![]]"
E["o"] = "([][" + E["at"] + "]+![])[+!![]+!![]+!![]+!![]+!![]+!![]]"
E["n"] = "([][" + E["at"] + "]+![])[+!![]+(+!![])]"
E["("] = "([][" + E["at"] + "]+![])[+!![]+!![]+!![]+!![]+!![]+!![]+!![]+!![]+!![]+!![]+!![]]"
E[")"] = "([][" + E["at"] + "]+![])[+!![]+!![]+!![]+!![]+!![]+!![]+!![]+!![]+!![]+!![]+!![]+!![]]"
E["()"] = E["("] + "+" + E[")"]

E["constructor"] = E["c"] + "+" + E["o"] + "+" + E["n"] + "+" + E["s"] + "+" + E["t"] + "+" + E["r"] + "+" + E["u"] + "+" + E["c"] + "+" + E["t"] + "+" + E["o"] + "+" + E["r"]

print("[][" + E["at"] + "][" + E["constructor"] + "](" + E["alert"] + "+" + E["()"] + ")()")
```

Final payload (776 chars) to trigger an `alert()`:

```js
[][(![]+[])[+!![]]+(!![]+[])[+![]]][([][(![]+[])[+!![]]+(!![]+[])[+![]]]+![])[+!![]+!![]+!![]]+([][(![]+[])[+!![]]+(!![]+[])[+![]]]+![])[+!![]+!![]+!![]+!![]+!![]+!![]]+([][(![]+[])[+!![]]+(!![]+[])[+![]]]+![])[+!![]+(+!![])]+(![]+[])[+!![]+!![]+!![]]+(!![]+[])[+![]]+(!![]+[])[+!![]]+(!![]+[])[+!![]+!![]]+([][(![]+[])[+!![]]+(!![]+[])[+![]]]+![])[+!![]+!![]+!![]]+(!![]+[])[+![]]+([][(![]+[])[+!![]]+(!![]+[])[+![]]]+![])[+!![]+!![]+!![]+!![]+!![]+!![]]+(!![]+[])[+!![]]]((![]+[])[+!![]]+(![]+[])[+!![]+!![]]+(!![]+[])[+!![]+!![]+!![]]+(!![]+[])[+!![]]+(!![]+[])[+![]]+([][(![]+[])[+!![]]+(!![]+[])[+![]]]+![])[+!![]+!![]+!![]+!![]+!![]+!![]+!![]+!![]+!![]+!![]+!![]]+([][(![]+[])[+!![]]+(!![]+[])[+![]]]+![])[+!![]+!![]+!![]+!![]+!![]+!![]+!![]+!![]+!![]+!![]+!![]+!![]])()
```

## Bypass characters

```js
[[]==0]+[]          // "true"
[]==0+``            // "false"
1E1000+``           // "Infinity" 
``["fontcolor"]``   // '<font color=""></font>'
[]["entries"]``+``  // "[object Array Iterator]" 
20["toString"](21)  // "k"
document.body["attributes"][0] // class="docs single"
```

## References

- [getify/You-Dont-Know-JS](https://github.com/getify/You-Dont-Know-JS/)
