---
title: "Ruby"
description: "Overview of Ruby"
lead: "Overview of Ruby"
date: 2023-01-01T00:00:00+00:00
lastmod: 2023-01-01T00:00:00+00:00
draft: false
images: []
menu:
  docs:
    parent: "programming"
weight: 620
toc: true
---

## Ruby

[Ruby](https://www.ruby-lang.org/en/) is a dynamic, open-source, object-oriented programming language known for its simplicity and productivity, often used for web development and data analysis.

## send

The `send` method allows you to dynamically invoke a method.

```ruby
class HelloWorld
  def print(*args)
    puts("Hello " + args.join(' '))
  end
end

obj = HelloWorld.new()
obj.print('world') # => 'Hello World'
obj.send('print', 'world') # => 'Hello World'
```

## References

- [Send()-ing Myself Belated Christmas Gifts - GitHub.com's Environment Variables & GHES Shell](https://starlabs.sg/blog/2024/04-sending-myself-github-com-environment-variables-and-ghes-shell/)
- [Attacking Ruby on Rails Applications](http://phrack.org/issues/69/12.html)