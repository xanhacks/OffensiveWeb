---
title: "Spring Boot"
description: "Cheatsheet on Spring Boot"
lead: "Cheatsheet on Spring Boot"
date: 2023-01-01T00:00:00+00:00
lastmod: 2023-01-01T00:00:00+00:00
draft: false
images: []
menu:
  docs:
    parent: "framework"
weight: 620
toc: true
---

## Spring Boot

[Spring Boot](https://spring.io/) is a Java-based framework used to create stand-alone, production-grade Spring applications with minimal configuration. It simplifies the bootstrapping and development of new Spring applications by providing various capabilities like auto-configuration, standalone code, and opinionated defaults.

## Tree Achitecture

- **java/**: Contains Java source code, including the main application class.
- **resources/**: Stores configuration files, static assets, and templates.
- **test/**: Holds test classes and resources.
- **static/**: For static files like CSS and JavaScript.
- **templates/**: For server-side templates.
- **service/**: Contains business logic implementation.
- **models/**: Defines data structures.
- **repository/**: Handles database operations.
- **META-INF/**: For special files like `MANIFEST.MF`.
- **target/**: Generated build artifacts.
- **pom.xml or build.gradle**: Build configuration files.

## Annotations

- `@Controller`: Marks a class as a Spring MVC controller, handling HTTP requests.
- `@RestController`: Combines `@Controller` and `@ResponseBody`, making it suitable for building RESTful APIs.
- `@RequestMapping`: Maps all HTTP verbs to controller methods.
- `@GetMapping`, `@PostMapping`, `@PutMapping`, `@DeleteMapping`: Specialized versions of `@RequestMapping` for specific HTTP methods.
- `@PathVariable`: Extracts values from the URI and maps them to method parameters.
- `@RequestParam`: Binds HTTP request parameters to method parameters.
- `@RequestBody`: Binds HTTP request body to a method parameter, typically used for receiving JSON or XML data in RESTful APIs.
- `@ResponseBody`: Indicates that the return value of a method should be serialized and included in the HTTP response body.
- `@Autowired`: Performs automatic dependency injection of Spring components.
- `@Service`: Marks a class as a service component, often used for business logic.
- `@Repository`: Marks a class as a repository component, typically used for database access.
