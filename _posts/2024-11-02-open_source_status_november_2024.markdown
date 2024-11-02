---
layout: post
title: "Open Source Status: November 2024 - dry-operation v1.0 is here!"
date: 2024-12-02 00:00:00 +0000
comments: true
categories: ["Open Source Status", "dry-rb", "dry-operation", "hanami"] 
published: true
---

I'm thrilled to announce that [dry-operation v1.0](https://dry-rb.org/gems/dry-operation/1.0/) has been released! I've wanted to streamline business logic flows in Ruby for ages, and having it now as part of both [dry-rb](https://dry-rb.org) and [Hanami](https://hanamirb.org/) feels like a perfect fit.

## What is dry-operation?

dry-operation provides an expressive and flexible way to model your app's business operations. It offers a lightweight DSL around [dry-monads](https://dry-rb.org/gems/dry-monads/1.6/) that lets you chain together steps and operations with a focus on the happy path while elegantly handling failures.

The library's core features include:

- A clean, linear approach to writing business flows
- Automatic failure short-circuiting
- Built-in database transaction support (ROM, Sequel and ActiveRecord supported out of the box)
- Failure hooks for cross-cutting concerns like logging
- Full opt-out flexibility for purists

Check out the [official documentation](https://dry-rb.org/gems/dry-operation/1.0/) to learn more about its features and how to get started.

## Acknowledgments

A special thank you goes to [Tim Riley](https://timriley.info/) for his invaluable support throughout this journey. His guidance and feedback have been instrumental in shaping dry-operation into what it is today.
