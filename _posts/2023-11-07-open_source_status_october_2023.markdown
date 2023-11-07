---
layout: post
title: "Open Source Status: October 2023 - Syntax: dry-operation vs. do notation"
date: 2023-11-07 00:00:00 +0000
comments: true
categories: ["Open Source Status", hanami, web_pipe] 
published: true
---
In [September]({% post_url 2023-10-10-open_source_status_september_2023 %}), we witnessed the birth of [dry-operation](https://github.com/dry-rb/dry-operation), a new library designed for managing business flows in Ruby. During October, my focus shifted towards optimizing its developer experience (DX) and also allowed me to revisit my beloved project, web_pipe.

## dry-operation: Ruby's magic wand to remove boilerplate

I typically approach Ruby's metaprogramming capabilities with caution. I've seen them misused too many times, and I strongly believe that, in most cases, being explicit rather than implicit is the better choice. However, there are certain scenarios where metaprogramming can be a powerful tool for reducing boilerplate and enhancing the developer experience. This is precisely where dry-operation shines.

The essence of dry-operation lies in crafting easily readable business flows, emphasizing the "happy path." Back in September, we agreed upon the following interface:

```ruby
class MyOperation < Dry::Operation
  def call(input)
    steps do
      attrs = step validate(input)
      user = step persist(attrs)
      step notify(user)
      user
    end
  end
end
```

The `step` method will unwrap a `Dry::Monad::Result::Success` returned by each operation, but it'll short-circuit the flow in case of `Failure`. It does so by throwing a symbol that is caught by the surrounding `#steps`.

Already, this is quite optimized for readability. Now, see how it could appear without dry-operation:

```ruby
class MyOperation 
  def call(input)
    validate(input).bind do |attrs|
      persist(attrs).bind do |user|
        notify(user).bind do |user|
          Success(user)
        end
      end
    end
  end
end
```

You're correct; we can do better with dry-monad's do notation:

```ruby
class MyOperation 
  include Dry::Monads::Do.for(:call)
  
  def call(input)
    attrs = yield validate(params)
    user = yield persist(attrs)
    yield notify(user)
    
    Success(user)
  end
end
```

There are other benefits that dry-operation will provide over dry-monad's do notation, but we can already compare their boilerplate. dry-operation comes out ahead in the following aspects:

- Inheriting from `Dry::Operation` is more concise than including `Dry::Monads::Do.for(:call)`.
- Using a `step` method feels less confusing than `yield`.
- There's no need to explicitly wrap the returned value in a `Success` object in dry-operation.

Nonetheless:

- dry-monad's do notation doesn't require wrapping the sequence of steps in a surrounding block (`steps` in dry-operation).

Here's where the metaprogramming magic comes into play. After a [couple](https://github.com/dry-rb/dry-operation/pull/11) of [iterations](https://github.com/dry-rb/dry-operation/pull/9), and thanks to the valuable feedback from [Tim Riley](https://timriley.info/), the happy path in dry-operation has become even more straightforward, as the `#steps` block is no longer required:

```ruby
class MyOperation < Dry::Operation
  def call(input)
    attrs = step validate(input)
    user = step persist(attrs)
    step notify(user)
    user
  end
end
```

The way it works is that `Dry::Operation` will automatically prepend a `steps` block to the `#call` method. All batteries are included by default, but if you're a purist there's always the possibility to opt-out of any kind of magic through a `skip_prepending` class method.

## web_pipe: on it's way to 1.0

[web_pipe](https://github.com/waiting-for-dev/web_pipe) serves as a lightweight layer on top of Rack, designed for building composable web applications. It has been in existence for a while, yet it hasn't reached version 1.0. I am committed to changing that and gradually solidifying its interface until the next major release. Last month, I made a few updates:

- I [updated the hanami-view extension](https://github.com/waiting-for-dev/web_pipe/pull/50) to accommodate upstream breaking changes. It now can be used integrated within a Hanami 2.1 application.
- I [transitioned to GitHub actions](https://github.com/waiting-for-dev/web_pipe/pull/51) for CI, as it provides a better experience these days.
- I also carried out some housekeeping tasks [related to gem files](https://github.com/waiting-for-dev/web_pipe/pull/52) and adhered to some [coding standards](https://github.com/waiting-for-dev/web_pipe/pull/53).

## What's next?

In November, I anticipate dedicating my efforts to gracefully managing the "unhappy" path within dry-operation. Additionally, I'll be exploring the optimal approach for integrating database transactions. I'm excited to make progress and continue moving forward!
