---
layout: post
title: "Open Source Status: November 2023 - dry-operation failure hooks & database transactions"
date: 2023-12-01 00:00:00 +0000
comments: true
categories: ["Open Source Status", hanami, web_pipe, dry-operation] 
published: true
---
This month, there's a lot in the making! My two main priorities remain [dry-operation](https://github.com/dry-rb/dry-operation) and [web_pipe](https://github.com/waiting-for-dev/web_pipe), and I've put a great deal of thought into both of them. I'm excited to share the progress I've made, so let's get started!

## dry-operation: the "unhappy" path & database transactions

As I've discussed in [previous updates]({% post_url 2023-11-07-open_source_status_october_2023 %}), dry-operation is all about streamlining the happy path. This doesn't mean that the "unhappy" path is neglected, but it doesn't impede understanding the intended flow. Usually, individual operations are responsible for locally managing their failures. Often, that's sufficient. The caller will likely also perform some form of failure handling in respect to the entire flow, such as returning a 4xx response code. However, sometimes it's useful to encapsulate part of that global error handling in the flow instance, treating it as something intrinsic that should be done regardless of the caller (e.g., logging a failure). To facilitate this, [we're introducing an `#on_failure hook`](https://github.com/dry-rb/dry-operation/pull/14) that can be defined to be called when things go wrong.

```ruby
class CreateUser < Dry::Operation
  def call(input)
    attrs = step validate(input)
    step persist(attrs)
    user
  end

  private

  def on_failure(failure)
    log_failure(failure)
  end

  # ...
end
```

Regarding database transactions, there are two main approaches we could consider. The first is to wrap the entire flow in a transaction, which appears to be the more user-friendly option. The second is to require manually wrapping the desired operations via a `#transaction` method, allowing more fine-grained control. The general behavior in both cases would be the same: rolling back a DB transaction, if present, in the case of an operation returning a failure. After much consideration, we've decided to go with the second approach. A lot has been done to hide the impedance of database transactions from the developer's eyes, and few of these efforts have been successful. Database transactions are lower-level details that developers need to be aware of, ensuring that no expensive operations are wrapped within them. Although it goes against a vision of composable flows, dry-operation will lean towards encouraging developers to compose operations instead of entire flows. Thanks to its design and helper libraries like dry-auto_inject, dry-operation operations are completely decoupled from the wrapping flow, making them suitable for composability at the right level of granularity.

An [extension for ROM](https://github.com/dry-rb/dry-operation/pull/15) is the first working example of this approach, but we'd eventually add support for other libraries.

```ruby
class MyOperation < Dry::Operation
  include Dry::Operation::Extensions::ROM

  attr_reader :rom

  def initialize(rom:)
    @rom = rom
  end

  def call(input)
    attrs = step validate(input)
    user = transaction do
      new_user = step persist(attrs)
      step assign_initial_role(new_user)
      new_user
    end
    step notify(user)
    user
  end

  # ...
end
```

By the way, if you're more interested in the thought process behind these decisions, you can check and comment on [gist](https://gist.github.com/waiting-for-dev/7615ae577807e3c3b990cd8c53670b2a) where we discussed the approach to take.

## web_pipe: welcome to the Zeitwerk family

From now on, [web_pipe is part of the growing family of Zeitwerk-enabled](https://github.com/waiting-for-dev/web_pipe/pull/54) Ruby gems.

Additionally, I'm experimenting a lot with its architecture, and it could result in a significant overhaul of its internals. The idea is to remove injection responsibilities from it and, instead, rely on something like dry-auto_inject. However, it's still too soon to share more information, so please stay tuned!
